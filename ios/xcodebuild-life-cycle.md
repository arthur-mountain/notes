# Xcode Build life cycle

在iOS開發中，當使用Xcode build iOS應用時，

Xcode執行了一系列的步驟來將原始碼轉換成可以運行在設備或模擬器上的可執行文件（如`.app`、`.ipa`等）。

這個過程涉及編譯、打包資源、鏈接依賴庫、簽名等。 以下是iOS Xcode build 流程的詳細介紹：

---

### 1. **Pre-Build（預 build 階段）**

Xcode 會檢查項目的初始設定，包括檔案和目錄的準備工作。如果有腳本設定或需要下載資源的操作，也會在這個階段進行。

- **檢查依賴項**：確保所有需要的第三方庫或資源都可用（如 CocoaPods 或 Swift Package Manager 依賴）。

- **運行腳本（Run Script Phases）**：如果有自定義腳本（如生成、清理操作等），它們會在這個階段被執行。

若是 React Native 則會檢查是否需要生成或更新 JavaScript bundle 和資源文件。

Metro打包器的初始化：

- Debug 模式: React Native不會將JavaScript代碼打包進應用，而是啟動Metro打包器，讓應用在運行時通過本地服務器來加載JavaScript。

- 生成JS Bundle（Release模式）：如果是Release build ，Xcode會運行React Native的自定義腳本，使用Metro打包器將所有的JavaScript代碼和資源打包成一個單獨的文件，通常是 main.jsbundle。

這個過程是在 build 過程的前期執行的，並在打包後將這個 bundle 文件保存到 iOS 項目的資源目錄中。

Xcode 會調用 `react-native-xcode.sh`(在 node_modules/react-native/scripts/react-native-xcode.sh)來生成JS bundle。
會執行以下任務：

1. 在Release模式下生成JavaScript bundle (main.jsbundle)。

2. 拷貝所有React Native應用的靜態資源（如圖片等）。

---

### 2. **編譯階段（Compilation Phase）**

這是 build 過程的核心部分，Xcode 會將原始碼編譯成機器碼。在這個階段，主要會發生以下步驟：

- **編譯Swift/Objective-C**：Xcode會依次編譯 Objective-C 或 Swift ，將其轉換為中間表示（如LLVM IR），然後進一步編譯為可執行的機器碼。

  - Swift編譯器將Swift編譯成`.o`（object file）文件。

  - Objective-C 編譯過程相似，產生的也是`.o`文件。

- **編譯資源文件**：如`Storyboard`、`XIB`、`Assets`（圖片、圖標等），Xcode會將這些資源編譯打包成應用資源包的一部分。

- **生成中間文件**：在這一階段，所有的原始碼都被轉換成二進制的中間文件，如`.o`（物件檔）和`.dylib`（動態鏈接庫）。

---

### 3. **鏈接階段（Linking Phase）**

在編譯完成後，Xcode 會將所有的物件文件和依賴庫鏈接在一起，生成一個完整的可執行文件。

- **鏈接靜態庫和動態庫**：將編譯後的`.o`文件與應用程序所依賴的靜態庫（如靜態.framework或`.a`文件）和動態庫（如系統庫或`.dylib`文件）進行鏈接。

- **處理符號**：鏈接器還會處理符號表，確保函數和變量能夠正確地被調用和引用。

- **最終生成 Mach-O 可執行文件**：這是macOS和iOS上的可執行文件格式，Xcode會將所有編譯後的和資源打包進這個文件。

- For React Native 的鏈接過程：
  React Native 會將其內部的JavaScript runtime和一些核心的React Native框架（如React和Yoga等）一併進行鏈接。

  鏈接React Native框架：在這個階段，React Native的核心框架以及你應用中使用的任何第三方原生模塊（例如使用原生代碼的React Native插件，如相機、地圖等）會被鏈接到應用中。這些框架通常會以靜態庫或動態庫的形式存在。

---

### 4. **資源處理階段（Resource Processing Phase）**

這個階段會處理應用中的資源，確保它們能夠正確地打包到應用程序中。

- **編譯資源文件**：包括`.plist`文件、`.strings`（本地化字符串）、`.xcassets`（圖片資源）等。

- **資源打包**：所有編譯完成的資源文件會打包到應用程序的最終包裡，這是應用能夠正確顯示圖標、界面和本地化內容的關鍵。

- For React Native 還需要打包一些特定的JavaScript和資源文件。

打包JS Bundle：在Release模式下，Xcode 會將 main.jsbundle 及所有相關的靜態資源（如圖片）打包到應用程序的資源文件夾中，
這樣它們就會被包含在應用的.app文件或.ipa文件中。

打包資源文件：React Native應用中的圖片和其他資源會通過自動化工具被拷貝並打包到iOS應用中。

---

