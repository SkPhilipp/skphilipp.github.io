# Jupyter Lab Development Machine

Edition 2018 Ubuntu 17 Python 2.7.14

## Installation

Compile Python from source, get Jupyter Lab with pip, create a separate user for hosting the process and add a default configuration.

```bash
apt-get update
apt-get install build-essential checkinstall
apt-get install libreadline-gplv2-dev libncursesw5-dev libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev
cd /usr/src
curl https://www.python.org/ftp/python/2.7.14/Python-2.7.14.tgz | tar xz
cd Python-2.7.14/
./configure 
make altinstall
rm -rf Python-2.7.14
curl https://bootstrap.pypa.io/get-pip.py | python2.7
pip install jupyterlab
useradd -m jupyter
su - jupyter
mkdir notebooks
mkdir .jupyter
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

You can now run `jupyter lab notebooks` from user `jupyter`, which will start Jupyter Lab on port 8080.

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
User=jupyter
Group=jupyter
WorkingDirectory=/home/jupyter
[Install]
WantedBy=multi-user.target
EOF
systemctl daemon-reload
systemctl enable jupyter-lab
systemctl start jupyter-lab
```

