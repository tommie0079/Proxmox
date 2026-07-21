# 1. Create a serial number and assign it to the VM

Follow this instruction to create a serial number on Proxmox and assign it to the Windows 11 VM.

## Step 1: Open the Shell

1. Go to Proxmox under **Datacenter → PVE**.
2. Click on **Shell**.

## Step 2: Install uuid-runtime

Run the following commands in the shell:

```bash
apt update
apt install uuid-runtime
```

## Step 3: Set the VMID

Change `VMID` to your Windows VM. For example:

```bash
VMID=102
```

## Step 4: Create the serial number and assign it to the VM

Run the following command (replace `<VMID>` with your VMID):

```bash
VMID=<VMID>; qm stop $VMID; \
cp /etc/pve/qemu-server/${VMID}.conf /etc/pve/qemu-server/${VMID}.conf.bak; \
SERIAL=$(uuidgen); \
UUID=$(grep -oP 'uuid=\K[^,]+' /etc/pve/qemu-server/${VMID}.conf); \
sed -i '/^args:/d' /etc/pve/qemu-server/${VMID}.conf; \
sed -i "s|^smbios1:.*|smbios1: uuid=${UUID}\nargs: -smbios type=1,serial=${SERIAL},manufacturer=Proxmox,product=Win11-VM-${VMID} -smbios type=3,serial=${SERIAL}|" /etc/pve/qemu-server/${VMID}.conf; \
echo "Serial: ${SERIAL}"; \
qm start $VMID
```

The command does the following:

- Stops the VM.
- Creates a backup of the configuration file (`.conf.bak`).
- Generates a new serial number using `uuidgen`.
- Adds the serial number to the VM's SMBIOS settings.
- Prints the serial number and starts the VM again.
