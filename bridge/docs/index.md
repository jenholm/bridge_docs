# Bridge documentation

Bridge is a Flask web application for building, scheduling, storing, and presenting reports.

Start by role

Report users
- `user/security-roles.md` - role and permission overview for user, dev, admin, and suspended accounts
- `user/report-users.md` - simple instructions for people who log in, open Reports, and read finished output

Dev users
- `user/dev-users.md` - detailed guide for report authors using Model Maintenance, table-based report building, HTML uploads, and schedules

Admin users
- `admin/install-bridge.md` - server installation, configuration, verification, and installer troubleshooting
- `admin/admin-users.md` - detailed guide for administrators handling users, permissions, table access, resource review, and containers
- `admin/common-admin-tasks.md` - quick-reference checklist for frequent admin tasks

Current implementation summary
- Users register and log in through the home page and `/admin` auth routes.
- Developers upload report sources or precompiled HTML through Model Maintenance.
- Reports can be authored as `.py`, `.ipynb`, `.qmd`, `.R`, `.Rmd`, or as prebuilt HTML.
- SQL/table reports can be assembled interactively in the browser and then saved as regular reports.
- Generated report output is stored in MySQL, primarily as HTML plus optional archived support files.
- Reports are presented back to users in an iframe-based viewer and can also be converted to PDF.
- Scheduled report execution is handled by a background scheduler plus a queue runner.
- Admin users can manage users, report access, table access, containers, and user resource metrics.

Documentation map
- `getting-started.md` - what Bridge does and how the UI is organized
- `user/index.md` - user-guide landing page
- `user/security-roles.md` - role and permission overview for the front-end security levels
- `user/report-users.md` - simple instructions for report consumers
- `user/dev-users.md` - detailed front-end guide for report authors using the web UI
- `admin/index.md` - administration and operations
- `admin/install-bridge.md` - server installation and troubleshooting
- `admin/admin-users.md` - detailed front-end guide for admin users
- `admin/common-admin-tasks.md` - quick-reference checklist for common admin actions
- `developer/index.md` - architecture and maintenance
- `developer/template-reference.md` - template-by-template UI reference from `bridge/templates/`
- `reference/config-reference.md` - important `config.ini` settings

What the app is for
Bridge is built around a report pipeline:
1. A report definition is created or uploaded.
2. The source script and optional support files are staged under the upload folder.
3. A Docker-backed execution pipeline runs the report.
4. The resulting HTML and support assets are saved to the database.
5. Authorized users open the saved report through the web UI.
6. Optionally, the HTML report is converted to PDF on demand.

Primary roles in the current app
- user: can access assigned reports
- dev: can build and schedule reports
- admin: can manage users, permissions, containers, and resource reporting
- suspend: disabled role used to block access

Important code entry points
- Flask app factory: `bridge/__init__.py`
- Auth and admin: `bridge/users/routes.py`
- Report UI and execution: `bridge/reports/routes.py`
- SQL report builder: `bridge/reports/sql_routes.py`
- Utilities and PDF export: `bridge/utils/routes.py`
- Scheduling: `bridge/scheduler/`
- Report execution engines: `bridge/make_py_report.py`, `bridge/make_r_report.py`
