# Fastlane - Android Deployment Guide

This guide will help you set up Fastlane for your Android project to automate your build and deployment processes.

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

- **Google Credentials (Google Service Account) for Uploading to Google Play Store**

  - Select the correct project and permissions in the Google Cloud Console.

  - **Export your Google service account JSON file.**

## Setting Up Fastlane

Run the following command to initialize Fastlane in your project:

```bash
fastlane init
```

This command creates a `fastlane` directory in your project's root:

```plaintext
fastlane/
├── Appfile
└── Fastfile
```

## Configuring the Appfile

Add the following to your `Appfile`:

```ruby
json_key_file("path/to/your/play-store-credentials.json")

package_name("com.yourcompany.yourapp")
```

**Note:** Replace `"path/to/your/play-store-credentials.json"` with the actual path
to your Google service account JSON file,
and `"com.yourcompany.yourapp"` with your app's package name.

## Fetching Your App Metadata

Download all of your current Google Play Store metadata to `fastlane/metadata/android`:

```bash
fastlane supply init
```

**Note:** `supply` is an alias for `upload_to_play_store`.

## Building Your App

You can build your app using Gradle:

```bash
./gradlew assembleDebug

./gradlew assembleRelease
```

Fastlane can also handle building your app. Add the following lane to your `Fastfile`:

```ruby
lane :beta do
  gradle(task: "clean", project_dir: "android")

  # Adjust the `build_type` and `flavor` parameters as needed
  gradle(
    task: 'assemble', # build apk
    build_type: 'Release',
    project_dir: "android",
    print_command: true
  )

  # Additional steps can be added here
end
```

Run the lane:

```bash
fastlane beta
```

To view available parameters for the `gradle` action, run:

```bash
fastlane action gradle
```

## Uploading Your App

After building your app, you can upload it to the Google Play Store:

```ruby
lane :beta do
  upload_to_play_store(track: 'beta')
  slack(message: 'Successfully distributed a new beta build')
end
```

## Full Example

First, Add the fastlane plugin for versioning,
or create Pluginfile and add the plugin:

```bash
fastlane add_plugin versioning_android
```

```ruby
# Bulid apk for beta internal testing
lane :beta do
  ensure_git_status_clean

  # clean gradle build
  gradle(task: "clean", project_dir: "android")

  # Increment version code and version name
  android_set_version_name(version_name: '1.2.3')
  android_set_version_code(version_code: 1)

  # build apk
  gradle(
    task: 'assemble', # build apk
    build_type: 'Release',
    project_dir: "android",
    print_command: true
  )

  # upload to play store
  upload_to_play_store(track: 'beta')

  # send slack
  slack(message: 'Successfully distributed a new beta build')
end

# Buld aab(android app bundle) for production
lane :release do
  ensure_git_status_clean

  # clean gradle build
  gradle(task: "clean", project_dir: "android")

  # Increment version code and version name
  android_set_version_name(version_name: '1.2.3')
  android_set_version_code(version_code: 1)

  # build aab
  gradle(
    task: 'bundle', # bulid aab(android app bundle)
    build_type: 'Release',
    project_dir: "android",
    print_command: true
  )

  # upload to play store
  upload_to_play_store(track: 'beta')

  # send slack
  slack(message: 'Successfully distributed a new beta build')
end
```

## References

- [Fastlane Documentation](https://docs.fastlane.tools)

- [Fastlane Android Setup](https://docs.fastlane.tools/getting-started/android/setup)

- [Fastlane Android Deployment](https://docs.fastlane.tools/getting-started/android/beta-deployment)
