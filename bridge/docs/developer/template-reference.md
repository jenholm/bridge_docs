# Template reference

This reference maps the Jinja templates in `bridge/templates/` to the current app behavior.

## Global layout

### `base.html`
Purpose
- shared authenticated layout
- navbar, assets, flash messages, footer

Navigation rules
- role >= 0: Account, Reports
- role >= 1: Model Maintenance, Scheduled Runs
- role == 2: User Admin, User Resource Usage, Container Control

## Anonymous/authentication screens

### `home.html`
Route sources
- `/` for GET
- `/register` on POST for registration
- `/admin/login` on POST for login

Functions
- sign in
- create account
- show validation errors and registration success message

### `reset_password_request.html`
Route
- `/admin/reset_password_request`

Function
- collect email for reset-link delivery

### `reset_password_response.html`
Route
- `/admin/reset_password_response`

Function
- confirmation page after request submission

### `reset_password.html`
Route
- `/admin/reset_password/<token>`

Function
- submit new password once token is validated

### `reset_password_confirm.html`
Shown after successful password change.

## Core authenticated screens

### `account.html`
Route
- `/admin/account`

Function
- simple landing page after login
- serves mostly as a navigation hub

### `reports.html`
Route
- `/reports/`

Function
- list reports the current user can access
- links each item to the report viewer

### `report.html`
Route source
- rendered by `/reports/get_report/user/<user_id>/report/<report_id>`

Function
- display restored HTML report in iframe
- server-side PDF export button
- client-side PDF generation attempt

## Developer/report-authoring screens

### `model_maintenance.html`
Route
- `/reports/model_maintenance`

Functions
- list existing models/reports
- upload scripted report source
- upload precompiled HTML report
- launch SQL report builder
- display ongoing build logs
- auto-refresh build page every 10 seconds
- preserve non-file form fields in local storage

### `report_schedule.html`
Route
- `/reports/report_schedule`

Function
- list scheduled reports
- update per-report interval
- show last scheduled run information

### `save_db_report.html`
Route source
- rendered from `/sql_report/save_report` and `/sql_report/save_sqlreport`

Function
- convert an ad hoc SQL/table report into a saved Bridge model

## SQL report builder screens

### `select_table.html`
Route
- `/sql_report/select_table`

Function
- choose a table the user is permitted to query

### `select_fields.html`
Route
- `/sql_report/select_fields`

Function
- choose fields/columns for the report

### `filter_fields.html`
Route
- `/sql_report/filter_fields`

Function
- collect filter operator/value criteria for the chosen fields

### `database_report.html`
Route sources
- `/sql_report/sql_report`
- `/sql_report/build_fail_report`
- `/sql_report/saved_sql_report`
- `/sql_report/database_report`

Function
- render generated table data directly in HTML

### `database_report_shell.html`
Route source
- rendered after ad hoc SQL report generation

Function
- wrapper page with actions to:
  - render the live SQL result
  - export CSV/TSV
  - save the result as a Bridge report

### `saved_database_report_shell.html`
Route source
- rendered by saved SQL report run routes

Function
- wrapper for already-saved SQL reports with CSV/TSV export links

## Admin screens

### `user_admin.html`
Route
- `/admin/user_admin`

Functions
- list users
- change role
- open report assignment
- open table assignment
- suspend user
- delete user

### `report_devs.html`
Route
- `/admin/get_devs/<user_id>`

Function
- list developers relevant to report assignment for a target user

### `user_report_ass.html`
Route
- `/admin/user_report_ass/<user_id>`

Function
- show reports already assigned to the selected user
- link into developer-specific report selection

### `userdev_report_ass.html`
Route
- `/admin/get_dev_reports/user/<user_id>/dev/<dev_id>`

Function
- show a specific developer's reports so the admin can grant access to the selected user

### `user_table_ass.html`
Route
- `/admin/user_table_ass/<user_id>`

Function
- show current table permissions and allow assignment/removal

### `user_resources.html`
Route
- `/admin/user_resources`

Function
- display user resource metrics collected by the system

### `container_control.html`
Route
- `/utils/get_containers`

Function
- list running containers
- show size, uptime, and inferred developer
- allow stop/remove from the UI

## Templates present but apparently less central

### `dashboard.html`
This template exists and displays a dashboard-style model/report table, but it does not appear to be the primary current route target in the active request flow.

If future cleanup is done, verify whether it is legacy, partially replaced by `reports.html`, or intended for restoration.
