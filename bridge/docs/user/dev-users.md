# Dev users

This page is for report authors who use the Bridge web UI to build, save, run, schedule, and review reports.

A dev user can:
- create new reports from uploaded scripts
- build reports from database tables
- upload finished HTML reports
- view finished reports
- delete reports
- set or change report schedule intervals
- watch build logs while a report is being created

## Quick start

If you already know the basics, use this short version.

To build a script-based report
1. Open Model Maintenance.
2. Fill in Add Model.
3. Upload the script.
4. Choose the container image.
5. Optionally add support files and a schedule interval.
6. Click Create.
7. Watch the build log.
8. Open View Report when the report appears in the Models list.

To build a database-table report
1. Open Model Maintenance.
2. Click Build Report From Database Table.
3. Select the table.
4. Select fields.
5. Apply filters.
6. Generate the report.
7. Save it if you want to keep it.

To upload finished HTML
1. Open Model Maintenance.
2. Use Add Precompiled HTML Report.
3. Upload the HTML file.
4. Optionally upload a support zip.
5. Click Upload Report.

## Main pages used by dev users

You will mainly use:
- Reports
- Model Maintenance
- Scheduled Runs

## Reports page

Use Reports when you want to open finished output.

What you do there
- find a report by name or model name
- click View Report
- inspect the finished result in the browser
- use Get PDF when you want a PDF copy

This page is the simplest way to confirm that a build succeeded and that users will see the expected output.

## Model Maintenance overview

Model Maintenance is the main work area for building reports.

On this page you will see four main sections:
1. Models list
2. Add Model
3. Build Report From Database Table
4. Add Precompiled HTML Report

You may also see a Report Build Log section while a build is running or after a build finishes.

## Models list

The Models table shows reports you have created or can manage.

The table includes:
- model number
- model name
- report name
- report language
- date created
- date updated
- build time in seconds
- View Report link
- Delete link

Use this section to:
- confirm that a report was created
- open the finished output with View Report
- remove a report with Delete

A good habit is to refresh this list after every build and verify that the new report appears with the expected name and language.

## Option 1: Build a report from an uploaded script

Use the Add Model form when you want Bridge to compile a report from a source file.

### Fields in the Add Model form

Model Name
- Enter a clear name for the report or model.
- This is the name you and other users will use to identify it later.

Script Type (file extension)
- Choose the type that matches your uploaded file.
- The form currently supports:
  - `R`
  - `py`
  - `ipynb`
  - `Rmd`
  - `qmd`

R Plot or Table (.R files only)
- This option matters when the uploaded script is an `.R` file.
- Choose Plot if the report is mainly a plotted or visual result.
- Choose Table if the output is meant to be tabular.
- For non-`.R` report types, this field can remain present in the form but is mainly relevant to `.R` uploads.

Upload Script File
- Choose the file that Bridge should run.
- Make sure the selected script type matches the file you upload.

Choose Container Image
- Pick the runtime image that best matches the report's dependencies.
- Use the image that contains the packages or environment your report needs.
- If you are unsure, use the image recommended by your team for that report type.

Upload Dependent File or .zip Archive
- Optional.
- Use this when your report needs extra files such as:
  - data files
  - images
  - helper assets
  - other inputs required at runtime
- If you have multiple support files, package them into a zip file and upload that zip.

Schedule Interval (seconds)
- Optional if you want the report to run again on a schedule.
- Enter the interval in seconds.
- Leave it blank if the report should be created without a recurring schedule.

### Standard scripted-report workflow

1. Open Model Maintenance.
2. In Add Model, enter the Model Name.
3. Choose the Script Type.
4. If the script is an `.R` file, choose Plot or Table.
5. Upload the script file.
6. Choose the container image.
7. Optionally upload a dependent file or zip archive.
8. Optionally enter a schedule interval in seconds.
9. Click Create.

### What to expect after clicking Create

- The page refreshes automatically while the build is in progress.
- The form tries to preserve typed values during refreshes.
- A Report Build Log may appear on the same page.
- When the build finishes, the new report should appear in the Models list.
- You can click View Report to inspect the result.

### Worked example: Python script report

Use this when you have a `.py` script that produces an HTML report.

