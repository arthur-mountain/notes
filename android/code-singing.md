# Android Code Signing(簽章/簽名)

在開發和發布 Android 應用程式時，對應用程式進行簽名是必不可少的步驟。

簽名可確保應用程式的完整性和安全性，並允許開發者發布更新。

## 為什麼需要簽名？

Android 系統要求所有的應用程式在安裝之前都必須進行數位簽名。這種機制可以：

- **驗證應用程式的來源**：確保應用程式由可信的開發者發布。

- **保護應用程式完整性**：防止應用程式在傳輸過程中被篡改。

- **允許應用程式更新**：只有使用相同簽名的應用程式才能覆蓋已安裝的版本。

## 什麼是 Keystore？

**Keystore** 是一個安全的容器，用於存儲您的加密密鑰（公鑰和私鑰）以及相關的證書。

它在應用程式簽名過程中扮演著關鍵角色。

透過 Keystore，您可以：

- **管理密鑰對**：生成、存儲和管理多個密鑰對。

- **保護私鑰**：私鑰被安全地存儲在 Keystore 中，防止未經授權的訪問。

- **簽署應用程式**：使用存儲在 Keystore 中的密鑰對應用程式進行數位簽名。

**重要性**：

- **唯一性**：每個開發者應該有自己唯一的 Keystore，用於簽署自己的應用程式。

- **持久性**：一旦應用程式發布，您應該使用相同的 Keystore 進行未來的更新，否則用戶將無法安裝更新。

## 生成 Keystore

### 步驟一：安裝 Java JDK

在生成 Keystore 之前，請確保已安裝 Java 開發工具包（JDK），因為我們需要使用其中的 `keytool` 工具。

### 步驟二：使用 `keytool` 生成 Keystore

打開 Terminal，輸入以下指令：

```bash
keytool \
  -genkey -v \
  -storetype JKS \
  -keyalg RSA \
  -keysize 2048 \
  -validity 10000 \
  -storepass $KEYSTORE_PASSWORD \
  -keypass $KEY_PASSWORD \
  -alias $KEY_ALIAS \
  -keystore release.keystore \
  -dname "CN=com.expo.your.android.package,OU=,O=,L=,S=,C=US"
```

#### 指令解析

- **`keytool`**：Java 提供的密鑰和證書管理工具。

- **`-genkey -v`**：生成一個新的密鑰對（包括公鑰和私鑰），`-v` 參數用於顯示詳細資訊。

- **`-storetype JKS`**：指定密鑰庫的類型為 JKS（Java KeyStore）。

- **`-keyalg RSA`**：設定密鑰的演算法為 RSA。

- **`-keysize 2048`**：設定密鑰的大小為 2048 位元。

- **`-validity 10000`**：設定證書的有效期為 10,000 天。

- **`-storepass $KEYSTORE_PASSWORD`**：設定密鑰庫的密碼，請替換為實際密碼。

- **`-keypass $KEY_PASSWORD`**：設定密鑰的密碼，請替換為實際密碼。

- **`-alias $KEY_ALIAS`**：為密鑰設定別名，方便日後引用。

- **`-keystore release.keystore`**：指定生成的密鑰庫檔案名稱。

- **`-dname`**：設定證書的識別名稱（Distinguished Name），包括：

  - `CN`（Common Name）：通常為您的應用程式包名。

  - `OU`（Organizational Unit）：組織單位，可留空。

  - `O`（Organization）：組織名稱，可留空。

  - `L`（Locality）：城市或地區，可留空。

  - `S`（State`）：州或省份，可留空。

  - `C`（Country）：國家代碼，如 `US`。

### 注意事項

- **替換佔位符**：在執行指令前，請將 `KEYSTORE_PASSWORD`、`KEY_PASSWORD`、`KEY_ALIAS` 和 `CN` 等佔位符替換為實際資訊。

- **安全性**：請妥善保管您的 Keystore 檔案和密碼。一旦遺失，您將無法更新已發布的應用程式。

- **備份**：建議將 Keystore 檔案備份至安全的位置。

## 使用 Keystore 簽署應用程式

### 步驟一：生成未簽名的 APK

在簽名之前，您需要生成未簽名的 APK。

通常可透過以下指令完成（根據您的建置工具，底下以 gradle 為例）：

```bash
./gradlew assembleRelease
```

### 步驟二：使用 `jarsigner` 簽名

使用以下指令對 APK 進行簽名：

```bash
jarsigner \
  -verbose \
  -keystore release.keystore \
  -storepass $KEYSTORE_PASSWORD \
  -keypass $KEY_PASSWORD \
  app-release-unsigned.apk \
  $KEY_ALIAS
```

#### 指令解析

- **`jarsigner`**：Java 提供的工具，用於簽署 JAR 或 APK 檔案。

- **`-verbose`**：顯示詳細的簽名過程。

- **`-keystore release.keystore`**：指定使用的 Keystore 檔案。

- **`-storepass $KEYSTORE_PASSWORD`**：Keystore 的密碼。

- **`-keypass $KEY_PASSWORD`**：密鑰的密碼。

- **`app-release-unsigned.apk`**：待簽名的 APK 檔案。

- **`$KEY_ALIAS`**：密鑰的別名。

### 步驟三：對齊 APK

簽名後，需使用 `zipalign` 工具對 APK 進行對齊，優化運行時得效率。

```bash
zipalign -v 4 app-release-unsigned.apk app-release.apk
```

- **`zipalign`**：Android SDK 提供的工具，用於對齊 APK。

- **`-v 4`**：指定對齊的字節邊界。

- **`app-release-unsigned.apk`**：簽名後的 APK。

- **`app-release.apk`**：對齊後的最終 APK。

### 步驟四：驗證簽名

最後，驗證 APK 是否正確簽名：

```bash
jarsigner -verify -verbose -certs app-release.apk

keytool -printcert -jarfile app-release.apk
```

## 發布應用程式

完成上述步驟後，`app-release.apk` 就是已簽名並對齊的 APK，可用於發布。

## 參考資料

- [Android 簽名指南](https://developer.android.com/studio/publish/app-signing)
- [Keytool 官方文件](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/keytool.html)
