## Ios Development CI/CD in GitHub Actions

### 步驟

1. **準備證書和描述檔：**

   簽署流程涉及儲存證書和描述檔，將它們傳送到 Runner，然後導入至 Runner 的金鑰鏈，並在構建過程中使用。

2. **創建 Secrets：**

   - 將證書和描述檔轉換為 Base64 格式。

     ```bash
     # 將 p12 檔案轉換為 Base64 格式
     base64 -i BUILD_CERTIFICATE.p12

     # 從 Apple Developer Portal 下載描述檔後轉換為 Base64 格式
     base64 -i PROVISIONING_PROFILE.mobileprovision
     ```

   - 將它們保存為 GitHub Secrets（如 `BUILD_CERTIFICATE_BASE64`、`P12_PASSWORD`、`BUILD_PROVISION_PROFILE_BASE64`、`KEYCHAIN_PASSWORD`）。

3. **範例：**

   ```yaml
   name: iOS Build and Sign

   on:
     push:
       branches:
         - main
     pull_request:
       branches:
         - main

   jobs:
     build_with_signing:
       runs-on: macos-latest

       steps:
         - name: Checkout code
           uses: actions/checkout@v4

         - name: Setup environment variables
           run: |
             echo "CERTIFICATE_PATH=$RUNNER_TEMP/build_certificate.p12" >> $GITHUB_ENV

             echo "PROVISION_PATH=$RUNNER_TEMP/build.mobileprovision" >> $GITHUB_ENV

             echo "KEYCHAIN_PATH=$RUNNER_TEMP/signing.keychain-db" >> $GITHUB_ENV

         - name: Setup Apple certificate and provisioning profile
           env:
             BUILD_CERTIFICATE_BASE64: ${{ secrets.BUILD_CERTIFICATE_BASE64 }}
             P12_PASSWORD: ${{ secrets.P12_PASSWORD }}
             BUILD_PROVISION_PROFILE_BASE64: ${{ secrets.BUILD_PROVISION_PROFILE_BASE64 }}
             KEYCHAIN_PASSWORD: ${{ secrets.KEYCHAIN_PASSWORD }}
           run: |
             # 從 secrets 中解碼並導入證書和描述檔
             echo -n "$BUILD_CERTIFICATE_BASE64" | base64 --decode -o "$CERTIFICATE_PATH"
             echo -n "$BUILD_PROVISION_PROFILE_BASE64" | base64 --decode -o "$PROVISION_PATH"

             # 創建臨時金鑰
             security create-keychain -p "$KEYCHAIN_PASSWORD" "$KEYCHAIN_PATH"
             security set-keychain-settings -lut 21600 "$KEYCHAIN_PATH"
             security unlock-keychain -p "$KEYCHAIN_PASSWORD" "$KEYCHAIN_PATH"

             # 將證書導入金鑰
             security import "$CERTIFICATE_PATH" -P "$P12_PASSWORD" -A -t cert -f pkcs12 -k "$KEYCHAIN_PATH"
             security set-key-partition-list -S apple-tool:,apple: -k "$KEYCHAIN_PASSWORD" "$KEYCHAIN_PATH"
             security list-keychain -d user -s "$KEYCHAIN_PATH"

             # 將描述檔複製到指定目錄
             mkdir -p ~/Library/MobileDevice/Provisioning\ Profiles
             cp "$PROVISION_PATH" ~/Library/MobileDevice/Provisioning\ Profiles

         # 安裝 CocoaPods 依賴
         - name: Install dependencies
           run: pod install

         # Build application
         - name: Build app
           run: |
             xrun xcodebuild clean build \
               -workspace <YOUR_WORKSPACE> \
               -scheme <YOUR_SCHEME> \
               -sdk <YOUR_SDK> \
               -configuration <Debug | Relase> \
               -archivePath "$RUNNER_TEMP/MyApp.xcarchive" \
               DEVELOPMENT_TEAM=<YOUR_TEAM_ID> \ # 根據需要替換 team ID
               CODE_SIGN_IDENTITY=<YOUR_CODE_SIGN_IDENTITY> \
               PROVISIONING_PROFILE_SPECIFIER=<YOUR_PROVISIONING_PROFILE_SPECIFIER>

         # 導出 build result
         - name: Export .ipa file
           run: |
             xrun xcodebuild -exportArchive \
               -archivePath "$RUNNER_TEMP/MyApp.xcarchive" \
               -exportPath "$RUNNER_TEMP" \
               -exportOptionsPlist <YOUR_EXPORT_OPTIONS_PLIST>

         # 上傳 ipa
         - name: Upload artifact
           uses: actions/upload-artifact@v3
           with:
             name: MyApp
             path: "$RUNNER_TEMP/MyApp.ipa"

         # 清理自托管 Runner 的環境
         - name: Clean up keychain and profile
           if: ${{ always() }}
           run: |
             security delete-keychain "$KEYCHAIN_PATH"
             rm -f "$CERTIFICATE_PATH" "$PROVISION_PATH"
             rm -f "~/Library/MobileDevice/Provisioning\ Profiles/$(basename $PROVISION_PATH)"
   ```

## 相關資源

[GitHub 文檔](https://docs.github.com/en/actions/use-cases-and-examples/deploying/installing-an-apple-certificate-on-macos-runners-for-xcode-development).
