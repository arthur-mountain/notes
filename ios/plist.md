# Plist

Property List，簡稱 **plist**，是 Apple 生態系統中常用的一種文件格式，用於儲存結構化的數據。

它廣泛應用於 iOS 和 macOS 的應用開發中，用來存儲配置、設置和其他靜態數據。

## 一、數據結構

plist 文件採用鍵值對（Key-Value）的形式，適合儲存以下基本數據類型：

- **字串（String）**：文字數據。

- **數字（Number）**：整數或浮點數，儲存數值數據。

- **布林值（Boolean）**：`true` 或 `false`，用於存儲邏輯狀態。

- **日期（Date）**：儲存時間和日期信息，通常採用 ISO 8601 格式。

- **資料（Data）**：二進制數據，常用於存儲圖片或其他非文本的數據。

- **陣列（Array）**：有序的數據集合，包含多個子元素。

- **字典（Dictionary）**：無序的鍵值對集合，用於存儲復雜的結構化數據。

plist 文件可以採用 **XML** 或 **二進制** 格式：

- **XML 格式**：便於人類閱讀和手動編輯，常在開發過程中使用。

- **二進制格式**：更緊湊，讀寫性能更佳，適合應用部署時使用。

## 二、plist 文件的用途

### 1. 應用基本配置（Info.plist）

每個 iOS 應用至少包含一個 `Info.plist` 文件，用於存儲應用的基本配置信息，例如：

- **應用名稱（CFBundleName）**：顯示在主屏幕和系統中。

- **版本號（CFBundleShortVersionString）**：應用的公開版本號。

- **應用標識符（CFBundleIdentifier）**：應用的唯一標識符，用於區分不同應用。

- **支援的設備方向（UISupportedInterfaceOrientations）**：設置應用支持的屏幕方向。

- **隱私權使用說明**：描述應用為什麼需要訪問用戶的隱私權限，如相機（`NSCameraUsageDescription`）、麥克風（`NSMicrophoneUsageDescription`）、定位服務（`NSLocationWhenInUseUsageDescription`）等。

### 2. 儲存靜態數據

開發者可以創建自定義的 plist 文件，用於存儲應用所需的靜態配置數據，例如：

- **默認設定**：如應用的預設語言、主題。

- **應用常量**：如 API 地址、版本更新提示等。

- **配置列表**：如菜單項、設置選項等。

### 3. 本地化設定

開發者可以針對不同語言和地區創建本地化的 `Info.plist` 文件，通常存放在 `*.lproj` 目錄中，為用戶提供多語言支持。

這些本地化文件通常僅包含需要本地化的鍵值對，例如應用名稱、使用說明等。

### 4. 偏好設定（NSUserDefaults）

開發者可以使用 `NSUserDefaults` API 來訪問應用的偏好設置，它背後是以 plist 文件的形式存儲的，用於保存應用運行時的配置信息，如用戶設置、登錄狀態等。

## 三、plist 文件的結構與配置內容

### 1. 基本結構

一個 plist 文件通常以 `<plist>` 標籤開始和結束，內部包含了數據的層級結構。

根元素通常是 **字典（Dictionary）** 或 **陣列（Array）**，根據需求來組織更複雜的數據結構。

#### 範例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <dict>
        <key>AppName</key>
        <string>我的應用程式</string>
        <key>Version</key>
        <string>1.0</string>
        <key>Features</key>
        <array>
            <string>登入</string>
            <string>註冊</string>
            <string>設定</string>
        </array>
        <key>IsEnabled</key>
        <true/>
    </dict>
</plist>
```

### 2. 常見的配置內容

以下是 `Info.plist` 中一些常見的鍵及其用途：

- **`CFBundleDisplayName`**：應用顯示名稱，通常比 `CFBundleName` 更短，顯示在主屏幕圖標下方。

- **`UIBackgroundModes`**：應用可以在後台執行的模式，例如音樂播放、位置更新等。

- **`NSAppTransportSecurity`**：應用網絡傳輸安全設置，如允許 HTTP 連接等。

- **`UIRequiredDeviceCapabilities`**：指定應用所需的硬體功能，如相機、陀螺儀等。

- **`UILaunchStoryboardName`**：啟動畫面的故事板名稱。

- **`UIApplicationShortcutItems`**：設置主屏幕上的快捷操作。

### 3. 自定義配置

開發者可以在 plist 文件中建立自定義的 key-value pairs，以滿足應用的特殊需求，例如：

```xml
<key>APIEndpoint</key>
<string>https://api.example.com</string>
<key>EnableLogging</key>
<true/>
<key>SupportedLanguages</key>
<array>
    <string>en</string>
    <string>zh</string>
    <string>ja</string>
</array>
```

自定義的配置鍵應該以清晰的命名約定來命名，避免與系統保留的鍵名衝突。

## 四、編輯與管理工具

### 1. Xcode

Xcode 提供了直觀的 plist 編輯器，可以通過圖形化界面編輯 plist 文件，而不需要直接操作 XML。

其他如：Neovim, Vscode 等編輯器也可以編輯 plist

### 2. plutil 工具

`plutil` 是 macOS 中的一個命令行工具，提供了多種功能，如格式轉換（XML 與二進制格式之間）、驗證 plist 文件的合法性等。

常見用法如下：

- 檢查 plist 文件格式是否正確：

  ```bash
  plutil -lint myfile.plist
  ```

- 將 XML 格式轉換為二進制格式：

  ```bash
  plutil -convert binary1 myfile.plist
  ```

- 將二進制格式轉換為 XML 格式：

  ```bash
  plutil -convert xml1 myfile.plist
  ```

## 五、注意事項

1. **大小寫敏感**：鍵名是區分大小寫的，錯誤的大小寫可能導致配置無效。

2. **編碼格式**：XML 格式的 plist 文件應使用 UTF-8 編碼。

3. **結構合法性**：確保 plist 文件的結構完整合法，例如每個 `<dict>` 都應有相應的 `<key>` 和對應的數據類型。

## 六、參考資料

[Apple Documentation](https://developer.apple.com/documentation/bundleresources/information_property_list)
