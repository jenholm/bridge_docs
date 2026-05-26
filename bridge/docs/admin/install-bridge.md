# Install Bridge

This page is for system administrators installing the Bridge Flask application on a Linux server.

Use this guide when you need to:
- prepare the server for Bridge
- create the root `config.ini`
- run `bridge/installer/install_bridge.sh`
- verify the installation
- troubleshoot installer failures

The installer is designed for Debian/Ubuntu-derived systems and is located at:
`bridge/installer/install_bridge.sh`

The installer reads installation settings from the project-root config file:
`config.ini`

Use the example file as the safe starting point:
`config.ini.example`

Do not publish or commit a real `config.ini`. It contains database passwords, application secrets, mail credentials, and bootstrap account passwords.

## What the installer does

The installer performs these major steps:

1. Verifies that the operating system is Debian/Ubuntu-compatible.
2. Installs apt packages needed by Python, report rendering, and PDF generation.
3. Checks `config.ini` and creates Bridge runtime directories.
4. Pulls or builds Docker images used by report execution.
5. Copies and extracts example report assets from the `bridge-base` container image.
6. Creates or updates the Python virtual environment at `bridge/venv`.
7. Installs the Bridge package and Python requirements.
8. Installs and starts MySQL if needed.
9. Drops and recreates the `bridge` database.
10. Imports the configured schema file.
11. Creates or updates MySQL users.
12. Installs `wkhtmltopdf` if it is missing.
13. Adds the installing user to the Docker group.
14. Loads example reports.
15. Creates or updates the configured admin and example dev users.

Important consequence:
- the installer drops and recreates the `bridge` database every time it runs
- do not rerun it on a production system without a database backup

## Supported operating systems

The installer accepts Debian/Ubuntu-derived distributions based on `/etc/os-release`.

It installs packages with `apt-get`, so it is not currently a native installer for Red Hat, Fedora, Rocky, AlmaLinux, Arch, or macOS.

## Required administrator access

Run the installer as the normal Linux user who will own and run the application, not as root.

That user must have sudo permission because the installer runs commands such as:
- `sudo apt-get update`
- `sudo apt-get install ...`
- `sudo systemctl start mysql`
- `sudo systemctl enable mysql`
- `sudo chown -R "$USER":"$USER" bridge`
- `sudo usermod -aG docker "$USER"`

## Required external tools

Before running the installer, make sure Docker is installed and working.

The installer uses Docker to pull or build report-execution images, create a temporary `bridge-base` container, and copy example report assets out of that image. It does not install Docker itself.

Check Docker:

```bash
docker --version
sudo systemctl status docker
```

If the installing user is not already in the Docker group, the installer attempts to add the user. That change does not affect the current shell immediately. After installation, run:

```bash
newgrp docker
```

or log out and back in.

## Installation files

Important paths relative to the project root. In the examples below, replace `/path/to/bridge` with the directory where you checked out or unpacked the Bridge project.

- project root: `/path/to/bridge`
- installer: `/path/to/bridge/bridge/installer/install_bridge.sh`
- installer helper: `/path/to/bridge/bridge/installer/config_ini_helpers.sh`
- generated or edited config: `/path/to/bridge/config.ini`
- safe example config: `/path/to/bridge/config.ini.example`
- schema file default: `bridge/installer/bridge_schema.sql`
- app virtual environment: `bridge/venv`
- app log file: `bridge/logs/bridge.log`
- upload folder default: `bridge/installer/UPLOAD_FOLDER`

## Prepare `config.ini`

The installer only reads the project-root `config.ini`.

From the project root, create it from the example file:

```bash
cd /path/to/bridge
cp config.ini.example config.ini
chmod 600 config.ini
```

Then edit `config.ini` and replace all placeholder values with environment-specific values.

Do not paste real passwords into documentation, tickets, chat, or version control.

## Required installer config keys

The installer requires these keys to be present and non-empty:

- `mysql_root_password`
- `mysql_user`
- `mysql_password`
- `admin_user`
- `admin_pw`
- `example_user`
- `example_pw`

If any are missing, the installer stops with a message like:

```text
Missing required installer properties in .../config.ini: key_name
```

## Optional installer config keys

The installer reads these optional keys and uses defaults when they are missing:

- `mysql_host`
  - default: `127.0.0.1`
- `schema_file`
  - default: `bridge/installer/bridge_schema.sql`
- `admin_first_name`
  - default: `Admin`
- `admin_last_name`
  - default: `User`
- `example_first_name`
  - default: `Example`
- `example_last_name`
  - default: `User`
- `use_venv`
  - default: `true`

Keep `use_venv = true`. The installer explicitly blocks system-Python installs on modern Ubuntu/Mint packaging.

## Important application config keys

The installer does not validate every application key, but the app and report runners depend on many of them after installation.

