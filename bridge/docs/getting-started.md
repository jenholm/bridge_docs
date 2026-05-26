# Getting started

This guide is a fast orientation for someone new to the Bridge application.

Start here by role

Role/security overview
- open `user/security-roles.md`
- use this if you need to understand the user, dev, admin, and suspended access levels

Report users
- open `user/report-users.md`
- use this if you mainly sign in, open Reports, and read finished output

Dev users
- open `user/dev-users.md`
- use this if you build reports through Model Maintenance, database-table workflows, or HTML upload

Admin users
- open `admin/admin-users.md`
- use this if you manage users, report access, table access, or containers
- keep `admin/common-admin-tasks.md` handy for a short task checklist

## What Bridge does

Bridge compiles reports from source code or prebuilt HTML, stores the resulting HTML in the database, and serves that HTML back to users through the web app.

Supported report inputs in the current codebase
- Python script: `.py`
- Jupyter notebook: `.ipynb`
- Quarto document: `.qmd`
- R script: `.R`
- R Markdown: `.Rmd`
- Precompiled HTML upload
- Browser-built SQL/table reports

## Main runtime pieces

- Flask app with four active route groups:
  - `/` and `/register` in `bridge/__init__.py`
  - `/admin/*` in `bridge/users/routes.py`
  - `/reports/*` in `bridge/reports/routes.py`
  - `/sql_report/*` in `bridge/reports/sql_routes.py`
  - `/utils/*` in `bridge/utils/routes.py`
- MySQL persistence through `bridge/db_helper.py` and SQL files under `bridge/SQL/`
- Docker-backed report execution for Python, notebook, Quarto, R, and R Markdown pipelines
- APScheduler plus queue thread for scheduled runs

## UI navigation by role

The global navbar is defined in `bridge/templates/base.html`.

For `user_role >= 0`
- Account
- Reports
- Logout

For `user_role >= 1` (developers)
- Model Maintenance
- Scheduled Runs

For `user_role == 2` (admins)
- User Admin
- User Resource Usage
- Container Control

## Typical user journeys

Report consumer
1. Open home page
2. Log in
3. Open Reports
4. Open an assigned report
5. Optionally export PDF

Report developer
1. Log in with developer role
2. Open Model Maintenance
3. Upload a script or HTML package
4. Wait for build logs to finish
5. Open generated report
6. Set schedule interval if needed

Administrator
1. Log in with admin role
2. Use User Admin to promote, suspend, or delete users
3. Grant report and table access
4. Inspect resource usage
5. Stop or remove orphaned report containers

## Important storage model

Bridge stores report output in the database, not just on disk.

The current pattern is:
- build artifacts are staged under the configured upload folder
- final HTML is read back into memory
- optional support files are archived and stored as binary data
- on view, HTML and support files are materialized back to disk under a temporary output directory and then served

That design explains why viewing a report is a reconstruction step, not just a static file read.

## Key operational assumptions in the code

- `config.ini` is expected at the project root by default, for example `/path/to/bridge/config.ini`
- database access uses the configured MySQL credentials loaded through `bridge/config_loader.py` and `bridge/dbconfig.py`
- report execution expects local Docker availability
- PDF conversion expects a working `wkhtmltopdf` binary configured via `wkhtml_to_pdf_path`
