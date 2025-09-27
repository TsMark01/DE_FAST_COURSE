# Jupyter Notebook Installation Guide

## 📋 Overview 

This guide provides step-by-step instructions for installing and configuring **Jupyter Notebook**, a web-based interactive computing environment ideal for data analysis, visualization, and prototyping. It covers setup on Linux (with notes for macOS and Windows via WSL), configuration for remote access, and running Jupyter as a background service using systemd. This setup is perfect for showcasing data science and scripting skills in a professional portfolio.

### 🎯 Key Objectives
- Install Jupyter Notebook and dependencies.
- Configure for secure remote access.
- Set up a systemd daemon for continuous operation.
- Ensure compatibility with Python 3.9+ environments.
- Provide troubleshooting and best practices.

## 🛠️ Tech Stack

| Category          | Tools/Technologies       | Purpose |
|-------------------|--------------------------|---------|
| **Notebook**      | Jupyter Notebook        | Interactive computing |
| **Language**      | Python 3.9+             | Core runtime |
| **System**        | systemd (Linux)         | Daemon service |
| **Dependencies**  | notebook (pip)          | Jupyter core package |

## 🏗️ Architecture

Jupyter Notebook's setup includes:
1. **Core Application**: Runs a web server for interactive Python notebooks.
2. **Configuration**: Custom settings for IP, port, and access control.
3. **Daemon**: Systemd service to run Jupyter Notebook in the background.
4. **Working Directory**: Stores notebook files for user access.

## 🚀 Quick Start

### Prerequisites
- Python 3.9+ (with pip and venv).
- Administrative privileges (sudo) for system services.
- Optional: Virtual environment for dependency isolation.

### Setup Instructions

1. **Install System Dependencies** (Linux/Ubuntu)
   ```bash
   sudo apt update
   sudo apt install -y python3-pip python3-dev
   ```

2. **Set Up Virtual Environment** (Recommended)
   ```bash
   python3 -m venv jupyter_env
   source jupyter_env/bin/activate
   ```

3. **Install Jupyter Notebook**
   ```bash
   pip install notebook
   ```

4. **Generate Configuration File**
   ```bash
   jupyter notebook --generate-config
   ```
   The config file is created at `~/.jupyter/jupyter_notebook_config.py` (hidden directory). Check with:
   ```bash
   ls -la ~/.jupyter/
   ```

5. **Configure Jupyter for Remote Access**
   Edit `~/.jupyter/jupyter_notebook_config.py` and add:
   ```python
   c.NotebookApp.ip = '0.0.0.0'  # Allow connections from any IP
   c.NotebookApp.allow_origin = '*'  # Enable cross-origin requests
   c.NotebookApp.allow_remote_access = True  # Permit remote access
   c.NotebookApp.allow_root = True  # Allow running as root (for systemd)
   c.NotebookApp.port = 8989  # Custom port
   c.NotebookApp.open_browser = True  # Auto-open browser (optional)
   ```

6. **Set a Password for Security**
   ```bash
   jupyter notebook password
   ```
   Follow prompts to set a password. This secures your notebook server.

7. **Create Working Directory**
   ```bash
   mkdir -p /jupyter_notebook_files
   chmod 777 /jupyter_notebook_files
   ```

8. **Test Jupyter Notebook**
   ```bash
   jupyter notebook --config ~/.jupyter/jupyter_notebook_config.py
   ```
   Access at `http://<your-ip>:8989` (e.g., `http://localhost:8989` or your server’s public IP). Log in with your password.

### Daemon Configuration (Systemd on Linux)

To run Jupyter Notebook as a background service, configure a systemd unit.

1. **Create Systemd Service File** (`/usr/lib/systemd/system/jupyter-notebook.service`)
   ```
   [Unit]
   Description=Jupyter Notebook daemon
   After=network.target

   [Service]
   WorkingDirectory=/jupyter_notebook_files/
   User=root
   Group=root
   Type=simple
   ExecStart=/usr/local/bin/jupyter notebook --config=/root/.jupyter/jupyter_notebook_config.py
   Restart=always
   RestartSec=10s

   [Install]
   WantedBy=multi-user.target
   ```

2. **Enable and Start the Daemon**
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable jupyter-notebook
   sudo systemctl start jupyter-notebook
   sudo systemctl status jupyter-notebook
   ```

   Check logs with:
   ```bash
   journalctl -u jupyter-notebook
   ```

3. **Access the Notebook**
   Open `http://<your-ip>:8989` in a browser. Use your password to log in.

### Additional Configuration

- **Custom Port**: Change `c.NotebookApp.port` in the config file if 8989 is in use.
- **Security**: Use HTTPS in production (add SSL certificates to config).
- **macOS/Windows (WSL)**: Install Jupyter in WSL for Windows; map `/jupyter_notebook_files` to a shared folder.
- **Notebook Storage**: Save `.ipynb` files in `/jupyter_notebook_files`.

## 🧪 Troubleshooting

- **Port Conflicts**: Check with `sudo netstat -tulnp | grep 8989` and kill conflicting processes (`sudo kill -9 <PID>`).
- **Config Not Found**: Verify `~/.jupyter/jupyter_notebook_config.py` exists.
- **Permission Issues**: Ensure `/jupyter_notebook_files` is writable (`chmod -R 777 /jupyter_notebook_files`).
- **Daemon Failures**: Check logs (`journalctl -u jupyter-notebook`) for errors.

## 📝 Additional Notes

- **Production Setup**: Use a reverse proxy (e.g., Nginx) with SSL for secure access.
- **Portfolio Tip**: Include this guide in your GitHub repo to demonstrate system administration and documentation skills for CS applications.
- **Updates**: Keep Jupyter updated (`pip install --upgrade notebook`).

For more details, see the [Jupyter Notebook Documentation](https://jupyter-notebook.readthedocs.io/en/stable/).
