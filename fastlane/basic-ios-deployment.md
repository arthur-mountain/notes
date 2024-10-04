# Fastlane - iOS Deployment Guide

This guide will help you set up Fastlane
for your iOS project to automate your build and deployment processes.

## Prerequisites

- **Ruby 2.5 or Higher**

  - Check your Ruby version:

    ```bash
    ruby --version
    ```

- **Bundler (Recommended)**

  - Install Bundler:

    ```bash
    gem install bundler
    ```

  - Create a `Gemfile` in your project's root directory and add:

    ```ruby
    source "https://rubygems.org"
    gem "fastlane"
    ```

- **Xcode**

  - Ensure Xcode is installed:

    ```bash
    xcode-select --install
    ```

## Fastlane Init

Before running `fastlane init`, decide how you want to create your app on App Store Connect:

- **Automated App Creation via Fastlane**

  - [create_app_online](https://docs.fastlane.tools/actions/create_app_online) for more details.

  - Setup required `PRODUCE_xxx` env vars for create_app_online action.

    ```bash
    export PRODUCE_COMPANY_NAME="Your Company Name"
    # ... other PRODUCE_xxx env vars
    ```

- **Manual App Creation**

  - Alternatively, create your app manually on App Store Connect.

After setting up, run:

```bash
fastlane init
```

This command creates a `fastlane` directory in your project's root:

```plaintext
fastlane/
├── Appfile
└── Fastfile
```

## Appfile

add the following to your `Appfile`:

```ruby
app_identifier("com.example.MyApp")
team_id("YOUR_TEAM_ID")
# ...others
```

## Code Signing

Prepare your code signing files and configure your project settings.

- **Prepare Code Signing Files**

  - Ensure you have the necessary certificates and provisioning profiles in your local environment.

  - [ios code signing note](../ios/code-signing.md)

- **Project Configuration**

  - Update your `project.pbxproj` and `exportOptions.plist` files.

  - Set the following configuration keys:

    - `DEVELOPMENT_TEAM`=YOUR_TEAM_ID

    - `CODE_SIGN_IDENTITY`=YOUR_CODE_SIGN_IDENTITY

    - `PROVISIONING_PROFILE`=YOUR_PROVISIONING_PROFILE_UUID

    - `PROVISIONING_PROFILE_SPECIFIER`=YOUR_PROVISIONING_PROFILE_SPECIFIER

- **Fastlane Code Signing Actions**

  - [`match`](https://docs.fastlane.tools/actions/match): Manages code signing certificates and provisioning profiles.

  - [`sigh`](https://docs.fastlane.tools/actions/sigh): Downloads provisioning profiles.

  - [`cert`](https://docs.fastlane.tools/actions/cert): Manages code signing certificates.

## Building Your App

You can build your app using Xcode or Fastlane.

- **Using Xcode Command Line**

  ```bash
  xcodebuild \
    -scheme "MyApp" \
    -workspace "MyApp.xcworkspace" \
    -destination 'generic/platform=iOS' \
    build
  ```

- **Using Fastlane**

  ```ruby
  lane :build do
    build_app(scheme: "MyApp")
  end
  ```

  Run the lane:

  ```bash
  fastlane build
  ```

  To view available parameters for `build_app`, run:

  ```bash
  fastlane action build_app
  ```

## Uploading Your App

After building your app, you can upload it to the `testflight` or `apple store`:

```ruby
lane :beta do
  sync_code_signing(type: "appstore")

  build_app(scheme: "MyApp")

  upload_to_testflight # or upload_to_app_store

  slack(message: "Successfully distributed a new beta build")
end
```

## Full Example

```ruby
lane :beta do
  ensure_git_status_clean

  # Select specific Xcode version (optional)
  xcodes(select_for_current_build_only: true)

  # Increment version number
  increment_version_number(
    xcodeproj: "MyApp.xcodeproj",
    version_number: '1.2.3'
  )

  # Increment build number
  increment_build_number(
    xcodeproj: "MyApp.xcodeproj",
    build_number: latest_testflight_build_number + 1
  )

  # Setup keychain in CI environment this run on CI only
  setup_ci

  # Sync code signing (alias for match)
  sync_code_signing(type: "appstore")

  # Build the app
  build_app(scheme: "MyApp")

  # App Store Connect API Key configuration
  app_store_connect_api_key(
    key_id: "YOUR_KEY_ID",
    issuer_id: "YOUR_ISSUER_ID",
    key_filepath: "path/to/your-key.p8",
    duration: 1200,
    in_house: false
  )

  # Upload to TestFlight or App Store
  upload_to_testflight # or upload_to_app_store

  # Download dSYM files
  download_dsyms

  # Clean build artifacts
  clean_build_artifacts

  # Notify via Slack
  slack(message: "Successfully distributed a new beta build")
end
```

**Note:** Remember replace all of placeholder to your actual value.

## References

- [Fastlane Documentation](https://docs.fastlane.tools)

- [Fastlane Code Signing](https://docs.fastlane.tools/codesigning/getting-started)

- [Fastlane iOS Deployment](https://docs.fastlane.tools/getting-started/ios/beta-deployment)

- [Fastlane Available Actions](https://docs.fastlane.tools/actions)
