# User guide

This section is for people who use Bridge through the web interface and do not need to know how the app is implemented.

Bridge has three active user security levels in the front end:

1. Report users
2. Dev users
3. Admin users

Each role sees a different set of pages and tools in the Bridge navigation bar.

## Choose your guide

### Role and security overview

Use this page if you need to understand the difference between `user`, `dev`, `admin`, and `suspend` access levels.

[Go to the role and security overview](security-roles.md)

### Report users

Use this page if you mainly log in, open **Reports**, and read finished output.

[Go to the report user guide](report-users.md)

### Dev users

Use this page if you build, save, schedule, and test reports through **Model Maintenance**.

[Go to the dev user guide](dev-users.md)

### Admin users

Use this page if you manage accounts, roles, report access, table access, resource usage, and containers.

[Go to the admin user guide](admin-users.md)

## Role overview

### Report users

Report users are usually:

- business users
- analysts
- report consumers
- people who mainly log in, open **Reports**, and read finished output

Report users normally see:

- Account
- Reports
- Logout

### Dev users

Dev users are report authors who use the Bridge web UI to build and manage reports.

Dev users normally see:

- Account
- Reports
- Model Maintenance
- Scheduled Runs
- Logout

Dev users can:

- upload report scripts
- build reports from database tables
- upload finished HTML reports
- schedule recurring report runs
- view their own finished reports

### Admin users

Admin users manage the operational side of Bridge.

Admin users can:

- manage user accounts
- change user roles
- assign report access
- assign table access
- review resource usage
- stop or remove report containers

Admin users normally see all dev-user navigation items plus:

- User Admin
- User Resource Usage
- Container Control

## Common tasks for all users

Most users follow the same basic starting flow:

1. Sign in from the Bridge home page.
2. Go to **Account** after login.
3. Use the navigation bar to open the pages available to your role.
4. Use **Logout** when finished.

## Navigation differences by role

Bridge changes the navigation bar based on the signed-in user's role.

| Role | Typical navigation |
|---|---|
| Report user | Account, Reports, Logout |
| Dev user | Account, Reports, Model Maintenance, Scheduled Runs, Logout |
| Admin user | Account, Reports, Model Maintenance, Scheduled Runs, User Admin, User Resource Usage, Container Control, Logout |

## Password reset

If you cannot sign in, use the password reset flow provided by the site.

The reset process usually works like this:

1. Open the password reset page.
2. Enter your email address.
3. Check your email for the reset link.
4. Open the link.
5. Create a new password.
6. Return to the home page and sign in again.
```
