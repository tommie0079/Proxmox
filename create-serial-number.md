# 1. Skap serienummer og tildel den til VM-en

Følg denne instruksjonen for å skape et serienummer på Proxmox og knytte det til Windows 11 VM-en.

## Steg 1: Åpne Shell

1. Gå til Proxmox under **Datacenter → PVE**.
2. Trykk på **Shell**.

## Steg 2: Installer uuid-runtime

Kjør følgende kommandoer i shellet:

```bash
apt update
apt install uuid-runtime
```

## Steg 3: Sett VMID

Endre `VMID` til din Windows VM. For eksempel:

```bash
VMID=102
```

## Steg 4: Skap serienummer og knytt det til VM-en

Kjør følgende kommando (bytt ut `<VMID>` med din VMID):

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

Kommandoen gjør følgende:

- Stopper VM-en.
- Lager en sikkerhetskopi av konfigurasjonsfila (`.conf.bak`).
- Genererer et nytt serienummer med `uuidgen`.
- Legger serienummeret inn i SMBIOS-innstillingene for VM-en.
- Skriver ut serienummeret og starter VM-en igjen.
