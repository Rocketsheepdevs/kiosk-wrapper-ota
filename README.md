# kiosk-wrapper-ota

OTA update manifest for [kiosk_wrapper](https://github.com/Rocketsheepdevs/kiosk_wrapper).

Served at: **https://kiosk-wrapper-ota.web.app/manifest.json**

## How updates work

The APK is hosted on this same Firebase site, alongside the manifest.

1. Build a new APK of `kiosk_wrapper`:
   ```bash
   cd ../kiosk_wrapper
   flutter build apk --release
   ```
2. Copy it into `public/` with a versioned filename:
   ```bash
   cp ../kiosk_wrapper/build/app/outputs/flutter-apk/app-release.apk \
      public/kiosk_wrapper-1.0.1.apk
   ```
3. Compute its SHA256:
   - Mac/Linux: `shasum -a 256 public/kiosk_wrapper-1.0.1.apk`
   - Windows PowerShell: `Get-FileHash public\kiosk_wrapper-1.0.1.apk -Algorithm SHA256`
4. Edit `public/manifest.json`:
   - bump `versionCode` (must be strictly greater than what's running on the kiosks)
   - update `versionName`, `url`, `sha256`, `notes`
5. Deploy:
   ```bash
   firebase deploy --only hosting
   ```
6. Kiosks pick up the new version on their next 30-min check, or instantly via
   the admin panel's "Check now" button.

APK files are `.gitignored` — they only need to exist locally at deploy time.

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
