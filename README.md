# Signature Breeze Quotation Maker: Android APK

This is the Capacitor wrapper for `www/index.html` (the quotation maker).
The app runs offline except for Google Fonts and html2pdf.js, which load from CDNs.

## What changed from the browser version
- **PDF output:** on Android, the PDF is saved to app cache and opened in the
  native share sheet (WhatsApp, Gmail, Drive, "Save to Files", etc.).
  In a normal browser it still uses download / Web Share as before.
- Everything else (form, numbering, GST, signature upload, localStorage) is unchanged.

## Prerequisites (on your computer)
1. Node.js 18 or newer (`node -v`)
2. JDK 17 or 21 (`java -version`)
3. Android Studio (includes the Android SDK; install API 34 platform)

## Build the debug APK
```bash
cd signature-breeze-apk
npm install
npx cap add android          # first time only, creates the android/ project
npx cap sync android         # copies www/ into the Android project
cd android
./gradlew assembleDebug      # Windows: gradlew.bat assembleDebug
```
Output: `android/app/build/outputs/apk/debug/app-debug.apk`
Copy it to your phone and install (enable "Install unknown apps" for your file manager).

## Build a signed release APK (for Play Store or distribution)
1. Create a keystore once. Keep this file and its passwords safe; you need them for every future update:
   ```bash
   keytool -genkey -v -keystore signature-breeze.keystore -alias sigbreeze \
     -keyalg RSA -keysize 2048 -validity 10000
   ```
2. In `android/app/build.gradle`, add a `signingConfigs` block pointing to the keystore
   and set `release { signingConfig signingConfigs.release }`.
   (Android Studio: Build > Generate Signed App Bundle / APK does this via a wizard.)
3. Run `./gradlew assembleRelease`
   Output: `android/app/build/outputs/apk/release/app-release.apk`

## Updating the app later
Edit `www/index.html`, then run `npx cap sync android` and rebuild the APK.

## Notes
- Change the app ID in `capacitor.config.json` (currently `com.signaturebreeze.quotation`)
  before publishing; it cannot be changed after the first Play Store upload.
- The quotation number counter is stored in the app's local storage on the phone.
  Uninstalling the app resets it, so keep a backup of the last number if that matters.
- To work fully offline, download html2pdf.js and the two fonts into `www/` and
  point the `<script>` / `<link>` tags there.
