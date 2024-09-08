### iOS Development with Xcode CLI - Summary and Organization

This guide provides an overview of using Xcode’s command-line tools (`xcodebuild` and `xcrun`) for building and managing iOS projects.

---

### 1. **`xcodebuild` Basics**

#### Xcode Setup

- **Check Xcode Installation:**

  ```bash
  xcode-select --install
  ```

- **List Available SDKs:**

  ```bash
  xcodebuild -showsdks
  ```

- **Show Project Schemes and Targets:**

  ```bash
  xcodebuild -list
  ```

---

### 2. **Building iOS Apps**

#### Build Commands

- **Build a Debug Version for Simulator:**

  ```bash
  xcodebuild -scheme <scheme-name> -sdk iphonesimulator -configuration Debug
  ```

  Example:

  ```bash
  xcodebuild -scheme MyApp -sdk iphonesimulator -configuration Debug
  ```

- **Build a Release Version for a Real Device:**

  ```bash
  xcodebuild -scheme <scheme-name> -sdk iphoneos -configuration Release
  ```

  Example:

  ```bash
  xcodebuild -scheme MyApp -sdk iphoneos -configuration Release
  ```

- **Specify Output Directory:**

  ```bash
  xcodebuild -scheme MyApp -sdk iphonesimulator -configuration Debug -derivedDataPath ./build
  ```

---

### 3. **Creating and Exporting `.ipa` Files**

#### Create `.xcarchive`

- **Archive a Project:**

  ```bash
  xcodebuild -scheme <scheme-name> -sdk iphoneos -configuration Release archive -archivePath <path-to-output>
  ```

  Example:

  ```bash
  xcodebuild -scheme MyApp -sdk iphoneos -configuration Release archive -archivePath ./build/MyApp.xcarchive
  ```

#### Export `.ipa` File

- **Export from Archive:**

  ```bash
  xcodebuild -exportArchive -archivePath <path-to-xcarchive> -exportPath <output-path> -exportOptionsPlist <plist-file>
  ```

  Example:

  ```bash
  xcodebuild -exportArchive -archivePath ./build/MyApp.xcarchive -exportPath ./build/MyApp.ipa -exportOptionsPlist ExportOptions.plist
  ```

- **Example `ExportOptions.plist`:**

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <plist version="1.0">
  <dict>
      <key>method</key>
      <string>ad-hoc</string>
      <key>signingStyle</key>
      <string>manual</string>
      <key>provisioningProfiles</key>
      <dict>
          <key>com.example.myapp</key>
          <string>MyApp AdHoc Provisioning</string>
      </dict>
  </dict>
  </plist>
  ```

---

### 4. **Testing**

#### Running Unit Tests

- **Execute Unit Tests on a Simulator:**

  ```bash
  xcodebuild test -scheme <scheme-name> -destination 'platform=iOS Simulator,name=iPhone 11'
  ```

#### Viewing Test Results

- **Generate Test Results in a Specific Path:**

  ```bash
  xcodebuild test -scheme MyApp -destination 'platform=iOS Simulator,name=iPhone 11' -resultBundlePath ./test-results
  ```

---

### 5. **Useful `xcodebuild` Commands**

- **Clean the Project:**

  ```bash
  xcodebuild clean -scheme <scheme-name>
  ```

- **Show Build Settings:**

  ```bash
  xcodebuild -showBuildSettings
  ```

---

### 6. **`xcrun` Commands**

#### Finding and Running Xcode Tools

- **Find the Path of a Tool:**

  ```bash
  xcrun --find xcodebuild
  ```

- **Run an Xcode Tool (`xcodebuild`):**

  ```bash
  xcrun xcodebuild -scheme <scheme-name> build
  ```

- **Package an IPA File (legacy approach):**

  ```bash
  xcrun -sdk iphoneos PackageApplication -v <app-path> -o <output-path>
  ```

---

### 7. **Differences Between `xcodebuild` and `xcrun`**

- **`xcodebuild`**:
  A core tool for building, testing, and packaging iOS/macOS applications. It handles the full build process, from compiling the code to creating archives and `.ipa` files, and it’s commonly used to run tests and clean up build files.

  **Common Functions**:

  - Building applications.
  - Running tests.
  - Generating `.xcarchive` and `.ipa` files for release.
  - Cleaning up intermediate build files.

  **Example Commands**:

  ```bash
  xcodebuild -scheme MyApp -configuration Release build
  ```

  ```bash
  xcodebuild test -scheme MyApp -destination 'platform=iOS Simulator,name=iPhone 11'
  ```

  ```bash
  xcodebuild -exportArchive -archivePath MyApp.xcarchive -exportPath MyApp.ipa -exportOptionsPlist ExportOptions.plist
  ```

- **`xcrun`**:
  A command-line utility to find and execute Xcode-related tools without needing to specify their full paths. It helps run tools like `xcodebuild`, `simctl`, and `instruments` by automatically locating the active Xcode’s toolchain.

  **Common Functions**:

  - Finding and running Xcode-related tools.
  - Managing Xcode simulators using `simctl`.
  - Ensuring the correct version of Xcode tools is being used in multi-Xcode environments.

  **Example Commands**:

  ```bash
  xcrun --find xcodebuild
  ```

  ```bash
  xcrun simctl list
  ```

### **Summary**

- **`xcodebuild`**: A direct tool for managing iOS/macOS project builds and testing. Used for performing detailed operations like building, cleaning, archiving, and testing an app.

- **`xcrun`**: A helper tool used to find and run various Xcode-related tools. It doesn't build or test by itself but assists in running tools like `xcodebuild` in a streamlined manner, especially useful in multi-Xcode environments.
