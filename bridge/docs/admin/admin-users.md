# Admin users

This page is for administrators who manage users, report access, table access, and running report containers through the Bridge web interface.

An admin user can use all normal report and dev-user screens, but the admin role is mainly for management tasks such as:
- changing a user's role to user, dev, admin, or suspend
- assigning or removing a user's access to reports
- assigning or removing a dev user's access to database tables
- suspending users
- deleting users
- viewing user resource usage
- stopping and removing running report containers

The main admin pages in the front end are:
- User Admin
- User Report Assignment
- Report Developers
- Reports by Developer
- User Table Assignment
- User Resource Usage
- Container Control

## User Admin

This is the main administration screen.

What you see on the page
- one row per user
- email
- last name
- first name
- current user security role
- date created
- date updated
- role-change controls
- report-assignment button
- table-assignment button
- suspend button
- delete button

### What each action does

Select New Role
- choose the new role from the dropdown in the user's row
- the available roles shown by the form are:
  - user
  - dev
  - admin
  - suspend

Change User Role
- applies the new role selected in the dropdown
- use this to promote a report consumer to dev, promote a dev to admin, or return a user to a lower role

Assign Reports
- opens the report-assignment workflow for that user
- use this when the user should be able to open reports built by a dev

Assign Tables
- opens the table-assignment workflow for that user
- use this mainly for dev users who need access to database tables to build table-based reports

Suspend
- changes the user's role to suspend
- use this when you want to block use of the system without fully deleting the account

Delete
- removes the user account
- use this only when you are sure the account should be removed

### How to change a user's role

1. Open User Admin.
2. Find the user.
3. In Select New Role, choose the target role.
4. Click Change User Role.
5. Confirm the row updates on reload.

Practical examples
- change a report consumer into a dev so they can use Model Maintenance
- change a dev into an admin so they can manage users and containers
- change a user to suspend when access should be temporarily blocked

### How to suspend a user

1. Open User Admin.
2. Find the user.
3. Click Suspend.
4. Reload the page and confirm the role now shows suspend.

### How to delete a user

1. Open User Admin.
2. Find the user.
3. Click Delete.
4. Confirm the user no longer appears in the list.

Use caution here because deletion is more final than suspension.

## User Report Assignment

This screen is used to see which reports a selected user can already access.

What the page shows
- the selected user's name, email, and role
- a table of reports currently assigned to that user
- report details including model name, report name, language, created/updated dates, and build time
- View Report link for each report
- Remove User link for each report
- Assign User to Report link to continue the assignment workflow

### How to review a user's current report access

1. Open User Admin.
2. Click Assign Reports for the target user.
3. Review the Reports Currently Assigned table.

Use this page to answer questions like:
- what reports can this user already open?
- should an old report assignment be removed?

### How to remove a user's report access

1. Open the user's User Report Assignment page.
2. Find the report.
3. Click Remove User.
4. Confirm the report disappears from the assigned list after reload.

### How to inspect an assigned report

1. On the same page, click View Report.
2. Review the output in the report viewer.

This is useful when checking that the user was assigned the correct report.

## Report Developers

This page is the next step in the report-assignment workflow.

Purpose
- show the developers whose reports can be considered for assignment to the selected user

What the page shows
- selected user's name, email, and role
- a list of developers
- View Dev Reports link for each developer

### How to use it

1. From User Report Assignment, click Assign User to Report.
2. On the Report Developers page, review the developer list.
3. Click View Dev Reports for the developer whose reports you want to inspect.

Use this page when you know the selected user needs access to reports created by a particular dev.

## Reports by Developer

This page shows the reports owned by the chosen developer.

What the page shows
- selected user's name, email, and role
- a list of the developer's reports
- report number
- model name
- report name
- report language
- created/updated dates
- build time
- View Report link
- Add User link

### How to assign a report to a user

