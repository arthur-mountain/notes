# Fastlane

automate your development and release process for iOS and Android apps

It handles all tedious tasks, like generating screenshots, dealing with code signing, and releasing your application.

You can start by creating a Fastfile file in your repository,
here’s one that defined the 2 different lanes,
one for beta deployment, one for App Store. To release your app in the App Store, all you have to do is process:

```ruby
lane :beta do
  increment_build_number
  build_app
  upload_to_testflight
end

lane :release do
  capture_screenshots
  build_app
  upload_to_app_store       # Upload the screenshots and the binary to iTunes
  slack                     # Let your team-mates know the new version is live
end
```

run with

```bash
fastlane release
```
