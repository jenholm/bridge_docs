# User guide

This section is for people who use Bridge through the web interface and do not need to know how the app is implemented.

Bridge has three active user security levels in the front end.

1. Report users
- business users
- analysts
- report consumers
- anyone who mainly logs in, opens Reports, and reads the finished output

2. Dev users
- report authors using the web UI
- users who build, save, schedule, and test reports through Model Maintenance
- users who may also view their own finished reports

3. Admin users
- users who manage accounts, roles, report access, table access, resources, and containers
- admins can also use the dev and report-user screens

Choose the page that matches how you use Bridge:
- `security-roles.md` - role and permission overview for user, dev, admin, and suspended accounts
- `report-users.md` - simple instructions for users who only need to open and read reports
- `dev-users.md` - detailed front-end instructions for users who build and manage reports
- `../admin/admin-users.md` - detailed front-end instructions for administrators

Common tasks for all users
- Sign in from the home page
- Go to Account after login
- Use the navigation bar to open the pages available to your role
- Use Logout when finished

Navigation differences by role
- report users normally see: Account, Reports, Logout
- dev users normally see: Account, Reports, Model Maintenance, Scheduled Runs, Logout
- admin users normally see all dev-user items plus User Admin, User Resource Usage, and Container Control

Password reset
- If you cannot sign in, use the password reset link/flow provided by the site.
- The reset screens ask for your email address, then let you create a new password from the link sent to you.
