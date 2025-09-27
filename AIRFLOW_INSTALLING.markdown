# Apache Airflow Installation Guide

## 📋 Overview

This guide provides detailed instructions for installing and configuring **Apache Airflow** on Linux (with notes for macOS and Windows via WSL). Airflow is an open-source platform for orchestrating complex computational workflows and data processing pipelines. It includes setup for a local development environment, daemon configuration using systemd, and best practices for production-like setups.

### 🎯 Key Objectives
- Install Airflow and its dependencies securely.
- Configure the environment for workflow orchestration.
- Set up daemons for the webserver and scheduler.
- Ensure compatibility with databases like PostgreSQL.
- Troubleshoot common issues.

## 🛠️ Tech Stack

| Category          | Tools/Technologies                  | Purpose |
|-------------------|-------------------------------------|---------|
| **Orchestration** | Apache Airflow 2.7.3+              | Workflow management |
| **Database**      | PostgreSQL (recommended) / SQLite  | Metadata storage |
| **Dependencies**  | psycopg2-binary, Flask-Session     | DB connectivity & session management |
| **System**        | systemd (Linux)                    | Daemon services |
| **Language**      | Python 3.9+                        | Core runtime |

## 🏗️ Architecture

Airflow's architecture includes:
1. **Metadata Database**: Stores DAGs, tasks, and execution history (PostgreSQL recommended).
2. **Scheduler**: Parses DAGs and schedules tasks.
3. **Webserver**: Provides UI for monitoring and management.
4. **Workers**: Execute tasks (LocalExecutor for simple setups).
5. **Daemons**: Run scheduler and webserver as background services via systemd.

For daemons:
- **airflow-scheduler.service**: Runs the scheduler continuously.
- **airflow-webserver.service**: Runs the web UI.

## 🚀 Quick Start

### Prerequisites
- Python 3.9+ (with pip and venv).
- Administrative privileges (sudo) for system packages and services.
- PostgreSQL (install via `sudo apt install postgresql` on Ubuntu).
- Optional: Kubernetes extras for advanced setups.

### Setup Instructions

1. **Install System Dependencies** (Linux/Ubuntu)
   ```bash
   sudo apt update
   sudo apt install -y python3-dev libpq-dev build-essential postgresql postgresql-contrib
   ```

2. **Set Up Virtual Environment**
   ```bash
   python3 -m venv airflow_env
   source airflow_env/bin/activate
   ```

3. **Install Airflow and Dependencies**
   ```bash
   pip install apache-airflow[postgresql,kubernetes]==2.7.3
   pip install psycopg2-binary
   pip install Flask-Session==0.5.0
   ```

4. **Configure Airflow Home**
   ```bash
   export AIRFLOW_HOME=/airflow
   mkdir -p /airflow/dags /airflow/plugins /airflow/scripts
   chmod 777 -R /airflow/dags /airflow/plugins /airflow/scripts
   ```

5. **Edit Airflow Configuration** (`/airflow/airflow.cfg` after init)
   ```
   executor = LocalExecutor
   sql_alchemy_conn = postgresql+psycopg2://airflowuser:password@localhost/airflow_metadata
   ```
   Replace credentials as needed.

6. **Initialize Database and User**
   ```bash
   airflow db init
   airflow users create --username admin --firstname Admin --lastname User --role Admin --email admin@example.com --password admin
   ```

7. **Set Up PostgreSQL Database**
   ```bash
   sudo -u postgres psql
   ```
   Inside psql:
   ```
   CREATE DATABASE airflow_metadata;
   CREATE USER airflowuser WITH PASSWORD 'password';
   GRANT ALL PRIVILEGES ON DATABASE airflow_metadata TO airflowuser;
   \q
   ```

8. **Start Airflow Manually (for Testing)**
   ```bash
   airflow scheduler &
   airflow webserver &
   ```

9. **Check and Kill Conflicting Ports**
   ```bash
   sudo netstat -tulnp | grep 8080  # Or 8793 if needed
   sudo kill <PID>
   sudo kill -9 <PID>  # If persistent
   ```

   Access UI at `http://localhost:8080`.

### Daemon Configuration (Systemd on Linux)

To run Airflow as background services (daemons), use systemd unit files. This ensures automatic restarts and system integration.

1. **Create Environment File** (`/etc/sysconfig/airflow`)
   ```
   AIRFLOW_CONFIG=/airflow/airflow.cfg
   AIRFLOW_HOME=/airflow
   ```

2. **Create Scheduler Service** (`/etc/systemd/system/airflow-scheduler.service`)
   ```
   [Unit]
   Description=Airflow scheduler daemon
   After=network.target postgresql.service
   Wants=postgresql.service

   [Service]
   EnvironmentFile=/etc/sysconfig/airflow
   User=root
   Group=root
   Type=simple
   ExecStart=/usr/local/bin/airflow scheduler
   Restart=always
   RestartSec=10s

   [Install]
   WantedBy=multi-user.target
   ```

3. **Create Webserver Service** (`/etc/systemd/system/airflow-webserver.service`)
   ```
   [Unit]
   Description=Airflow webserver daemon
   After=network.target postgresql.service
   Wants=postgresql.service

   [Service]
   EnvironmentFile=/etc/sysconfig/airflow
   User=root
   Group=root
   Type=simple
   ExecStart=/usr/local/bin/airflow webserver --pid /airflow/airflow-webserver.pid
   Restart=on-failure
   RestartSec=5s
   PrivateTmp=true

   [Install]
   WantedBy=multi-user.target
   ```

4. **Enable and Start Daemons**
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable airflow-scheduler
   sudo systemctl enable airflow-webserver
   sudo systemctl start airflow-scheduler
   sudo systemctl start airflow-webserver
   ```

5. **Check Daemon Status**
   ```bash
   sudo systemctl status airflow-scheduler
   sudo systemctl status airflow-webserver
   ```

   Logs: `journalctl -u airflow-scheduler` or `journalctl -u airflow-webserver`.

### Additional Configuration

- **airflow.cfg**: Customize further (e.g., enable email alerts, change DAG folder).
- **Production Tips**: Use CeleryExecutor for distributed tasks; secure with SSL.
- **Windows/WSL**: Install in WSL Ubuntu; map directories if needed.
- **Uninstall/Reset**: `pip uninstall apache-airflow -y`; remove `/airflow` and services.

## 🧪 Troubleshooting

- **DB Connection Errors**: Verify `sql_alchemy_conn` and PostgreSQL user privileges.
- **Permission Issues**: Ensure Airflow home has correct ownership (`chown -R user:group /airflow`).
- **Daemon Failures**: Check `journalctl` for logs; ensure environment file is loaded.
- **Version Conflicts**: Use constraints file during install.

For full config details, see the provided `airflow.cfg` excerpt in your project docs.
