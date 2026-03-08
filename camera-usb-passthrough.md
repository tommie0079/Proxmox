# Forward a Camera USB Port to a VM in Proxmox

## 1. Connect the camera and identify it on the Proxmox host

Open the Proxmox shell:

`Datacenter -> pve -> Shell`

Run:

```bash
lsusb
```

Find the USB hardware ID of the camera you want to pass through.

## 2. Pass the camera through to the ZoneMinder VM

Replace the VM ID and hardware ID with your own values. Example:

```bash
qm set 105 -usb0 host=046d:0843
```

In this example:

- `105` is the VM ID
- `046d:0843` is the USB hardware ID of the camera

## 3. Pass through multiple cameras

If you want to connect more than one camera, you can add additional USB devices like this:

```bash
qm set 103 -usb0 host=046d:0843
qm set 103 -usb1 host=17ef:482f
```

## 4. Verify the VM configuration

Check that the USB devices are listed correctly in the VM configuration:

```bash
grep usb /etc/pve/qemu-server/103.conf
```

## 5. Restart the VM if needed

If the camera does not appear inside the VM devices, restart the VM and check again.

## Troubleshooting

If not all cameras appear inside the VM, verify that the required drivers are installed in the guest operating system.