# Tilt Fire

An Expo React Native mobile application running on **Expo SDK 58** (React Native 0.88), configured for building standalone Android APKs and running on local devices.

---

## 🚀 How to Run on a Local Device

### Option 1: Run with Expo Go (Fastest Development Mode)
1. Install **Expo Go** from Google Play Store on your Android device.
2. Ensure your computer and Android phone are connected to the same Wi-Fi network.
3. In the project directory, run:
   ```bash
   npm install
   npm run start
   ```
4. Scan the QR code displayed in the terminal using the Expo Go app or your phone camera.

### Option 2: Run via USB Debugging (Native Development)
1. Enable **Developer Options** and **USB Debugging** on your Android device.
2. Connect your device via USB and verify it is detected:
   ```bash
   adb devices
   ```
3. Run the application:
   ```bash
   npm run android
   ```

### Option 3: Install the Standalone Built APK
1. Build the APK locally using one of the methods below.
2. Install the APK directly onto your connected device via ADB:
   ```bash
   adb install -r android/app/build/outputs/apk/debug/app-debug.apk
   ```

---

## 📦 How to Build the APK

### Method 1: Local Native Build (Gradle / Prebuild)
You can build the APK directly on your local machine using the configured npm scripts:

- **Build Debug APK:**
  ```bash
  npm run build:android:debug
  ```
  The generated APK will be available at:
  ```
  android/app/build/outputs/apk/debug/app-debug.apk
  ```

- **Build Release APK:**
  ```bash
  npm run build:android:release
  ```
  The generated APK will be available at:
  ```
  android/app/build/outputs/apk/release/app-release.apk
  ```

### Method 2: Cloud / Local Build with EAS CLI
This repository includes a configured `eas.json` that outputs installable `.apk` files:
1. Install EAS CLI globally (if not already installed):
   ```bash
   npm install -g eas-cli
   ```
2. Build APK locally with EAS:
   ```bash
   npm run build:apk
   ```
3. Build APK in the cloud with EAS:
   ```bash
   npm run build:apk:cloud
   ```

### Method 3: Automated CI via GitHub Actions
A GitHub Actions workflow is provided at `.github/workflows/build-apk.yml`.
- Automatically triggers on every push and pull request to `main`.
- Compiles the Android project in a clean environment.
- Uploads the resulting `app-debug.apk` directly to the GitHub Actions Artifacts tab for instant download and review.

---

## 🔍 Code Changes & Explanation

Here is a summary of all changes made to enable building standalone APKs:

1. **`app.json`**:
   - Added `android.package`: `"com.rtnst.tiltfire"` (required unique Android package identifier).
   - Added `android.versionCode`: `1` (version tracking for native package builds).

2. **`eas.json`**:
   - Created EAS Build configuration with `preview` and `production` profiles setting `"buildType": "apk"`.
   - By default, Expo EAS produces `.aab` (Android App Bundle for Google Play). Specifying `"buildType": "apk"` tells the build system to generate a standalone, installable `.apk` file.

3. **`package.json`**:
   - Added npm helper scripts:
     - `build:apk`: Builds APK locally using EAS CLI.
     - `build:apk:cloud`: Triggers remote EAS cloud build.
     - `build:android:debug`: Generates native Android code via `expo prebuild` and builds debug APK via `./gradlew assembleDebug`.
     - `build:android:release`: Prebuilds and builds release APK via `./gradlew assembleRelease`.

4. **`.github/workflows/build-apk.yml`**:
   - Configured CI pipeline to check out code, set up Node.js 20, Java 17, and Android SDK, prebuild native Android files, execute Gradle build, and attach the compiled APK as a downloadable artifact.

5. **`.gitignore`**:
   - Added `/android` and `/ios` so transient native build artifacts remain reproducible across environments without bloating the repository.