Example front-end flow
1. Open Model Maintenance.
2. Set Model Name to something like `Weekly Sales Summary`.
3. Set Script Type to `py`.
4. Leave the R Plot or Table option alone because it is not relevant.
5. Upload your Python report script.
6. Choose the Python container image your team normally uses.
7. If the script needs a CSV input or image assets, upload them as a zip archive.
8. Leave Schedule Interval blank for a one-time test build.
9. Click Create.
10. Watch the Report Build Log.
11. When the report appears in Models, click View Report.
12. If it looks right, add a schedule later from Scheduled Runs.

### Worked example: Jupyter notebook report

Use this when your report starts as a notebook.

Example front-end flow
1. Open Model Maintenance.
2. Enter a model name such as `Notebook KPI Report`.
3. Set Script Type to `ipynb`.
4. Upload the `.ipynb` notebook.
5. Choose the image that contains the notebook dependencies.
6. Upload a zip if the notebook needs local data files.
7. Click Create.
8. Wait for the report to build.
9. Open View Report and confirm the notebook rendered cleanly as a report.

Best use
- exploratory analyses turned into reusable reports
- notebooks already used by analysts that now need scheduled or repeatable output

### Worked example: Quarto report

Use this when you are authoring a `.qmd` report.

Example front-end flow
1. Open Model Maintenance.
2. Name the report, for example `Quarterly Operations Review`.
3. Set Script Type to `qmd`.
4. Upload the `.qmd` file.
5. Choose the Quarto-capable container image.
6. Upload a zip if the report depends on images, data files, or additional resources.
7. Click Create.
8. Review the log.
9. Open the finished report and verify layout, charts, and links.

Best use
- polished narrative reports
- combined text, code, charts, and tables

### Worked example: R script report

Use this when you are uploading a plain `.R` file.

Example front-end flow
1. Open Model Maintenance.
2. Enter a model name such as `Daily Production Plot`.
3. Set Script Type to `R`.
4. Decide whether the output is a Plot or a Table.
5. Upload the `.R` file.
6. Choose the R container image that matches the packages the script needs.
7. Upload a zip if the script depends on supporting files.
8. Click Create.
9. Watch the log for package or file issues.
10. Open View Report when complete.

When to choose Plot
- charts
- graphics
- visual summaries

When to choose Table
- tabular output
- data listings
- report pages centered on tables rather than figures

### Worked example: R Markdown report

Use this when the report is an `.Rmd` document.

Example front-end flow
1. Open Model Maintenance.
2. Enter the model name.
3. Set Script Type to `Rmd`.
4. Upload the `.Rmd` file.
5. Choose the R image that supports the required libraries.
6. Upload support files as a zip if the report needs them.
7. Click Create.
8. Review the build log.
9. Open the completed report and verify text, charts, and tables.

Best use
- parameterized or narrative R reports
- formatted analytical documents with mixed text and output

### Choosing the right script type

Use `py`
- when your report is a Python script that directly produces the final report output

Use `ipynb`
- when your report starts as a Jupyter notebook

Use `qmd`
- when your report is a Quarto document

Use `R`
- when your report is an R script and you want Bridge to compile it

Use `Rmd`
- when your report is an R Markdown document

## Option 2: Build a report from a database table

Use the Build Report From Database Table link when you want to create a report without uploading a code file.

This starts a guided front-end workflow.

### Step 1: Select a table

- Open Model Maintenance.
- Click Build Report From Database Table.
- Choose one table from the available list.
- Click Next.

Think of this step as choosing the main data source.

Worked example
- If you need a user activity report, choose the table that contains the user activity records.
- If you need a report on orders, choose the orders table rather than a reference table.

### Step 2: Select fields

- Check the fields you want in the report.
- You can use Select All if needed.
- Click Next.

Best practice
- start with only the fields users actually need
- avoid selecting every field unless you are doing exploration or testing

Worked example
For a customer-order report, you might choose fields like:
- customer name
- order date
- order amount
- order status

### Step 3: Apply filters

For each selected field, you can define a filter.

The filter page gives you:
- a field name
- a starting value or range selector when values are available
- an operator selector such as `>`, `>=`, `<`, `<=`, or `=`
- a value entry box

After choosing your filters:
- click Generate Report

Worked example
If you only want recent records:
- choose a date field
- set an operator such as `>=`
- enter the cutoff value you want

If you only want one status:
- choose the status field
- use `=`
- enter the target value

### Step 4: Review the generated table report

After generation, Bridge opens a Database Table Report page.

From that page you can typically:
- view the table report in the browser
- generate a PDF from the table
- generate a tab-delimited file
- generate a comma-delimited file
- save the table report as a normal report

