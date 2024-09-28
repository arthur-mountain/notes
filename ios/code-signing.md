# iOS Code Signing Guide

This guide provides a comprehensive overview of the files required for iOS app deployment,

their uses, and how to generate and manage them using command-line interface (CLI) operations.

It outlines the step-by-step process of deploying an iOS app, including code signing, packaging, and submission to the App Store.

---

## 一、 Required Files for iOS App Deployment

1. **Certificate Files**

   - **.csr** (Certificate Signing Request)

   - **.cer** (Certificate)

   - **.p12** (Personal Information Exchange)

   - **.pem** (Privacy-Enhanced Mail)

   - **.p8** (APNs Auth Key)

2. **Provisioning Profile**

   - **.mobileprovision**

3. **Application Files**

   - **.ipa** (iOS App Package)

4. **Other Necessary Resources**

   - **App Icons and Screenshots**

   - **Metadata required by App Store Connect**

---

## 二、 Introduction, Uses, and CLI Operations for Each File

### 1. `.csr` (Certificate Signing Request)

#### **Definition**

A **Certificate Signing Request (CSR)** is a file used to request a certificate from a Certificate Authority (CA).

It contains the applicant's public key and identification information.

#### **Uses**

- Submitted to Apple to apply for `development` or `distribution` certificates.

#### **Generation Process and CLI Operations**

1. **Generate a private key and CSR file**

   Using `Keychain Access` or via CLI:

   ```bash
   # Generate a 2048-bit RSA private key
   openssl genrsa -out mykey.key 2048

   # Generate CSR using the private key
   openssl req -new \
    -key mykey.key \
    -out CertificateSigningRequest.certSigningRequest \
    -subj "/emailAddress=your_email@example.com/CN=Your Name/C=TW"
   ```

   **Parameter Explanations:**

   - `openssl genrsa`: Generates an RSA private key.

   - `-out mykey.key`: Output private key file name.

   - `2048`: Key length.

   - `openssl req -new`: Creates a new certificate request.

   - `-key mykey.key`: Specifies the private key file.

   - `-out CertificateSigningRequest.certSigningRequest`: Output CSR file name.

   - `-subj`: Subject information (email, common name, country).

