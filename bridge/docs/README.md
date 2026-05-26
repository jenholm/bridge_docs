# Bridge documentation

This documentation set was created from the implementation plan in `../bridge/imp_plans/bridge_documentation_plan.md` and from the current Flask application code under `../bridge/`.

Audience-first starting points
- role/security overview: `user/security-roles.md`
- report users: `user/report-users.md`
- dev users: `user/dev-users.md`
- install/admin setup: `admin/install-bridge.md`
- admin users: `admin/admin-users.md`
- common admin checklist: `admin/common-admin-tasks.md`

It focuses on the app as it exists today:
- Flask application factory in `bridge/__init__.py`
- blueprints in `bridge/users`, `bridge/reports`, `bridge/reports/sql_routes`, and `bridge/utils`
- Jinja templates in `bridge/templates/`
- report execution and scheduling code in `bridge/make_py_report.py`, `bridge/make_r_report.py`, and `bridge/scheduler/`
- raw SQL-backed persistence in `bridge/SQL/` through `bridge/db_helper.py`

Start here:
- `index.md` - documentation home
- `getting-started.md` - quick orientation
- `user/index.md` - user-guide landing page
- `user/security-roles.md` - role and permission overview for user, dev, admin, and suspended accounts
- `user/report-users.md` - simple instructions for report consumers
- `user/dev-users.md` - detailed front-end guide for report authors using the web UI
- `admin/install-bridge.md` - server installation, configuration, verification, and installer troubleshooting
- `admin/index.md` - administration and operations
- `admin/admin-users.md` - detailed front-end guide for admin users
- `admin/common-admin-tasks.md` - quick-reference checklist for common admin actions
- `developer/index.md` - architecture and maintenance
- `reference/config-reference.md` - configuration keys and meanings

Notes
- The docs describe the current code paths, even where the implementation is rough or inconsistent.
- Some templates exist but do not appear to be part of the main active route flow, such as `dashboard.html`.
- Configuration examples intentionally describe keys without repeating live secret values from the local `config.ini`.
