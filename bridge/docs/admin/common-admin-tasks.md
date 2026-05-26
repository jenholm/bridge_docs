# Common admin tasks

This page is a quick-reference checklist for administrators who already know the screens and just need the shortest path to a task.

For full explanations of each screen, use:
- `admin-users.md`

## Promote a report user to a dev

1. Open User Admin.
2. Find the user.
3. In Select New Role, choose `dev`.
4. Click Change User Role.
5. If the user needs database-table report access, click Assign Tables and grant the needed tables.

## Promote a dev to an admin

1. Open User Admin.
2. Find the user.
3. Choose `admin` in Select New Role.
4. Click Change User Role.
5. Confirm the user now has access to User Admin, User Resource Usage, and Container Control.

## Suspend a user

1. Open User Admin.
2. Find the user.
3. Click Suspend.
4. Confirm the role is now suspend.

Use this when you want to block access without deleting the account.

## Delete a user

1. Open User Admin.
2. Find the user.
3. Click Delete.
4. Confirm the user disappears from the list.

Use this only when the account should be removed rather than temporarily disabled.

## Give a user access to a report

1. Open User Admin.
2. Click Assign Reports for the target user.
3. Review Reports Currently Assigned.
4. Click Assign User to Report.
5. Choose a developer with View Dev Reports.
6. Find the target report.
7. Optionally click View Report to verify it.
8. Click Add User.
9. Confirm the report now appears in the user's assigned list.

## Remove a user's access to a report

1. Open Assign Reports for the user.
2. Find the report in Reports Currently Assigned.
3. Click Remove User.
4. Confirm it disappears from the list.

## Give a dev access to database tables

1. Open User Admin.
2. Click Assign Tables for the dev.
3. Select a table from the dropdown.
4. Click Assign Table.
5. Confirm the table appears in Tables Currently Assigned to User.

Use this when the dev needs to use Build Report From Database Table.

## Remove a dev's table access

1. Open Assign Tables for the dev.
2. Find the table in the assigned list.
3. Click Remove Table Privs.
4. Confirm the table disappears from the list.

## Check what reports a user can currently open

1. Open User Admin.
2. Click Assign Reports for the user.
3. Review the Reports Currently Assigned table.
4. Use View Report if you need to confirm the content.

## Check which developer owns a report you may want to assign

1. Open User Admin.
2. Click Assign Reports for the user.
3. Click Assign User to Report.
4. Review the developer list.
5. Open View Dev Reports for the likely owner.

## Kill a stuck report container

1. Open Container Control.
2. Look for the container by name, uptime, size, or dev.
3. Confirm it is the correct container.
4. Click kill.
5. Refresh and confirm it is gone.

Use this when a build is clearly stuck or when an old container should have finished already.

## Check who is using the most resources

1. Open User Resource Usage.
2. Review CPU, disk, and network values by user.
3. Compare users to spot unusually heavy usage.

This page is useful for troubleshooting, but it does not itself change permissions or stop workloads.

## Fast role guide

Use `user`
- for report consumers who only need Reports

Use `dev`
- for report authors who need Model Maintenance and Scheduled Runs

Use `admin`
- for users who need to manage users, permissions, containers, and resource monitoring

Use `suspend`
- for users who should be blocked without being deleted