1. Open User Admin.
2. Click Assign Reports for the target user.
3. Click Assign User to Report.
4. Choose a developer with View Dev Reports.
5. On Reports by Developer, find the report.
6. Click Add User.
7. You should be returned to the user's User Report Assignment page.
8. Confirm the report now appears under Reports Currently Assigned.

### How to verify before assigning

Use View Report before clicking Add User when:
- the report name is unclear
- there are multiple similar reports
- you want to confirm the user is getting the correct output

## User Table Assignment

This page is used mainly for dev users who need direct table access for building database-table reports.

What the page shows
- selected user's name, email, and role
- Tables Currently Assigned to User list
- remove privilege button for each currently assigned table
- a dropdown of available tables that can be assigned
- Assign Table button

### What this page is for

Use this page when a dev user needs permission to use the Build Report From Database Table workflow.

If a dev cannot build from a table, check here first.

### How to assign table access

1. Open User Admin.
2. Click Assign Tables for the target user.
3. On User Table Assignment, open the table dropdown.
4. Select the table to grant.
5. Click Assign Table.
6. Confirm the table appears in the list of currently assigned tables.

### How to remove table access

1. Open User Table Assignment for the user.
2. In the current table list, find the table.
3. Click Remove Table Privs.
4. Confirm the table no longer appears in the assigned list.

### Best use of table assignments

Use table assignment for users who:
- build database-driven reports
- need controlled access to specific datasets
- should not automatically see every table in the system

## User Resource Usage

This page shows system usage metrics by user.

What the page shows
- user ID
- CPU usage
- disk usage
- network usage

There is also a time-period selector in the interface, though the current app behavior may not always reflect every option dynamically.

### How to use this page

Use User Resource Usage to:
- spot unusually heavy users or report activity
- investigate resource complaints
- compare relative load across users

This page is more observational than action-based.

## Container Control

This page is used to monitor and kill running report containers.

What the page shows
- container name
- dev associated with the container
- uptime in hours:minutes
- size in GB
- kill button

### When to use Container Control

Use this page when:
- a report build appears stuck
- a container has been running longer than expected
- you need to clean up running build containers
- you are investigating a dev user's failed or hanging report build

### How to kill a container

1. Open Container Control.
2. Find the container by name or by dev.
3. Review uptime and size to make sure it is the one you intend to stop.
4. Click kill.
5. Confirm it disappears from the running list after refresh.

### Good admin practice for containers

Before killing a container:
- check whether the dev is still actively building a report
- review the uptime to see whether it is actually abnormal
- if possible, ask the dev whether they still need the run

Kill containers when they are clearly stuck, orphaned, or consuming resources unnecessarily.

## Admin use of Model Maintenance

Admins can also use Model Maintenance and Scheduled Runs because the admin role includes dev-level access.

In practice, admins usually go there for:
- checking whether a report exists
- opening a report to verify access or output
- reviewing build logs during troubleshooting
- confirming a schedule exists

But the pages above are the main admin-management screens.

## Recommended admin workflows

### Promote a report user to a dev

1. Open User Admin.
2. Find the user.
3. Choose dev in Select New Role.
4. Click Change User Role.
5. If needed, open Assign Tables and grant database-table access.

### Give a report user access to a dev's report

1. Open User Admin.
2. Click Assign Reports for the user.
3. Review current assignments.
4. Click Assign User to Report.
5. Choose the developer.
6. Click Add User on the target report.
7. Return to the assignment page and confirm it is listed.

### Remove access to a report

1. Open Assign Reports for the user.
2. Find the report in Reports Currently Assigned.
3. Click Remove User.
4. Confirm the report is removed.

### Give a dev access to build from a database table

1. Open User Admin.
2. Click Assign Tables for the dev.
3. Choose the table from the dropdown.
4. Click Assign Table.
5. Confirm it appears in the assigned table list.

### Handle a stuck report build

1. Open Container Control.
2. Look for the container belonging to the dev or report.
3. Review uptime and size.
4. Click kill if the container is clearly stuck.
5. Ask the dev to rerun the build if needed.
