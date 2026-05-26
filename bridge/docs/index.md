# Bridge documentation

Bridge is a Flask web application for building, scheduling, storing, and presenting reports.

This documentation is organized by how people use the app: report users, dev users, administrators, and developers who maintain the codebase.

## Start by role

### Report users

Use these pages if you mainly sign in, open **Reports**, and read finished output.

- [Role and security overview](user/security-roles.md)
- [Report user guide](user/report-users.md)

### Dev users

Use this page if you build reports through **Model Maintenance**, table-based report building, HTML uploads, or schedules.

- [Dev user guide](user/dev-users.md)

### Admin users

Use these pages if you install Bridge, manage users, assign access, review resources, or clean up containers.

- [Bridge installation guide](admin/install-bridge.md)
- [Admin user guide](user/admin-users.md)
- [Common admin tasks](admin/common-admin-tasks.md)

## Current implementation summary

Bridge currently supports these major workflows:

- Users register and log in through the home page and `/admin` auth routes.
- Developers upload report sources or precompiled HTML through **Model Maintenance**.
- Reports can be authored as `.py`, `.ipynb`, `.qmd`, `.R`, `.Rmd`, or as prebuilt HTML.
- SQL/table reports can be assembled interactively in the browser and then saved as regular reports.
- Generated report output is stored in MySQL, primarily as HTML plus optional archived support files.
- Reports are presented back to users in an iframe-based viewer and can also be converted to PDF.
- Scheduled report execution is handled by a background scheduler plus a queue runner.
- Admin users can manage users, report access, table access, containers, and user resource metrics.

## Documentation map

### Getting started

- [Getting started](getting-started.md)

### User guides

- [User guide home](user/index.md)
- [Role and security overview](user/security-roles.md)
- [Report user guide](user/report-users.md)
- [Dev user guide](user/dev-users.md)

### Admin guides

- [Admin guide home](admin/index.md)
- [Bridge installation guide](admin/install-bridge.md)
- [Admin user guide](admin/admin-users.md)
- [Common admin tasks](admin/common-admin-tasks.md)

### Developer guides

- [Developer guide home](developer/index.md)
- [Template reference](developer/template-reference.md)

### Reference

- [Configuration reference](reference/config-reference.md)

## What the app is for

Bridge is built around a report pipeline:

1. A report definition is created or uploaded.
2. The source script and optional support files are staged under the upload folder.
3. A Docker-backed execution pipeline runs the report.
4. The resulting HTML and support assets are saved to the database.
5. Authorized users open the saved report through the web UI.
6. Optionally, the HTML report is converted to PDF on demand.

## Primary roles in the current app

Bridge uses four effective role states:

| Role | Purpose |
|---|---|
| `user` | Can access assigned reports |
| `dev` | Can build and schedule reports |
| `admin` | Can manage users, permissions, containers, and resource reporting |
| `suspend` | Disabled role used to block access |

## Important code entry points

These files and modules are useful when maintaining or troubleshooting Bridge:

- Flask app factory: `bridge/__init__.py`
- Auth and admin routes: `bridge/users/routes.py`
- Report UI and execution routes: `bridge/reports/routes.py`
- SQL report builder routes: `bridge/reports/sql_routes.py`
- Utilities and PDF export: `bridge/utils/routes.py`
- Scheduling: `bridge/scheduler/`
- Report execution engines:
  - `bridge/make_py_report.py`
  - `bridge/make_r_report.py`