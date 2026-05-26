# User security roles

Bridge uses three active security levels in the web app:

- `user`
- `dev`
- `admin`

There is also a suspended state used to block access without deleting the account.

This page explains what each level can normally do in the front end and where the controls appear.

## Role values

Bridge uses role levels to decide what a signed-in person can see and do.

| Role name | Role value | Meaning |
|---|---:|---|
| `suspend` | -1 | Account is disabled in practice. The Account page shows suspended status. |
| `user` | 0 | Can sign in and open assigned reports. |
| `dev` | 1 | Can do user tasks and author/manage reports. |
| `admin` | 2 | Can do dev tasks and manage users, permissions, resources, and containers. |

Role access is cumulative:

- `user` is the standard report-viewing role.
- `dev` includes report-user access and adds report-building tools.
- `admin` includes dev-user access and adds user, permission, resource, and container management.
- `suspend` blocks normal access without deleting the account.

Use the lowest role that lets the person do their work.

## Navigation by role

Bridge changes the navigation bar based on the signed-in user's role.

### Report users

Users with role value `0` and above normally see:

- Account
- Reports
- Logout

### Dev users

Users with role value `1` and above normally see:

- Account
- Reports
- Model Maintenance
- Scheduled Runs
- Logout

### Admin users

Users with role value `2` normally see:

- Account
- Reports
- Model Maintenance
- Scheduled Runs
- User Admin
- User Resource Usage
- Container Control
- Logout

### Suspended users

Users with role value `-1` are suspended.

A suspended account:

- is shown as suspended on the Account page
- should not be used for normal work
- must be restored by an administrator before normal access resumes

## Report user level

Use the `user` role for people who only need to consume reports.

A report user can normally:

- sign in
- open the Account page
- open the Reports page
- view reports assigned to them
- open a report in the report viewer
- use PDF export when available
- log out

A report user normally cannot:

- build reports
- upload scripts or HTML reports
- manage report schedules
- assign reports to other users
- assign database-table access
- manage users
- stop report containers

For step-by-step instructions, see the [report user guide](report-users.md).

## Dev user level

Use the `dev` role for report authors.

A dev user can normally do everything a report user can do, plus:

- open Model Maintenance
- view the models/reports they can manage
- upload report source files
- choose a container image for report builds
- upload dependent files or support zip archives
- build reports from Python, notebook, Quarto, R, or R Markdown sources
- upload precompiled HTML reports
- build reports from assigned database tables
- save browser-built SQL/table reports
- delete reports from the Model Maintenance list
- open Scheduled Runs
- change schedule intervals for reports they manage
- inspect build logs during report creation

Database-table access is separate from the dev role.

A dev user may have the Model Maintenance page but still not see a table they need in the SQL/table report builder. In that case, an admin must grant the table on User Table Assignment.

For step-by-step instructions, see the [dev user guide](dev-users.md).

## Admin user level

Use the `admin` role for people who manage Bridge itself through the web UI.

An admin can normally do everything a dev user can do, plus:

- open User Admin
- promote or demote users between `user`, `dev`, `admin`, and `suspend`
- suspend users
- delete users
- assign reports to users
- remove report access from users
- assign database tables to users
- remove database-table permissions
- review User Resource Usage
- open Container Control
- stop or remove running report containers

For step-by-step instructions, see the [admin user guide](admin-users.md).

For shorter operational checklists, see [common admin tasks](../admin/common-admin-tasks.md).

## How report visibility works

The Reports page lists reports available to the current user.

For report users, this means assigned reports.

For dev users, this includes reports they can manage or view through the report manager queries.

For admins, use User Admin and the report-assignment workflow to inspect and change exactly which user can open which report.

Recommended admin verification after changing report access:

1. Open User Admin.
2. Click Assign Reports for the target user.
3. Confirm the report appears or disappears in Reports Currently Assigned.
4. If needed, use View Report to confirm it is the intended report.

## How table access works

Table access controls the SQL/table report builder.

A dev user needs two things to build from database tables:

1. A `dev` or `admin` role, so they can open Model Maintenance.
2. A table assignment, so the needed table appears in Select a Table.

If a dev says a table is missing:

1. Open User Admin.
2. Click Assign Tables for that dev.
3. Confirm whether the table is already listed under Tables Currently Assigned to User.
4. If not, choose the table from the dropdown and click Assign Table.
5. Ask the dev to reopen the Build Report From Database Table workflow.

## Choosing the right role

Use `user` when:

- the person only reads reports
- they should not build or schedule reports
- they should only see reports explicitly assigned to them

Use `dev` when:

- the person creates or tests reports
- they need Model Maintenance
- they need Scheduled Runs
- they may need assigned database-table access

Use `admin` when:

- the person manages other users
- the person grants report or table permissions
- the person investigates resource usage
- the person stops stuck report containers

Use `suspend` when:

- access should be temporarily blocked
- the account should remain available for later review or restoration

Prefer the lowest role that lets the person do their work.

## Common access problems

### A user can sign in but sees no reports

The user may not have any reports assigned.

Ask an admin to check User Report Assignment.

### A user cannot see Model Maintenance

The account is probably role `user` rather than `dev` or `admin`.

Ask an admin to change the role if the person should author reports.

### A dev cannot see a database table

The dev role alone is not enough for table-based reports.

Ask an admin to assign the table on User Table Assignment.

### A dev cannot change a schedule

Scheduled interval changes require a role greater than `user`.

Confirm the account is `dev` or `admin`.

### An admin page is missing from the navbar

Confirm the account role is `admin`.

The admin navbar entries only appear for role value `2`.

### An account shows suspended status

The role is set to `suspend`.

An admin must restore the account to `user`, `dev`, or `admin`.