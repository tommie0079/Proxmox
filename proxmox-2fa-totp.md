# Set Up Two-Factor Authentication (TOTP) in Proxmox

## Enable TOTP for a Proxmox User

1. Log in to the Proxmox web interface.
2. Go to `Datacenter -> Users`.
3. Select your user account, for example `root@pam`.
4. Click `TFA -> Add`.
5. Choose `OATH (TOTP)`.
6. Scan the QR code with an authenticator app on your phone.
7. Enter the 6-digit code from the app to confirm setup.

## Result

After this is completed, the user account will require a TOTP code during sign-in.