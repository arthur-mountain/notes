### Android Development with Gradle and Android SDK CLI

This guide covers the basics of using Android SDK CLI and Gradle through the terminal for Android development.

#### 1. Install Android SDK and Gradle

##### Android SDK Installation

- Install the Android SDK via [Android Studio](https://developer.android.com/studio). The SDK will be installed automatically.
- To verify SDK installation, run:

  ```bash
  sdkmanager --list
  ```

##### Gradle Installation

- Install Gradle:

  ```bash
  brew install gradle
  ```

- Verify installation:

  ```bash
  gradle -v
  adb --version
  ```

#### 2. Create and Build an Android Project

##### Using Gradle to Build an Android App

In the project root directory, use the following command to build the app:

```bash
gradlew assembleDebug # This will generate the APK in `app/build/outputs/apk/debug/`.
```

#### 3. Android SDK CLI

##### Emulator Management with `emulator`

- List available emulators:

  ```bash
  emulator -list-avds
  ```

- Start a specific emulator:

  ```bash
  emulator @<AVD_NAME> # Replace `<AVD_NAME>` with the emulator name.
  ```

##### Install APK to Emulator or Device

- Use `adb` to install APK:

  ```bash
  adb install app/build/outputs/apk/debug/app-debug.apk
  ```

##### Run App on Emulator

```bash
adb shell am start -n com.example.myapp/.MainActivity
```

#### 4. Gradle CLI Commands

##### Build APK

- Debug build:

  ```bash
  gradlew assembleDebug
  ```

- Release build:

  ```bash
  gradlew assembleRelease
  ```

##### Clean Build

```bash
gradlew clean
```

##### Run Tests

- Unit tests:

  ```bash
  gradlew test
  ```

- Instrumented tests:

  ```bash
  gradlew connectedAndroidTest
  ```

#### 5. Debugging with `adb`

##### View Logs

```bash
adb logcat
```

You can filter logs using:

```bash
adb logcat | grep "MyApp"
```

##### Take a Screenshot

```bash
adb shell screencap /sdcard/screenshot.png
adb pull /sdcard/screenshot.png
```

##### Record Screen

```bash
adb shell screenrecord /sdcard/screenrecord.mp4
adb pull /sdcard/screenrecord.mp4
```

### 6. `adb shell`

`adb shell` is used to access the command-line shell environment of an Android device or emulator, allowing you to run commands directly on the device.

#### Usage

```bash
adb shell
```

This command opens a terminal interface, allowing you to interact with the Android device like a Linux environment.

#### Common Operations

- **View file system:**

  ```bash
  adb shell ls /sdcard/
  ```

  List files in the SD card directory.

- **View running processes:**

  ```bash
  adb shell ps
  ```

  List currently running processes.

- **Check device storage:**

  ```bash
  adb shell df
  ```

  Check storage space usage.

- **Launch an application:**

  ```bash
  adb shell am start -n com.example.myapp/.MainActivity
  ```

  Launch the app's `MainActivity`.

- **Exit `adb shell`:**
  Simply type `exit` to close the shell environment.

### 7. `adb pull`

`adb pull` is used to copy files from the Android device or emulator to your local machine.

#### Usage

```bash
adb pull <remote-file-path> <local-path>
```

This command copies files from the specified path on the device to the local path on your computer.

#### Examples

- **Pull a file from the Android device:**

  ```bash
  adb pull /sdcard/myfile.txt ./app-test
  ```

  This command pulls `myfile.txt` from the device's `/sdcard/` directory to your desktop.

- **Pull an entire directory:**

  ```bash
  adb pull /sdcard/DCIM/ ./app-test
  ```

  This pulls the `/sdcard/DCIM/` directory from the device to your local `~/Pictures/` directory.

#### Common Uses of `adb pull`

- **Extract logs for debugging:**
  If you need to extract application logs stored in `/sdcard/` or `/data/log/`, you can use `adb pull` to retrieve them for analysis.
- **Backup files:**
  Use `adb pull` to back up personal files like photos and music from your device to your computer.

---

#### 8. `sdkmanager` for SDK Management

##### List Installed and Available SDK Packages

```bash
sdkmanager --list
```

##### Install SDK Platform or Tool

```bash
sdkmanager "platforms;android-30"
```

##### Update All SDK Packages

```bash
sdkmanager --update
```

##### Uninstall SDK Package

```bash
sdkmanager --uninstall "platforms;android-30"
```
