# Proxmox Auto-Update (Unattended Upgrades)

Automatically install stable security patches and point releases on Proxmox.

> **Note:** This only covers updates within the same major version (e.g. 8.x → 8.y). Major version upgrades (e.g. Proxmox 8 → 9) always require manual intervention.

---

## 1 — Install unattended-upgrades

```bash
apt update && apt install -y unattended-upgrades apt-listchanges
```

## 2 — Enable it

```bash
dpkg-reconfigure -plow unattended-upgrades
```

Select **Yes** when prompted.

## 3 — Configure origins

Edit `/etc/apt/apt.conf.d/50unattended-upgrades` and make sure the `Origins-Pattern` block includes Proxmox:

```
Unattended-Upgrade::Origins-Pattern {
    "origin=Debian,codename=${distro_codename},label=Debian";
    "origin=Debian,codename=${distro_codename},label=Debian-Security";
    "origin=Debian,codename=${distro_codename}-security,label=Debian-Security";
    "origin=Proxmox";
};
```

## 4 — Set update frequency

Create or edit `/etc/apt/apt.conf.d/20auto-upgrades`:

```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

This checks and installs updates daily.

## 5 — (Optional) Auto-reboot

Some updates (e.g. kernel) require a reboot. In `/etc/apt/apt.conf.d/50unattended-upgrades`, uncomment or add:

```
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "03:00";
```

Pick a time when the system is idle.

## 6 — Test

```bash
unattended-upgrade --dry-run --debug
```

This simulates the upgrade so you can verify it picks up the right packages without actually installing anything.

---

## Prerequisites

If you are using the **free no-subscription repo** (no enterprise license), make sure it is configured:

```bash
# Should exist in /etc/apt/sources.list
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription
```

And that the enterprise repo is **commented out** (otherwise apt will fail on authentication errors):

```bash
# Comment out the line in /etc/apt/sources.list.d/pve-enterprise.list
# deb https://enterprise.proxmox.com/debian/pve bookworm pve-enterprise
```

---

## Notes

- **Production clusters:** Consider staggering updates across nodes so they don't all reboot at the same time.
- **Logs:** Check `/var/log/unattended-upgrades/` for history and errors.
- **Disable temporarily:** `sudo dpkg-reconfigure unattended-upgrades` and select **No**.