### 5. **簽名（Code Signing）**

iOS 應用在部署到真機或App Store之前，必須進行簽名以保證安全性。

Xcode會自動處理簽名步驟，但開發者需要提前配置好 `簽名憑證（Certificates）` 和 `描述文件（Provisioning Profiles）`。

- **使用開發者憑證進行簽名**：應用的可執行文件會使用指定的開發者憑證進行簽名，以便iOS系統能夠驗證其來源和完整性。

- **嵌入描述文件**：簽名過程中，應用會嵌入有效的描述文件，這個文件定義了應用可以運行的設備、App ID等信息。

---

### 6. **打包階段（Packaging Phase）**

簽名完成後，應用會進行最後的打包，生成一個可在設備上安裝的`.app`或`.ipa`文件。

- **生成`.app`包**：這是iOS應用的實際文件包，包含了可執行文件、資源文件、框架、簽名信息等。

- **生成`.ipa`文件（可選）**：如果應用需要部署到App Store或者發送給測試者，Xcode會將`.app`包壓縮為`.ipa`文件，這是iOS應用的安裝包格式。

---

### 7. **部署階段（Deployment Phase）**

在這個階段，應用會被部署到目標設備（模擬器或真機），或者上傳到App Store Connect以供審核和發布。

- **部署到模擬器或真機**：開發者可以通過Xcode將應用安裝到模擬器或者已配置的真機進行測試。

- **上傳到App Store或TestFlight**：對於分發應用，開發者可以將 build 完成的應用上傳到App Store Connect，通過TestFlight進行測試或直接提交審核上架。

---

### 8. **增量 build （Incremental Build）**

Xcode支持增量 build ，這意味著如果只修改了某些文件，Xcode只會重新編譯和鏈接那些發生變化的部分，而不是重建整個應用，從而提高 build 速度。這對於大型項目特別有用，因為它能顯著減少等待時間。

---

### 9. **其他重要概念**

- **Derived Data**：Xcode會將中間文件和 build 結果存儲在`DerivedData`目錄中，這裡包含了所有編譯過程中的臨時文件和編譯結果，這樣可以加速後續 build 。

- **Schemes**：Xcode 中的 Scheme 定義了應用的 build 和運行設置，開發者可以根據不同需求（e.g. Debug 或 Release 模式）來切換Scheme。

- **Build Settings**：Xcode提供了大量的 build 設置，這些設置可以控制編譯器參數、簽名選項、資源打包策略等。
  這些設定可以在Xcode的圖形界面中進行調整，或者手動修改`.xcconfig`配置文件。

---

### **React native Debug模式與Release模式的區別**

- Debug模式：

  - 在Debug模式下，Xcode並不會打包JavaScript代碼到應用中，而是啟動Metro打包器，並將React Native應用連接到該打包器來加載JavaScript bundle。
    這種方式允許你在開發過程中快速迭代代碼並即時查看效果，無需重新編譯應用。

  - 你可以通過 localhost 的方式動態加載和調試JavaScript代碼。

- Release模式：

  - 在Release模式下，Xcode會將JavaScript代碼打包成main.jsbundle並內嵌到應用中，應用在運行時不需要依賴Metro打包器。

  - 這樣確保了應用在真機或發布版本中不需要額外的開發工具，能夠脫機運行。

## 總結

Xcode的 build 流程主要分為以下幾個階段：

1. **預 build 階段**：檢查依賴和資源，運行預 build 腳本。

2. **編譯階段**：編譯原始碼（Swift/Objective-C）、資源文件。

3. **鏈接階段**：鏈接所有編譯結果生成可執行文件。

4. **資源處理**：將資源打包進應用。

5. **簽名**：使用開發者憑證對應用進行簽名。

6. **打包階段**：生成`.app`或`.ipa`文件。

7. **部署階段**：將應用部署到設備或提交到App Store。

每個階段都有特定的任務，這些任務確保應用從到可執行文件的過程是完整和安全的。
如果 build 過程中出現問題，開發者可以根據不同階段的輸出日誌進行排查。

---

React Native的iOS build 流程基於標準的Xcode build 流程進行，但在此基礎上添加了JavaScript打包和靜態資源處理的步驟。
關鍵步驟包括：

1. 預 build 階段：生成main.jsbundle（僅限Release模式），並處理靜態資源。

2. 編譯和鏈接：編譯原生代碼，並鏈接React Native框架和原生模塊。

3. 資源處理：將打包的JavaScript和資源文件添加到應用中。

4. 代碼簽名：簽名React Native框架、JS bundle和應用的其他部分。

5. 打包和部署：生成最終的.app或.ipa文件並進行部署。

6. 通過這些步驟，React Native可以將JavaScript代碼與iOS原生代碼無縫集成，最終生成一個完整的iOS應用。