2. **Submit the `.csr` file**

   - Log in to [Apple Developer](https://developer.apple.com/account).

   - Navigate to "Certificates, Identifiers & Profiles".

   - Choose the certificate type and upload the `.csr` file.

### 2. `.cer` (Certificate)

#### **Definition**

A **Certificate** issued and signed by Apple, used to confirm the developer's identity and the legitimacy of their public key.

#### **Uses**

- Used in Xcode to sign applications, ensuring the app comes from a legitimate developer.

#### **Generation Process and CLI Operations**

1. **Download the `.cer` file**

   - From Apple Developer after certificate approval.

2. **Import the `.cer` file into Keychain Access**

   ```bash
   security import /path/to/your_certificate.cer -k ~/Library/Keychains/login.keychain-db
   ```

   **Parameter Explanations:**

   - `security import`: Tool to import certificates or keys.

   - `-k`: Specifies the keychain to import into.

### 3. `.p12` File

#### **Definition**

A **Personal Information Exchange** file that contains the certificate and private key, used for secure storage and transmission.

#### **Uses**

- Used in Xcode to sign applications.

#### **Generation Process and CLI Operations**

1. **Export the `.p12` file from Keychain Access**

   Using `Keychain Access` or via CLI:

   ```bash
   security export \
    -k ~/Library/Keychains/login.keychain-db \
    -t identities \
    -f pkcs12 \
    -P export_password \
    -o /path/to/exported_certificate.p12
   ```

   **Parameter Explanations:**

   - `security export`: Exports items from a keychain.

   - `-t identities`: Exports certificates and private keys.

   - `-f pkcs12`: Export format.

   - `-P`: Export password.

   - `-o`: Output file path.

   **Note:** CLI export can be complex; GUI is recommended.

### 4. `.pem` File

#### **Definition**

**Privacy-Enhanced Mail** format, used to store certificates or private keys.

#### **Uses**

- Required by some services needing `.pem` formatted files.

#### **Generation Process and CLI Operations**

1. **Convert `.p12` to `.pem`**

   ```bash
   openssl pkcs12 \
    -in certificate.p12 \
    -out certificate.pem \
    -nodes \
    -password pass:export_password
   ```

   **Parameter Explanations:**

   - `openssl pkcs12`: Processes PKCS#12 files.

   - `-nodes`: No DES encryption on the output.

   - `-password`: Password for the `.p12` file.

### 5. `.p8` File

#### **Definition**

**APNs Auth Key**, used for authentication with the Apple Push Notification service (APNs).

#### **Uses**

- Set up push notification services.

#### **Generation Process**

1. **Create an APNs Auth Key**

   - Log in to Apple Developer.

   - Go to "Certificates, Identifiers & Profiles" > "Keys".

   - Click "+" to create a new key.

   - Enable "Apple Push Notifications service (APNs)".

   - Download the `.p8` file.

   **Note:** The `.p8` file can only be downloaded once.

### 6. `.mobileprovision` File

#### **Definition**

A **Provisioning Profile** authorizes the app to run on specific devices or be distributed in a specific way,
containing App ID, certificates, permissions, etc.

#### **Uses**

- **Development Stage**

  - **Purpose**: Development and testing on specified devices.
  - **Profile Type**: Development
  - **`method` Value**: `development`

- **Publishing Stage (App Store)**

  - **Purpose**: Submit app to the App Store.
  - **Profile Type**: App Store
  - **`method` Value**: `app-store`

- **Ad Hoc Distribution**

  - **Purpose**: Distribute to specific testers.
  - **Profile Type**: Ad Hoc
  - **`method` Value**: `ad-hoc`

- **Enterprise Distribution**

  - **Purpose**: Internal enterprise distribution.
  - **Profile Type**: In-House
  - **`method` Value**: `enterprise`

#### **Generation Process**

1. **Create an App ID**

   - Log in to Apple Developer.

   - Navigate to "Certificates, Identifiers & Profiles" > "Identifiers".

   - Click "+" to create a new App ID.

   - Enter required details.

2. **Create a Provisioning Profile**

   - Go to "Profiles" > "+".

   - Select the profile type based on your distribution method.

   - Associate it with your App ID and certificates.

   - Download the `.mobileprovision` file to `~/Library/MobileDevice/Provisioning\ Profiles`.

### 7. `.ipa` File

#### **Definition**

An **iOS App Package** is the packaging format for iOS applications.

#### **Uses**

- Deploy the app to devices or upload it to the App Store.

#### **Generation Process and CLI Operations**

1. **Configure Code Signing in Xcode**

   - Ensure the Bundle Identifier matches the App ID.

   - Select your Team in "Signing & Capabilities".

2. **Use `xcodebuild` to Package the App**

   ```bash
   # Step 1: Build and archive
   xcodebuild -workspace YourApp.xcworkspace \
   -scheme YourScheme \
   -configuration Release \
   -archivePath ./build/YourApp.xcarchive \
   archive

   # Step 2: Export the .ipa
   xcodebuild -exportArchive \
   -archivePath ./build/YourApp.xcarchive \
   -exportPath ./build/YourApp \
   -exportOptionsPlist ExportOptions.plist
   ```

   **Parameter Explanations:**

   - `-workspace`: Specify the workspace file.

   - `-scheme`: Scheme to build.

   - `-configuration`: Build configuration.

   - `-archivePath`: Output path for the archive.

   - `-exportPath`: Output path for the `.ipa`.

   - `-exportOptionsPlist`: Export options file.

3. **Create `ExportOptions.plist`**

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
   <plist version="1.0">
   <dict>
       <key>method</key>
       <string>app-store</string> <!-- Change as needed -->
       <key>teamID</key>
       <string>YourTeamID</string>
       <key>provisioningProfiles</key>
       <dict>
           <key>com.example.app</key>
           <string>YourProvisioningProfileName</string>
       </dict>
       <key>compileBitcode</key>
       <false/>
   </dict>
   </plist>
   ```

   **Parameter Explanations:**

   - `<key>method</key>`: `app-store`, `ad-hoc`, `enterprise`, `development`.

   - `<key>teamID</key>`: Your Apple Developer Team ID.

   - `<key>provisioningProfiles</key>`: Maps Bundle Identifier to Provisioning Profile name.

   - `<key>compileBitcode</key>`: Whether to compile Bitcode.

---

## 三、 iOS App Deployment Process and File Relationships

### **Step 1: Prepare Certificates and Private Keys**

1. **Generate the CSR file**

   - Follow the steps in section 二.1.

2. **Apply for and download certificates from Apple Developer**

   - Submit the `.csr` file and download the `.cer` file.

3. **Import the certificate and export the `.p12` file**

   - Use `security import` to import the `.cer` file.

   - Export the `.p12` file using Keychain Access.

### **Step 2: Create App ID and Provisioning Profile**

1. **Create an App ID**

   - Refer to section 二.6.

2. **Create a Provisioning Profile**

   - Refer to section 二.6.

3. **Install the Provisioning Profile**

   - Use the CLI commands provided.

### **Step 3: Configure the Xcode Project**

1. **Open project settings**

   - Go to "Signing & Capabilities".

2. **Select Team and Signing Options**

   - Use automatic signing or manually select certificates and profiles.

3. **Configure Capabilities**

   - Add necessary app capabilities.

### **Step 4: Package and Sign the App**

1. **Build and Archive**

   - Use `xcodebuild` as described in section 二.7.

2. **Verify Code Signing**

   ```bash
   codesign -dv --verbose=4 /path/to/YourApp.app
   ```

3. **View the Provisioning Profile**

   ```bash
   security cms -D -i /path/to/YourApp.app/embedded.mobileprovision
   ```

### **Step 5: Configure the App in App Store Connect**

1. **Log in to [App Store Connect](https://appstoreconnect.apple.com)**

2. **Create a New App**

   - Provide app name, language, Bundle ID, SKU, etc.

3. **Fill in App Information**

   - Name, description, keywords, privacy policy URL.

4. **Upload Screenshots**

   - Ensure they meet Apple's requirements.

5. **Configure Version Information**

   - Version number, release notes.

6. **Fill in App Privacy Information**

   - Describe data collection and usage.

7. **Submit the App for Review**

   - Wait for Apple's approval.

**Upload the App to App Store Connect**

```bash
xcrun altool --upload-app \
  -f /path/to/YourApp.ipa \
  -t ios \
  -u your_email@example.com \
  -p your_app_specific_password
```

**Parameter Explanations:**

- `xcrun altool`: Tool to upload apps.

- `--upload-app`: Indicates an upload action.

- `-f`: Specifies the `.ipa` file.

- `-t ios`: App type.

- `-u`: Apple ID email.

- `-p`: App-specific password.
