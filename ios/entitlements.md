# Entitlements

Entitlements（權限配置）是 iOS 應用程序用來定義其特權和能力的一種機制。

以 `.entitlements` 文件的形式存在，包含了應用在運行時所需的各種權限，如推送通知、應用內購買、iCloud 存取等。

這些權限設定告訴系統應用可以執行哪些操作，並在需要時與 Apple 的服務進行協同工作。

## 一、什麼是 Entitlements

Entitlements 是一組鑰匙（Key）- 值（Value）對，用來申請應用的特定能力。

這些能力可能包括訪問某些系統級服務或執行特定的操作。

每個 entitlements 鍵代表一項特定的權限，並且只有當應用擁有相應的權限時才能夠執行該操作。

### 主要特點

1. **應用的身份標識**：每個應用的 entitlements 與其證書和 provisioning profile 綁定，以確保只有合法簽署的應用才能獲得相應權限。

2. **分發時校驗**：在應用分發和運行時，系統會校驗應用的簽名和 entitlements 是否一致，確保安全性。

3. **管理特殊權限**：包括背景執行、Apple Pay 支持、健康數據訪問等，都需要在 entitlements 中明確聲明。

## 二、Entitlements 文件結構

Entitlements 文件的基本結構是一個 XML 格式的字典，
其中每個鍵（Key）代表一項權限或能力，鍵名對應的值（Value）則是這項能力的配置。

範例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <dict>
        <key>aps-environment</key>
        <string>development</string>
        <key>com.apple.developer.in-app-payments</key>
        <array>
            <string>merchant.com.example.myapp</string>
        </array>
        <key>com.apple.developer.icloud-services</key>
        <array>
            <string>CloudDocuments</string>
            <string>CloudKit</string>
        </array>
        <key>com.apple.developer.healthkit</key>
        <true/>
        <key>keychain-access-groups</key>
        <array>
            <string>$(AppIdentifierPrefix)com.example.myapp</string>
        </array>
    </dict>
</plist>
```

## 三、常見的 Entitlements 鍵

### 1. 推送通知（Push Notifications）

- **`aps-environment`**：指定應用使用推送通知的環境，可能值有 `development`（開發環境）或 `production`（生產環境）。

### 2. iCloud 支持

- **`com.apple.developer.icloud-services`**：啟用 iCloud 功能，如 `CloudDocuments`、`CloudKit`。

- **`com.apple.developer.ubiquity-kvstore-identifier`**：應用的 iCloud 存取標識符，用於 KVS（Key-Value Storage）存儲。

### 3. 應用內購（In-App Purchase）

- **`com.apple.developer.in-app-payments`**：應用內購功能，通常需要指定一個或多個商戶標識符。

### 4. HealthKit

- **`com.apple.developer.healthkit`**：啟用應用訪問 HealthKit 數據的權限。

### 5. App Groups

- **`com.apple.security.application-groups`**：允許多個應用之間共享數據的應用組標識符。

### 6. Keychain 共享

- **`keychain-access-groups`**：允許應用在多個應用之間共享 Keychain 資料的組標識符。

### 7. Background Modes

- **`UIBackgroundModes`**：定義應用允許的背景執行模式，如音樂播放、VoIP、位置更新等。

### 8. Sign in with Apple

- **`com.apple.developer.applesignin`**：配置應用使用 “Sign in with Apple” 功能。

### 9. Associated Domains

- **`com.apple.developer.associated-domains`**：配置應用與特定網域相關聯，支持深度連結和 Universal Links 功能。

## 四、設置 Entitlements 的流程

1. **在 Xcode 中設置**：

   - 打開 Xcode，選擇 Target，進入 `Signing & Capabilities` 標籤頁。

   - 點擊 `+ Capability` 按鈕，選擇所需的權限，如推送通知、iCloud 等。

   - Xcode 會自動在項目中生成或更新 `.entitlements` 文件，並在需要時修改 `Info.plist` 文件。

2. **更新 Provisioning Profile**：

   - 某些 entitlements（如推送通知、iCloud 等）需要在 Apple 開發者網站上對應的 App ID 中啟用相應的服務。

   - 啟用後，下載更新的 Provisioning Profile 並配置到 Xcode 中。

3. **調整和測試**：

   - 在測試環境中，確保使用正確的 entitlements 和 Provisioning Profile。

   - 測試推送通知、iCloud 功能等，確認應用能夠正常獲取和使用這些權限。

## 五、注意事項

1. **正確配置權限**：

   - 在開發者網站上啟用與 entitlements 文件中鍵對應的服務，並下載更新的 Provisioning Profile。

2. **簽名一致性**：

   - 應用簽名（Code Signing）、Provisioning Profile 和 entitlements 必須保持一致，否則應用可能無法正確運行。

3. **安全性考量**：

   - Entitlements 是應用的敏感配置，不要將無關的權限添加到 entitlements 文件中。

   - 如果應用使用了過多的權限，可能會被拒絕上架，或引起用戶的安全擔憂。

4. **檢查 entitlements**：

   - 可以使用 `codesign` 工具檢查已簽名的應用程序中 entitlements 是否配置正確：

     ```bash
     codesign -d --entitlements :- YourApp.app
     ```

## 六、參考資料

[Apple Documentation](https://developer.apple.com/documentation/bundleresources/entitlements)
