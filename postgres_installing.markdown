# 📦 Installing PostgreSQL 12+ on Ubuntu

This guide provides a quick setup for **PostgreSQL 12+** on Ubuntu, tailored for data engineering projects.

## 🚀 Steps

1. **Update Package List**:
   ```bash
   sudo apt update
   ```

2. **Install PostgreSQL**:
   ```bash
   sudo apt install postgresql postgresql-contrib
   ```

3. **Verify Service**:
   Check if PostgreSQL is running:
   ```bash
   sudo systemctl status postgresql
   ```
   If not running, start it:
   ```bash
   sudo systemctl start postgresql
   ```

4. **Configure Database**:
   Switch to the `postgres` user and access `psql`:
   ```bash
   sudo -u postgres psql
   ```
   Create a database and user:
   ```sql
   CREATE DATABASE data_engineering;
   CREATE USER myuser WITH PASSWORD 'mypassword';
   GRANT ALL ON DATABASE data_engineering TO myuser;
   ```
   Exit `psql`:
   ```sql
   \q
   ```

5. **Enable Remote Access** (Optional):
   - Edit `/etc/postgresql/<version>/main/pg_hba.conf`:
     ```conf
     host    all             all             0.0.0.0/0               scram-sha-256
     ```
   - Edit `/etc/postgresql/<version>/main/postgresql.conf`:
     Change `listen_addresses = 'localhost'` to `listen_addresses = '*'`.
   - Restart PostgreSQL:
     ```bash
     sudo systemctl restart postgresql
     ```

6. **Test Connection**:
   ```bash
   psql -U myuser -d data_engineering -h localhost -p 5432
   ```
   Success if you see `data_engineering=>`.

## 💡 Notes
- Replace `<version>` with your PostgreSQL version (e.g., 12, 14).
- Use `\q` to exit `psql`.
- Secure your `mypassword` for production.

**Author**: Mark Tsyrul