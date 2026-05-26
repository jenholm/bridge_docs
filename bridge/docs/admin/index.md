# Administrator guide

This section covers Bridge administration.

If you want step-by-step front-end instructions for daily admin work, start with:
- `install-bridge.md` - server installation, configuration, verification, and installer troubleshooting
- `admin-users.md` - user administration, report assignment, table assignment, resource review, and container control
- `common-admin-tasks.md` - quick-reference checklist for frequent admin actions

The rest of this page describes the operational and architectural admin-side behavior visible in the current codebase.

## Security roles

Roles are managed in `bridge/users/services.py` and surfaced in `base.html`.

Current effective roles
- `user` -> integer 0
- `dev` -> integer 1
- `admin` -> integer 2
- `suspend` -> integer -1

Observed permissions in the UI
- `user_role >= 0`: can access Reports
- `user_role >= 1`: can access Model Maintenance and Scheduled Runs
- `user_role == 2`: can access User Admin, User Resource Usage, and Container Control

## User administration

Route: `/admin/user_admin`
Template: `user_admin.html`

Admin features
- list all users
- change role
- open report-assignment page
- open table-assignment page
- suspend user
- delete user

Supporting routes
- `/admin/change_role/<user_id>`
- `/admin/user_report_ass/<user_id>`
- `/admin/get_devs/<user_id>`
- `/admin/get_dev_reports/user/<user_id>/dev/<dev_id>`
- `/admin/add_report_access/user/<user_id>/report/<report_id>`
- `/admin/remove_report_access/user/<user_id>/report/<report_id>`
- `/admin/user_table_ass/<user_id>`
- `/admin/table_ass`
- `/admin/remove_user_table_read/user/<user_id>/table/<table>`
- `/admin/suspend_user/<user_id>`
- `/admin/delete_user/<user_id>`

### Report assignment

Templates
- `report_devs.html`
- `user_report_ass.html`
- `userdev_report_ass.html`

This workflow allows an admin to:
- inspect developers related to a user
- inspect reports owned by a developer
- grant or revoke a specific user's access to reports

### Table assignment

Template: `user_table_ass.html`

This page controls which database tables a user can read in the SQL report builder.

## Resource usage page

Route: `/admin/user_resources`
Template: `user_resources.html`

This page shows aggregate usage data returned by `DBSystemManager.get_user_resources()`.

The app also captures resource and bandwidth data during normal request handling:
- request size stored in `g.request_size` during `before_request`
- response size added in `after_request`
- per-user bandwidth accumulated in `app.user_bandwidth_usage`
- periodic sampling job created per logged-in user via APScheduler

## Container control

Route: `/utils/get_containers`
Template: `container_control.html`

Admin-only operational features
- list running Docker containers
- calculate writable layer size using Docker low-level API
- infer owner from names matching `user(\d+)model`
- stop/remove a container from the UI

Supporting route
- `/utils/stop_remove_container/<container_name>`

Why this page matters
- report execution is Docker-backed
- failed or stuck jobs can leave containers behind
- Bridge exposes a minimal admin UI for container cleanup

## Scheduling and queue operation

Startup flow
- `create_app()` calls `start_scheduler()` immediately
- on first request, `launch_queue_once()` configures logging and starts the queue thread

Scheduler pieces
- `bridge/scheduler/scheduler_manager.py` manages per-user APScheduler jobs used for resource capture
- `bridge/scheduler/report_queue.py` manages a background priority queue for scheduled report execution
- `bridge/scheduler/schedule_runner.py` dispatches execution by report language

Queue behavior
- checks for reports due to run using `DBReportManager.check_and_run_reports()`
- skips already-running reports using `running_reports`
- only starts work if CPU and memory are below configured thresholds in code (75% each)
- starts scheduled runs in daemon threads

Operational implication
- scheduled work depends on the web app process being alive long enough to start the queue thread
- if the process never receives traffic after restart, queue thread startup may be delayed

## Logging

Current logging location
- `bridge/logs/bridge.log`

Configured in `create_app()`
- rotating file handler
- stream handler
- startup message: `Bridge startup`

Build and runtime diagnostics are also written to database-backed model logs through `DBHelper.update_model_logs()`.

## Report storage model

The primary persistence pattern is database storage.

Bridge stores
- report HTML content
- archived support files
- support zip uploads
- model metadata
- run metadata and logs

When an end user opens a report, Bridge recreates files on disk under `reports/out/...` so the browser can load HTML plus dependent assets.

## Operational cautions observed in code

- `config.ini` contains many sensitive settings and should be treated as secret material.
- The app sets `SESSION_COOKIE_SECURE = True`, which is correct for HTTPS but can interfere with plain HTTP local access.
- Some routes rely heavily on session state and may fail oddly if the session is cleared mid-workflow.
- Several workflows depend on config-driven filesystem and Docker paths; test scheduled execution carefully after config changes.
