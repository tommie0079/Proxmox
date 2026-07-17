# Server Setup Guide

## Firewall

Install UFW, allow SSH **before** enabling the firewall (otherwise you risk locking yourself out of an active SSH session), then enable it.

```bash
sudo apt install ufw -y
sudo ufw allow ssh
sudo ufw enable
```

## Install Nano

```bash
sudo apt install nano -y
```

## Install Ping

```bash
sudo apt install iputils-ping -y
```

## Samba

Install Samba:

```bash
sudo apt install samba -y
```

Enable at boot:

```bash
sudo systemctl enable smbd
```

Create a shared folder:

```bash
sudo mkdir -p /srv/samba/share
```

Give your user access (use the full path and the `nimda` user/group):

```bash
sudo chown -R userexample:userexample /srv/samba/share
sudo chmod 775 /srv/samba/share
```

Configure Samba:

```bash
sudo nano /etc/samba/smb.conf
```

Add this to the bottom:

```ini
[Share]
path = /srv/samba/share
browseable = yes
read only = no
writable = yes
guest ok = no
valid users = userexample
```

Save and restart:

```bash
sudo systemctl restart smbd
```

Create a Samba password:

```bash
sudo smbpasswd -a nimda
```

Enable the account:

```bash
sudo smbpasswd -e userexample
```

Allow through the firewall:

```bash
sudo ufw allow samba
sudo systemctl restart smbd
```

## Install Docker

### 1. Set up Docker's apt repository

Add Docker's official GPG key:

```bash
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Add the repository to Apt sources:

```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

### 2. Install the Docker packages

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 3. Test

```bash
sudo docker run hello-world
```

## Tips and Tricks

```bash
sudo reboot now
sudo shutdown now
ip a
```
