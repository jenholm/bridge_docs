# Getting started

This guide is a fast orientation for someone new to the Bridge application.

Bridge is a Flask web application for building, scheduling, storing, and presenting reports. It supports several kinds of users, from people who only need to view finished reports to administrators who manage users, access, resources, and containers.

## Start by role

Choose the section that best matches what you need to do.

### Report users

Use this path if you mainly sign in, open **Reports**, and read finished output.

[Go to the report user guide](user/report-users.md)

### Dev users

Use this path if you build reports through **Model Maintenance**, database-table workflows, scheduled runs, or HTML upload.

[Go to the dev user guide](user/dev-users.md)

### Admin users

Use this path if you manage users, report access, table access, resource usage, or report containers.

[Go to the admin user guide](user/admin-users.md)

For frequent admin actions, use the quick checklist:

[Go to common admin tasks](admin/common-admin-tasks.md)

### Role and security overview

Use this path if you need to understand the difference between `user`, `dev`, `admin`, and `suspend` access levels.

[Go to the role/security overview](user/security-roles.md)

## What Bridge does

Bridge compiles reports from source code or prebuilt HTML, stores the resulting HTML in the database, and serves that HTML back to users through the web app.

Supported report inputs in the current codebase:

- Python script: `.py`
- Jupyter notebook: `.ipynb`
- Quarto document: `.qmd`
- R script: `.R`
- R Markdown: `.Rmd`
- Precompiled HTML upload
- Browser-built SQL/table reports

## Main runtime pieces

Bridge is made from several cooperating parts:

- Flask web application routes
- MySQL persistence
- Docker-backed report execution
- APScheduler-based scheduled runs
- A report queue for background execution
- Browser pages for users, developers, and administrators

The main route groups are:

- `/` and `/register`
- `/admin/*`
- `/reports/*`
- `/sql_report/*`
- `/utils/*`

## UI navigation by role

Bridge shows different navigation options depending on the signed-in user's role.

### Standard users

Users with `user_role >= 0` can see:

- Account
- Reports
- Logout

### Developers

Users with `user_role >= 1` can also see:

- Model Maintenance
- Scheduled Runs

### Administrators

Users with `user_role == 2` can also see:

- User Admin
- User Resource Usage
- Container Control

## Typical user journeys

### Report consumer

1. Open the Bridge home page.
2. Log in.
3. Open **Reports**.
4. Open an assigned report.
5. Optionally export the report to PDF.

### Report developer

1. Log in with a developer account.
2. Open **Model Maintenance**.
3. Upload a script, notebook, Quarto document, R Markdown file, or HTML package.
4. Wait for the build logs to finish.
5. Open the generated report.
6. Set a schedule interval if the report should run automatically.

### Administrator

1. Log in with an admin account.
2. Use **User Admin** to promote, suspend, or delete users.
3. Grant report and table access.
4. Inspect resource usage.
5. Stop or remove orphaned report containers when needed.

## Important storage model

Bridge stores report output in the database, not just on disk.

The current pattern is:

1. Build artifacts are staged under the configured upload folder.
2. Final HTML is read back into memory.
3. Optional support files are archived and stored as binary data.
4. When a user views a report, Bridge materializes the HTML and support files back to disk under a temporary output directory.
5. The browser loads the restored report through the report viewer.

That design explains why viewing a report is a reconstruction step, not just a static file read.

## Key operational assumptions

Bridge expects several external pieces to be available:

- `config.ini` should exist at the project root, for example `/path/to/bridge/config.ini`
- MySQL credentials should be configured through the Bridge config system
- Docker should be available for report execution
- `wkhtmltopdf` should be installed and configured if PDF export is enabled