# Developer guide

This section documents how the current Bridge codebase is organized and how the major request and report flows work.

## High-level architecture

Bridge is a Flask application factory with raw-SQL persistence and Docker-backed report execution.

Core layers
- web layer: Flask routes and Jinja templates
- domain objects: lightweight `Report` and `User` models
- service layer: `DBUserManager`, `DBReportManager`, `DBSystemManager`
- persistence layer: `DBHelper` plus SQL files under `bridge/SQL/`
- execution layer: Python and R report generators plus Docker helpers
- scheduling layer: APScheduler plus a background queue thread

## Request-side app structure

App factory
- file: `bridge/__init__.py`
- function: `create_app()`

Blueprint registration
- `/admin` -> `bridge.users.routes.admin_bp`
- `/reports` -> `bridge.reports.routes.report_bp`
- `/sql_report` -> `bridge.reports.sql_routes.sql_report_bp`
- `/utils` -> `bridge.utils.routes.util_bp`

Global behaviors inside `create_app()`
- loads root config with `load_config()`
- configures Flask secret key and upload folder
- configures mail settings
- starts APScheduler
- starts queue thread on first request
- installs Flask-Login user loader
- captures request/response size for bandwidth accounting
- ensures each authenticated user has a recurring resource-capture job

## Important modules

### `bridge/users/routes.py`
Responsibilities
- login
- password reset
- account page
- admin user management
- report/table access management
- resource usage page

### `bridge/reports/routes.py`
Responsibilities
- reports listing
- restoring stored HTML reports to disk for viewing
- model maintenance UI
- upload/build entry points for scripted reports
- upload path for precompiled HTML
- schedule management UI

### `bridge/reports/sql_routes.py`
Responsibilities
- multi-step table/field/filter report builder
- saving SQL reports as regular Bridge models
- CSV/TSV export endpoints
- rebuilding saved SQL reports on demand

### `bridge/utils/routes.py`
Responsibilities
- PDF conversion through `wkhtmltopdf`
- container listing and cleanup

## Data and persistence design

### Config loading

`bridge/config_loader.py` searches in this order:
1. explicit `config_path` argument
2. `BRIDGE_CONFIG_PATH`
3. `INSTALL_MODE`
4. project root `config.ini`

For a standard checkout, the default canonical path is:
`/path/to/bridge/config.ini`

### Database access

`bridge/db_helper.py`
- loads SQL text from `bridge/SQL/`
- opens a PyMySQL connection using credentials exposed by `bridge/dbconfig.py`
- provides helper methods for model log writes and reads

The codebase depends on SQL files as a first-class part of the application design. When documenting or changing DB behavior, inspect both the Python service method and the matching SQL file.

### Session state

Bridge passes a serialized `Report` object through several workflows using `session['this_report']`.

This is central to:
- SQL report generation
- SQL report saving
- fail-report generation
- report view/export flows

The `Report` class in `bridge/reports/models.py` is intentionally a mutable transport object rather than an ORM model.

## Report execution architecture

### Shared lifecycle

Most report creation flows follow this sequence:
1. Create and populate `Report`
2. Insert model metadata in DB
3. Create working directory under upload folder
4. Save script/support files to disk
5. Launch language-specific build thread or scheduled runner
6. Collect generated HTML and support files
7. Save output back into the database
8. Show build logs and expose the resulting report in the UI

### Python, notebook, and Quarto

Primary files
- `bridge/report_gen.py`
- `bridge/make_py_report.py`
- `bridge/docker_manager.py`

Implementation notes
- `run_py_gen()` wraps `PyReportGenerator`
- Python, notebook, and Quarto runs all go through Docker-backed container operations
- the generator copies the report source and maker script into a container, executes there, retrieves output files, and persists HTML/support assets

### R and R Markdown

Primary file
- `bridge/make_r_report.py`

Implementation notes
- `run_docker_report()` stages the source file and support zip
- R and Rmd use container-side scripts referenced by config entries such as `r_docker_maker_path` and `r_docker_installer_script`
- post-processing retrieves generated files, archives support assets, and updates DB state

### Precompiled HTML

Path
- `/reports/model_maintenance/insert_html`

This skips computation and stores uploaded HTML plus optional support zip as a report.

### SQL reports

SQL reports are different:
- the browser builds the query through several steps
- `DBReportManager.execute_dbreport_query()` or `execute_saved_dbreport_query()` generates tabular output
- saving the SQL report turns it into a regular stored report model managed through Model Maintenance and Reports

## Scheduling architecture

There are two distinct background mechanisms.

1. Per-user resource capture jobs
- created by `add_user_job()` from `scheduler_manager.py`
- run every minute for authenticated users
- record CPU, disk, and bandwidth snapshots

2. Scheduled report execution queue
- started by `queue_manager.start_queue_thread()`
- loop implemented in `report_queue.run_queue()`
- due reports fetched from `DBReportManager.check_and_run_reports()`
- execution delegated to `schedule_runner.run_scheduled_reports()`

The queue uses resource gating with `psutil` and a `running_reports` set to avoid duplicate execution.

## Template system

The UI uses a small shared shell:
- `base.html` defines the navbar, assets, footer, and flash-message area
- most authenticated pages extend `base.html`

Role-sensitive navigation is template-driven, not centralized in a permission framework.

See `template-reference.md` for screen-level detail.

## Files and directories worth knowing

Top-level code areas in `bridge/`
- `templates/` - Jinja templates
- `static/` - CSS, JS, favicon
- `users/` - auth and admin routes/services
- `reports/` - report routes, services, models
- `scheduler/` - queue and scheduled execution
- `SQL/` - raw SQL queries
- `PY/` and `R/` - report helper scripts copied into containers
- `installer/` - setup assets and example reports
- `tests/` - route tests, service tests, execution tests, installer tests

## Known rough edges from current code review

- route definitions and template usage are functional but not always consistent
- some legacy/commented code remains in route files
- `dashboard.html` appears present but not on the main active route path
- many execution paths depend on config-derived Docker and filesystem paths
- session-carried `Report` objects make workflows convenient but tightly couple UI steps
