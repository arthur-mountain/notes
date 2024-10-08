# EAS Credentials

用於管理和處理應用程式的憑證（credentials），這些憑證包括簽名證書、推送通知密鑰等。

e.g.

在 Android 平台上需要 keystore, etc.

在 iOS 平台上，需要 certificate, provisioning profile, p12, p8, etc.

**EAS Credentials** 為開發者提供了一個集中化、簡化的方式來管理這些憑證。

它可以自動處理憑證的生成、存儲和更新，也可以手動上傳現有的憑證，但減少了許多手動管理的繁瑣，提高了開發效率和安全性。

---

## 管理 Credentials 的方法

### 使用 EAS CLI 管理憑證

EAS CLI 提供了一系列命令，用於管理憑證的生成、上傳和更新。例如：

```bash
# 查看當前項目的憑證狀態
eas credentials

# 上傳自定義的 Android 簽名證書
eas credentials --platform android --upload-keystore /path/to/keystore.jks

# 更新 iOS 的推送通知密鑰
eas credentials --platform ios --update-push-key /path/to/key.p8
```

### 配置文件管理

EAS 使用配置文件來管理不同環境下的憑證設定。這些配置文件通常包含應用的識別信息、憑證路徑和其他相關設定，確保在不同部署環境中使用正確的憑證。

## 簡單範例

以下是一個使用 EAS 管理 Android 簽名證書的簡單範例：

### 生成和上傳簽名證書

```bash
# 使用 EAS CLI 自動生成簽名證書
eas build:configure

# 上傳自定義的簽名證書
eas credentials --platform android --upload-keystore ./path/to/keystore.jks
```

