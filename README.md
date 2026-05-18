# kiosk-wrapper-ota

OTA update manifest for [kiosk_wrapper](https://github.com/Rocketsheepdevs/kiosk_wrapper).

Served at: **https://kiosk-wrapper-ota.web.app/manifest.json**

## How updates work

1. Build a new APK of `kiosk_wrapper` and upload it to `uploads.drc.zone`.
2. Compute its SHA256:
   - Mac/Linux: `shasum -a 256 app-release.apk`
   - Windows PowerShell: `Get-FileHash app-release.apk -Algorithm SHA256`
3. Edit `public/manifest.json`:
   - bump `versionCode` (must be strictly greater than what's running on the kiosks)
   - update `versionName`, `url`, `sha256`, `notes`
4. Deploy:
   ```bash
   firebase deploy --only hosting
   ```
5. Kiosks pick up the new version on their next 30-min check, or instantly via
   the admin panel's "Check now" button.

## Manifest format

```json
{
  "versionCode": 5,
  "versionName": "1.0.5",
  "url": "https://uploads.drc.zone/kiosk_wrapper-1.0.5.apk",
  "sha256": "abcdef0123456789...",
  "notes": "Bug fixes for printer retry"
}
```

`versionCode` is the integer Android build number — the kiosk wrapper compares
it against the running app's build number and only installs if strictly greater.

## First-time setup on a new workstation

```bash
npm install -g firebase-tools
firebase login
git clone https://github.com/Rocketsheepdevs/kiosk-wrapper-ota
cd kiosk-wrapper-ota
firebase deploy --only hosting
```
