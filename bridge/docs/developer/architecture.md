# Developer architecture detail

This page is a deeper technical map of the current Bridge implementation.

## Application startup

File: `bridge/__init__.py`

`create_app()` does the following:
- instantiates `Flask`
- loads `config.ini` through `load_config()`
- starts APScheduler immediately with `start_scheduler()`
- configures rotating file logging on first request
- starts the background report queue thread on first request
- loads mail, upload folder, secret key, and session settings from config
- registers the four blueprints
- configures Flask-Login user loading from the database

Two `before_request` handlers are significant:
- `launch_queue_once()` lazily starts logging and the queue thread
- `before_request()` enforces user existence, creates per-user resource jobs, and tracks request size

`after_request()` updates bandwidth usage and adds `X-User-Bandwidth-Usage`.

## Object model

### `Report`

File: `bridge/reports/models.py`

This is the central workflow object. It stores:
- model metadata
- report identifiers
- language and execution settings
- filesystem staging paths
- Docker paths
- support-asset metadata
- scheduling state
- build completion state

It also provides `to_dict()` / `from_dict()` for session transport.

### `User`

Used through `bridge/users/models.py` and Flask-Login. Authentication is email-based.

## How a report build works

### A. Script upload flow

Route: `reports.model_maintenance_createmodel`

1. WTForms object is created from `CreateModelForm`
2. Uploaded script is saved to `UPLOAD_FOLDER`
3. Optional support zip is saved and read into memory
4. `DBReportManager.add_model()` creates the DB-side model record
5. A report-specific temp directory is created under `upload_folder/in/temp_user<id>_report_<id>`
6. Execution is dispatched by file extension and selected language

Dispatch table from current code
- `.R` + `R` -> `run_docker_report(this_report, False)`
- `.Rmd` + `Rmd` -> `run_docker_report(this_report, True)`
- `.ipynb` + `ipynb` -> `run_py_gen(this_report)`
- `.qmd` + `qmd` -> `run_py_gen(this_report)`
- `.py` + `py` -> `run_py_gen(this_report)`

### B. Python-family execution

`report_gen.run_py_gen()`:
- constructs `PyReportGenerator`
- calls `setup_report()`
- calls `execute_report()`
- calls `finalize_report()`
- on exception, writes DB logs and may generate a fail report

`PyReportGenerator`:
- loads config and derives Docker/container paths
- stages script and support files
- runs container operations through `DockerManager`
- retrieves generated files from the container
- reads final HTML back as binary
- archives `_files` support directory when present
- updates the saved DB report

### C. R-family execution

`make_r_report.run_docker_report()`:
- loads config
- writes/stages report source and support files
- computes R-specific container paths
- starts a Docker container
- copies installer script, maker script, report source, and optional support zip into the container
- optionally unzips support files in the container
- runs R or Rmd build commands
- retrieves generated files
- archives support assets and persists the output

### D. Viewing a saved report

Route: `reports.get_and_extract_report`

1. Fetch report HTML and archived support assets from DB
2. Create a server-side output directory under `reports/out/<name>/`
3. Write HTML to disk
4. Extract support archive if present
5. Render `report.html` with iframe source pointing to `reports.serve_report_files`

Special cases
- `report_language == 'sql'` -> redirect into saved SQL report execution
- `report_language == 'fail'` -> redirect into fail-report generation

## SQL report builder internals

The SQL builder is stateful and session-driven.

State keys used in session
- `selected_table`
- `fields`
- `filters`
- `this_report`
- `report_data`
- `saved_report`

Key routes
- `select_table()`
- `select_fields()`
- `filter_fields()`
- `saved_sql_report()`
- `run_saved_sql_report()`
- `run_db_saved_report()`
- `generate_tsv()` / `generate_csv()` and non-saved variants

The builder is both a query UI and a report authoring path.

## Template architecture

### Shared shell

`base.html`
- Bootstrap-based page shell
- navbar with role-gated links
- flash message display
- static asset loading
- footer

### Screen categories

Anonymous/auth
- `home.html`
- `reset_password*.html`

User report consumption
- `reports.html`
- `report.html`

Developer report creation
- `model_maintenance.html`
- `report_schedule.html`
- `save_db_report.html`
- `database_report_shell.html`
- `saved_database_report_shell.html`

Admin management
- `user_admin.html`
- `user_report_ass.html`
- `userdev_report_ass.html`
- `user_table_ass.html`
- `report_devs.html`
- `user_resources.html`
- `container_control.html`

SQL builder
- `select_table.html`
- `select_fields.html`
- `filter_fields.html`
- `database_report.html`

## Logging and failure handling

Bridge records operational detail in two places:
- file logging at `bridge/logs/bridge.log`
- DB-backed model logs via `DBHelper.update_model_logs()`

Build failure path
- Python execution failures call `make_build_report_fail(...)` through `report_gen.py`
- there is a dedicated route `/reports/make_report_build_fail/user/<user_id>/model/<model_id>` that creates a SQL-style fail report against `model_logs`

## Recommended maintenance mindset

When changing this app, verify three layers together:
1. route/template behavior
2. service/SQL behavior
3. execution pipeline side effects on disk, Docker, and DB

A route can look correct while still failing later in the pipeline because Bridge's workflows are multi-stage and stateful.