At this stage, decide whether the result is:
- just a one-time ad hoc report
- something worth saving and reusing

### Step 5: Save the database report

If you want to keep the table report for later use:
1. Click Save Report.
2. Enter a Report Name.
3. Optionally enter a Report Refresh Interval in seconds.
4. Click Create.

After saving, the report becomes part of your normal Models list and can be opened from Reports or Model Maintenance.

Worked example
1. Build a filtered sales table.
2. Review the output.
3. Click Save Report.
4. Name it `Monthly Regional Sales Table`.
5. Add a schedule interval if you want it refreshed automatically.
6. Save it and verify it appears in Models.

## Option 3: Upload a precompiled HTML report

Use Add Precompiled HTML Report when you already have a finished HTML report and want Bridge to host it.

### Fields in the HTML upload form

Model Name
- Enter the name the report should have in Bridge.

Upload HTML File
- Select the finished HTML file.

Upload .zip Archive of HTML Support Files
- Optional.
- Use this when the HTML page depends on extra files such as:
  - images
  - JavaScript files
  - CSS files
  - exported support folders

### How to upload a precompiled HTML report

1. Open Model Maintenance.
2. Go to Add Precompiled HTML Report.
3. Enter the Model Name.
4. Upload the HTML file.
5. If the HTML needs companion files, upload them as a zip archive.
6. Click Upload Report.

After upload, the report should appear in the Models list. Use View Report to confirm it renders correctly.

### Worked example: publish a finished HTML report

Use this when the report was produced outside Bridge but you still want Bridge to serve it.

Example front-end flow
1. Open Model Maintenance.
2. In Add Precompiled HTML Report, set Model Name to something like `External KPI Dashboard`.
3. Upload the main HTML file.
4. If the report came with a folder of CSS, JS, or images, zip that support folder and upload the zip.
5. Click Upload Report.
6. Wait for the new entry to appear in Models.
7. Click View Report and confirm the page renders with correct styling and assets.

## Using the Report Build Log

When Bridge is building a report, the build log area can appear on Model Maintenance.

Use it to:
- monitor progress
- look for missing dependencies or file problems
- copy the full log with the Copy Log button

Practical tips
- Wait for the page to refresh and the report to appear in the Models list.
- If a report does not complete, copy the log and share it with your administrator or support contact.
- If the report appears but looks wrong, still open it first; some issues are presentation problems rather than full build failures.

## Viewing your finished reports

Dev users can also use the Reports page like regular report users.

To open a report:
1. Click Reports.
2. Find the report row.
3. Click View Report.
4. Use Get PDF if you need a PDF copy.

Use this after every important build so you verify the actual user-facing output, not just the build log.

## Managing schedules

Use Scheduled Runs to review or change recurring report timing.

The page shows:
- report ID
- model ID
- user ID
- model name
- report name
- current schedule interval in seconds
- last scheduled run
- last run status

### How to change a schedule

1. Open Scheduled Runs.
2. Find the report you want.
3. Enter the new interval in the Select new Interval field.
4. Click New Schedule Interval.

Use this page to:
- confirm a schedule is set
- increase or decrease how often a report runs
- check whether the last scheduled run succeeded or failed

Worked example
- After testing a report manually, open Scheduled Runs.
- Find the report.
- Enter the desired refresh interval.
- Save it.
- Return later to confirm that Last Scheduled Run and Run Status are updating as expected.

## Recommended dev-user workflows

### New scripted report

1. Open Model Maintenance.
2. Fill out Add Model.
3. Click Create.
4. Watch the build log.
5. Open View Report from the Models list.
6. If needed, set or adjust the schedule in Scheduled Runs.

### New database-backed report

1. Open Model Maintenance.
2. Click Build Report From Database Table.
3. Select table, fields, and filters.
4. Generate and review the table report.
5. Save the report.
6. Open it from the Models list or Reports page.
7. Add a schedule if needed.

### Finished HTML report produced elsewhere

1. Open Model Maintenance.
2. Use Add Precompiled HTML Report.
3. Upload the HTML file and optional support zip.
4. Confirm it appears in Models.
5. Open View Report to verify it renders correctly.

## Common mistakes to avoid

- choosing a script type that does not match the uploaded file
- forgetting support files when the report depends on local assets
- using the wrong container image for the report's dependencies
- saving a database-table report without first reviewing the generated output
- assuming the build succeeded without opening View Report
- setting a schedule before verifying the one-time build result
