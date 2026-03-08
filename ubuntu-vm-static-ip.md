# Set a Static IP Address in an Ubuntu VM

Use the Proxmox console or SSH to access the Ubuntu VM, then run the commands inside the VM.

## 1. Install nano

```bash
sudo apt update
sudo apt install nano -y
```

## 2. Identify the network interface

Check which network interface is connected to your network:

```bash
ip a
ls /etc/netplan/
```

## 3. Edit the netplan configuration

For example:

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

Replace the configuration with:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens18:
      dhcp4: no
      addresses:
        - 192.168.1.38/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
```

Update the following values to match your environment:

- `ens18` with your actual network interface name
- `192.168.1.38/24` with the static IP address you want to assign
- `192.168.1.1` with your default gateway
- DNS servers if needed

## 4. Apply the network configuration

```bash
sudo netplan apply
```

## Result

The Ubuntu VM should now use the static IP address you configured.