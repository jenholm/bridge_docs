# Configuration reference

Bridge loads configuration through `bridge/config_loader.py`.

Lookup order
1. explicit path passed to `load_config()`
2. `BRIDGE_CONFIG_PATH`
3. `INSTALL_MODE`
4. project root `config.ini`

For a standard checkout, the default file is:
`/path/to/bridge/config.ini`

Do not commit or publish real secret values from this file.

## Core application settings

- `app_key`
  - Flask secret key
- `app_host`
  - host/IP bind for the web server
- `app_port`
  - web server port
- `upload_folder`
  - root staging directory for uploaded scripts, HTML, and temp per-report workspaces
- `wkhtml_to_pdf_path`
  - absolute path to `wkhtmltopdf`

## Database settings

- `db_user`
- `db_password`
- `mysql_root_password`
- `mysql_user`
- `mysql_password`
- `mysql_host`
- `schema_file`

How they are used
- `db_user` and `db_password` are surfaced through `bridge/dbconfig.py`
- `DBHelper.connect()` uses those values to open the main application database connection

## Mail and password reset

- `mail_server`
- `mail_port`
- `mail_use_tls`
- `mail_username`
- `mail_password`
- `security_password_salt`

How they are used
- password reset emails are sent from `bridge/users/routes.py`
- token generation/validation relies on `app_key` plus `security_password_salt`

## Python report execution settings

- `py_installer_script`
- `py_docker_image`
- `py_docker_maker_path`
- `py_host_maker_path`
- `py_docker_lib_path`
- `py_torch_docker_image`
- `quarto_docker_image`
- `docker_user_path`

How they are used
- `PyReportGenerator` derives container paths from these settings
- Python, notebook, and Quarto flows all depend on `docker_user_path`

## R report execution settings

- `r_installer_script`
- `r_docker_installer_script`
- `r_docker_maker_path`
- `r_host_maker_path`
- `r_report_library`
- `r_docker_image`
- `r_docker_gis_image`
- `r_docker_lib_path`
- `docker_user_path`

How they are used
- `make_r_report.py` uses them to stage files, install packages, and run report-maker scripts in containers

## Example content / bootstrap settings

- `example_csv_dir`
- `example_py_dir`
- `example_r_dir`
- `example_qmd_dir`
- `example_user`
- `example_pw`
- `example_first_name`
- `example_last_name`
- `admin_user`
- `admin_first_name`
- `admin_last_name`
- `admin_pw`

These are used by installer/bootstrap utilities and example-report loading flows.

## Other settings present in the current file

- `use_venv`
- `ftp_host`
- `ftp_user`
- `ftp_pass`

These appear to support installation or example-distribution workflows rather than core request handling.

## Operational guidance

- Prefer setting `BRIDGE_CONFIG_PATH` in deployment so the runtime config location is explicit.
- Rotate all secrets in any copied environment; the checked local file should be treated as development-only state.
- Keep Docker path settings consistent across Python, Quarto, and R execution flows.
- When changing key names, test both direct report creation and scheduled report execution because not every path uses config keys identically.
