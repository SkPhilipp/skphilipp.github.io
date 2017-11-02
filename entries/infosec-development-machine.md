# Infosec Development Machine

## Tools

- Everything on [Kali Linux](https://www.kali.org/downloads/)
- Docker installation ([instructions for Kali](https://www.ptrace-security.com/2017/06/14/how-to-install-docker-on-kali-linux-2017-1/))
- [howdoi](http://tldr.sh/) command line help utility
- [tldr](http://tldr.sh/) command line help utility
- [jupyter lab](https://github.com/jupyterlab/jupyterlab) for taking notes<br/>
- TransmissionQT

## OS Installation

To create a Bootable USB from a Windows machine:
1. Get Transmission [https://transmissionbt.com/download/](https://transmissionbt.com/download/)
2. Get Kali .torrent [https://www.kali.org/downloads/](https://www.kali.org/downloads/)
3. Get Win32 Disk Imager [https://launchpad.net/win32-image-writer](https://launchpad.net/win32-image-writer)
4. Use Win32 Disk Images to create a Bootable USB of the Kali ISO

Plug in the Bootable USB and step through the non-GUI Install option

## Post-Installation Customizations

```bash
echo 'deb http://http.kali.org/kali kali-rolling main non-free contrib' >> /etc/apt/sources.list
apt-get update
apt-get upgrade
apt --fix-broken install
pip install howdoi
pip install tldr
cd /root/Downloads
git clone https://github.com/tldr-pages/tldr.git
cat > /root/.tldrrc << EOF
colors:
  command: cyan
  description: blue
  usage: green
platform: linux
repo_directory: /root/Downloads/tldr
EOF
tldr reindex
pip install jupyterlab
jupyter serverextension enable --py jupyterlab --sys-prefix
pip install metakernel
pip install metakernel_bash
apt-get install transmission-qt
```

Install FireFox addons:
- [LastPass](https://addons.mozilla.org/nl/firefox/addon/lastpass-password-manager/)
- [UBlock Origin](https://addons.mozilla.org/nl/firefox/addon/ublock-origin/)