Review at least these keys before first startup:

Application:
- `app_key`
- `app_host`
- `app_port`
- `upload_folder`
- `wkhtml_to_pdf_path`

Database:
- `db_user`
- `db_password`
- `mysql_host`
- `mysql_user`
- `mysql_password`
- `schema_file`

Mail and password reset:
- `mail_server`
- `mail_port`
- `mail_use_tls`
- `mail_username`
- `mail_password`
- `security_password_salt`

Docker/report execution:
- `py_docker_image`
- `py_torch_docker_image`
- `quarto_docker_image`
- `r_docker_image`
- `r_docker_gis_image`
- `docker_user_path`
- `r_docker_lib_path`
- `py_docker_lib_path`

Example bootstrap data:
- `example_csv_dir`
- `example_py_dir`
- `example_r_dir`
- `example_qmd_dir`

For more detail, see `../reference/config-reference.md`.

## Database warning

The installer runs SQL equivalent to:

```sql
DROP DATABASE IF EXISTS bridge;
CREATE DATABASE bridge CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

Then it imports the configured schema file if it exists.

Before running on an existing server, back up the database:

```bash
mysqldump -u root -p bridge > bridge-backup.sql
```

Only continue after you are sure the existing `bridge` database can be replaced.

## Run the installer

From the project root:

```bash
cd /path/to/bridge
bash bridge/installer/install_bridge.sh
```

Installer options:

```text
--skip-setup    run non-interactively
--build-images  build Docker images locally instead of pulling them
--no-venv       skip virtual environment creation
-h, --help      show installer help
```

Do not use `--no-venv` for normal installs. The script exits with an error if system-Python installation is selected.

Use `--skip-setup` for unattended execution where no prompt should be shown.

Use `--build-images` only when the server should build images from local build scripts under `bridge/installer/images/` instead of pulling published images.

## Docker images used by Bridge

By default the installer pulls the Bridge images listed in `bridge/installer/install_bridge.sh` from GitHub Container Registry. In the examples below, replace `<registry-owner>` with the registry namespace used by your installer:

- `ghcr.io/<registry-owner>/bridge-base`
- `ghcr.io/<registry-owner>/bridge-r`
- `ghcr.io/<registry-owner>/bridge-gis`
- `ghcr.io/<registry-owner>/bridge-pytorch`

It then tags them locally as:

- `bridge-base:latest`
- `bridge-r:latest`
- `bridge-gis:latest`
- `bridge-pytorch:latest`

The local tags matter because the app config typically refers to the short local image names.

## Files and directories created by installation

The installer creates or uses:

- `bridge/logs`
- `bridge/reports/in`
- `bridge/reports/upload`
- `bridge/installer/UPLOAD_FOLDER`
- `bridge/installer/examples`
- `/tmp/bridge_assets`
- `/tmp/bridge_wkhtmltox`
- `bridge/venv`

It also changes ownership under `bridge` to the installing Linux user.

## Start Bridge after installation

After the installer finishes, refresh Docker group membership:

```bash
newgrp docker
```

Then start the Flask app from the project root:

```bash
cd /path/to/bridge
./run_bridge.sh
```

The helper script activates `bridge/venv`, sets `FLASK_APP=bridge:create_app`, and runs Flask without the reloader.

By default the application listens according to `app_host` and `app_port` in `config.ini`. The example config uses port `5000`.

## First login

The installer creates or updates two Bridge users from `config.ini`:

Admin user:
- email from `admin_user`
- password from `admin_pw`
- role: `admin`

Example developer user:
- email from `example_user`
- password from `example_pw`
- role: `dev`

Use the configured admin account for the first login.

After login, verify that the admin navbar includes:
- User Admin
- User Resource Usage
- Container Control

## Verify the installation

Use this checklist after the installer completes.

System checks:

```bash
cd /path/to/bridge
bridge/venv/bin/python --version
bridge/venv/bin/python -m pip show bridge
mysql -h 127.0.0.1 -u "$MYSQL_USER" -p bridge -e "SELECT 1;"
docker images | grep bridge
wkhtmltopdf --version
```

If you do not have `MYSQL_USER` in your shell, use the `mysql_user` value from `config.ini`.

Application checks:

1. Start the app with `./run_bridge.sh`.
2. Open the configured host and port in a browser.
3. Log in as the configured admin user.
4. Open User Admin and confirm the admin and example users exist.
5. Open Container Control and confirm the page loads.
6. Open Model Maintenance as the admin or example dev user and verify example reports are present.
7. Build or open an example report if you want an end-to-end Docker/report execution check.

Log check:

```bash
tail -n 100 bridge/logs/bridge.log
```

## Troubleshooting

### Unsupported OS

Symptom:

```text
Unsupported OS format
```

Cause:
- the installer did not detect Debian or Ubuntu compatibility in `/etc/os-release`

Fix:
- use an Ubuntu/Debian-derived server
- or port the apt/systemd package steps manually for the target distribution

### Missing installer helper

Symptom:

```text
Missing installer config helper: .../config_ini_helpers.sh
```

Cause:
- `bridge/installer/config_ini_helpers.sh` is missing or the repository checkout is incomplete

Fix:
- restore the missing file from source control
- run the installer from a complete repository checkout

### Missing `config.ini`

Symptom:

```text
Missing root config at .../config.ini
```

What happens:
- the installer tries to run `bridge/installer/make_configs.py`
- that script prompts for the MySQL root password and writes a generated root `config.ini`

Recommended fix:

```bash
cd /path/to/bridge
cp config.ini.example config.ini
chmod 600 config.ini
nano config.ini
```

Then rerun the installer.

### Missing required properties

Symptom:

```text
Missing required installer properties in .../config.ini: ...
```

Cause:
- a required key is missing or blank

Fix:
- add values for the listed keys in the `[DEFAULT]` section of `config.ini`
- rerun the installer

Required keys are listed in the section `Required installer config keys` above.

### Apt package failures

Symptoms:
- apt update warnings
- package installation stops
- missing packages such as `python3.12-dev`, `python3-venv`, `libffi-dev`, or MySQL packages

Fixes:

```bash
sudo apt-get update
sudo apt-get install -f
sudo apt-get install python3.12-dev python3-venv python3-pip python3-full libffi-dev mysql-server
```

Then rerun the installer.

If the server does not provide `python3.12-dev`, use an OS release that ships Python 3.12 packages or update the installer package list for the Python version supported by that server.

### Docker command not found

Symptom:

```text
docker: command not found
```

Cause:
- Docker is not installed

Fix:
- install Docker Engine for the server distribution
- start Docker
- rerun the Bridge installer

Check:

```bash
docker --version
sudo systemctl status docker
```

### Permission denied when using Docker

Symptoms:

```text
permission denied while trying to connect to the Docker daemon socket
```

or report builds fail because Docker cannot be accessed.

Cause:
- the installing user is not active in the Docker group in the current shell

Fix:

```bash
newgrp docker
```

or log out and back in.

Then check:

```bash
docker ps
```

### Unable to pull container images

Symptom:

```text
Unable to pull container image ghcr.io/<registry-owner>/...
```

Causes:
- no network access to GitHub Container Registry
- DNS/proxy/firewall issue
- registry authentication/rate limit issue

Fixes:

```bash
docker pull ghcr.io/<registry-owner>/bridge-base
docker pull ghcr.io/<registry-owner>/bridge-r
docker pull ghcr.io/<registry-owner>/bridge-gis
docker pull ghcr.io/<registry-owner>/bridge-pytorch
```

If the server cannot pull from GHCR, rerun with local image builds:

```bash
bash bridge/installer/install_bridge.sh --build-images
```

Only use `--build-images` if the local build scripts and Dockerfiles are present under `bridge/installer/images/`.

### Failed to copy examples archive

Symptoms:

```text
Unable to create temporary container from bridge-base:latest
Failed to copy bundled examples archive from bridge-base:latest
Bundled examples archive is empty or invalid
```

Causes:
- `bridge-base:latest` does not exist locally
- the image pull/build failed
- the image does not contain `/opt/bridge_assets/examples.tar.gz`

Fixes:

```bash
docker images | grep bridge-base
docker create bridge-base:latest
```

If the image is missing, pull or build it again and rerun the installer.

### Virtual environment problems

Symptoms:
- `Virtual environment Python not found`
- pip install errors
- `System Python installs are blocked...`

Fixes:

```bash
cd /path/to/bridge
rm -rf bridge/venv
python3 -m venv bridge/venv
bridge/venv/bin/python -m pip install --upgrade pip setuptools wheel
```

Then rerun the installer without `--no-venv`.

### Python dependency install failures

Symptoms:
- `pip install -e .` fails
- `pip install -r bridge/requirements.txt` fails

Fixes:
- confirm the virtual environment exists
- confirm `python3-dev` or `python3.12-dev`, `libffi-dev`, and build tools are installed
- rerun pip manually to see the full error

```bash
cd /path/to/bridge
bridge/venv/bin/python -m pip install -e .
bridge/venv/bin/python -m pip install -r bridge/requirements.txt
```

### MySQL will not start

Symptoms:
- `systemctl start mysql` fails
- installer stops during database setup

Fixes:

```bash
sudo systemctl status mysql
sudo journalctl -u mysql --no-pager -n 100
sudo apt-get install mysql-server
sudo systemctl start mysql
```

Then rerun the installer.

### Unable to connect to MySQL as root

Symptom:

```text
Unable to connect to MySQL as root using sudo socket auth or mysql_root_password.
```

Cause:
- neither local sudo socket authentication nor `mysql_root_password` worked

Fixes:
- verify that `sudo mysql -u root -e "SELECT 1;"` works
- verify that `mysql_root_password` is correct in `config.ini`
- verify `mysql_host` points to the intended MySQL server

Commands:

```bash
sudo mysql -u root -e "SELECT 1;"
mysql -h 127.0.0.1 -u root -p -e "SELECT 1;"
```

### Schema file missing

Symptom:

```text
Missing schema mapping file referenced: ... Skipping DB table generation.
```

Cause:
- the configured `schema_file` does not exist

Fix:
- set `schema_file = bridge/installer/bridge_schema.sql`
- confirm the file exists
- rerun the installer

Check:

```bash
ls -l bridge/installer/bridge_schema.sql
```

If the installer skipped schema import, the app may start but fail later when database tables are missing.

### MySQL application user verification fails

Symptom:
- the installer fails while verifying the configured MySQL application user

Cause:
- `mysql_user`, `mysql_password`, or `mysql_host` is incorrect
- MySQL rejected the created user or host mapping

Fixes:
- confirm the values in `config.ini`
- try the same login manually

```bash
mysql -h 127.0.0.1 -u your_mysql_user -p bridge -e "SELECT 1;"
```

Then rerun the installer.

### wkhtmltopdf download or install fails

Symptoms:
- failed download of `wkhtmltox_0.12.6.1-2.jammy_amd64.deb`
- `wkhtmltopdf binary was not found after installation`

Causes:
- no network access to GitHub releases
- incompatible distribution package
- apt dependency failure

Fixes:

```bash
wkhtmltopdf --version
sudo apt-get install fontconfig libfreetype6 libjpeg-turbo8 libpng16-16 libx11-6 libxcb1 libxext6 libxrender1 xfonts-base xfonts-75dpi xz-utils wget
```

If needed, manually install a compatible `wkhtmltopdf` package and set `wkhtml_to_pdf_path` in `config.ini` to the actual binary path.

### Admin or example user was not created

Symptoms:
- cannot log in with the configured admin account
- admin user exists but does not have admin navigation
- example user missing

Causes:
- installer failed before `setup_tools_and_docker`
- database schema was missing
- `admin_user`, `admin_pw`, `example_user`, or `example_pw` was wrong in `config.ini`

Fixes:
- correct the values in `config.ini`
- confirm the schema was imported
- rerun the installer

Remember that rerunning the installer recreates the database.

### Flask app does not start

Symptoms:
- `./run_bridge.sh` exits immediately
- Flask import error
- configuration load error

Fixes:

```bash
cd /path/to/bridge
source bridge/venv/bin/activate
export BRIDGE_CONFIG_PATH=/path/to/bridge/config.ini
export FLASK_APP=bridge:create_app
flask run --no-reload
```

Check the app log:

```bash
tail -n 100 bridge/logs/bridge.log
```

Also confirm that `config.ini` is in the project root and is readable by the user running Flask.

### Browser login or session behaves oddly over HTTP

The app sets secure session-cookie behavior in code. If you run the app over plain HTTP for local testing, login/session behavior can differ from production HTTPS behavior.

For production, run Bridge behind HTTPS. For local testing, use a browser and host configuration that matches the current application cookie settings.

### Report builds fail after the app starts

Causes to check:
- Docker permission for the Flask-running user
- local Bridge Docker image tags exist
- `upload_folder` is writable
- `docker_user_path` matches paths expected inside containers
- `wkhtmltopdf` works if PDF export fails
- R/Python support paths in `config.ini` match the repository layout

Useful commands:

```bash
docker ps
docker images | grep bridge
ls -ld bridge/installer/UPLOAD_FOLDER
wkhtmltopdf --version
tail -n 100 bridge/logs/bridge.log
```

## Safe rerun checklist

Before rerunning the installer on an existing system:

1. Back up the `bridge` database.
2. Back up `config.ini`.
3. Confirm Docker images can be pulled or local image builds are available.
4. Confirm `schema_file` points to the intended SQL schema.
5. Warn users that the app may be unavailable during installation.
6. Remember that the database will be dropped and recreated.

## Security checklist

After installation:

- keep `config.ini` mode restricted, for example `chmod 600 config.ini`
- rotate any placeholder passwords copied from `config.ini.example`
- use strong unique values for `app_key` and `security_password_salt`
- store SMTP credentials securely
- limit shell access to the application user
- run production traffic over HTTPS
- do not commit `config.ini`
- keep database backups separate from the application directory

## Related documents

- `admin-users.md` - admin UI tasks after installation
- `common-admin-tasks.md` - quick-reference admin tasks
- `../reference/config-reference.md` - configuration key reference
- `../getting-started.md` - Bridge application overview
