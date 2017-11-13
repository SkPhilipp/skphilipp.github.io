# Jupyter Lab Development Machine

## Installation

Get Jupyter Lab with pip, create a separate user for hosting the process and add a default configuration. 

```bash
apt -y install python-pip
pip install jupyterlab
useradd -m jupyterhost
su - jupyterhost
mkdir notebooks
cat > .jupyter/jupyter_notebook_config.py << EOF
c.NotebookApp.open_browser = False
c.NotebookApp.token = ''
# Format "<hash method>:<salt>:<hash>", with salt appeneded to password. The example represents password "memes".
c.NotebookApp.password = u'sha1:faeeb4164638:80b264dcc5d723961c5f77a5b7efa20544116c0b'
c.NotebookApp.ip = '*'
c.NotebookApp.port = 8080
EOF
exit
```

You can now run `jupyter lab notebooks` from user `jupyterhost`, which will start Jupyter Lab on port 8080.

## Autorun

Configure Jupyter Lab to run on startup, and start it.

```bash
cat > /lib/systemd/system/jupyter-lab.service << EOF
[Unit]
Description=Jupyter Lab
[Service]
Type=simple
PIDFile=/var/run/jupyter-lab.pid
ExecStart=/usr/local/bin/jupyter lab notebooks
User=jupyterhost
Group=jupyterhost
WorkingDirectory=/home/jupyterhost
[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable jupyter-lab
systemctl start jupyter-lab
```
