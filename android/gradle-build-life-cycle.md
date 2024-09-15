# Gradle bulid life cycle

在 React Native 的開發過程中，Android 端的底層構建流程依賴於 Gradle。

Gradle 是一個自動化的 build tool，透過定義一系列的任務和依賴關係，完成 Android 應用的編譯、打包和部署。

當我們運行 `react-native run-android` 或 `./gradlew assembleRelease` 等命令時，
Gradle 會啟動並執行 Android build life cycle 中的不同階段。

## Android Gradle build 詳細介紹

Gradle 的 build life cycle 由多個階段組成，這些階段定義了應用從原始碼到可執行 APK/AAB 文件的過程。

主要可以分為三大部分：

- **初始化階段**

- **配置階段**

- **執行階段**

---

### 1. **初始化階段（Initialization Phase）**

Gradle 會決定在當前 build 環境下需要執行哪些項目，並創建 `Gradle` 和 `Project` Instance。

- **多模組構建（Multi-Module Builds）：** 如果項目包含多個模組（例如 `app` 模組和其他 library 模組），
  Gradle 會遍歷整個項目結構，建立所有子項目。

- **創建 Project Instance：** Gradle 會為每個模組創建一個 `Project` Instance，
  代表每個模組的 build 設置、依賴關係、build script 等。

---

### 2. **配置階段（Configuration Phase）**

Gradle 會執行每個模組的 `build.gradle` 文件，解析所有配置，並建立執行任務。

- **解析 `build.gradle` 文件：** Gradle 讀取每個模組的 `build.gradle` 文件，解析各種任務定義、依賴關係、套件等。

- **建立任務依賴圖：** 根據模組之間的依賴關係，Gradle 建立任務圖（task graph），
  以確定任務的執行順序。

  例如，某個模組依賴於另一個模組完成編譯，那麼它必須等待依賴模組的 build 完成。

- **任務配置：** 每個任務在此階段被設置好，但不會實際執行。

  例如，編譯 Java/Kotlin 的任務、打包資源的任務等。

在 React Native 的環境下，這個階段還包括一些特殊的配置，
例如通過 `react.gradle`（位於 `node_modules/react-native/react.gradle`），
將 JS bundle 的路徑設置到 native code 中，並將 React Native 的 build 邏輯注入到 Gradle 中。

---

### 3. **執行階段（Execution Phase）**

這是構建的核心階段，Gradle 會依次執行所有已配置的任務。主要包括以下步驟：

1. **依賴解析（Dependency Resolution）：**

   - Gradle 首先下載並解析所有依賴項，包括外部庫、套件和文件。

2. **資源處理（Resource Processing）：**

   - 編譯和處理應用中的資源（例如 XML 佈局文件、圖片等）。

   - 生成 `R.java` 文件，包含所有資源的引用。

   - 使用 `aapt2`（Android Asset Packaging Tool）打包資源。

3. **編譯原始碼（Compile Sources）：**

   - 將 Java/Kotlin 源碼編譯為字節碼（`.class` 文件）。

   - 如果應用包含原生代碼（例如 C/C++），使用 NDK 進行編譯。

   - 在 React Native 中，此階段還會執行 JS bundle 的打包任務，
     使用 `Metro` 打包工具生成 JavaScript 並嵌入到 APK/AAB 中。

4. **轉換字節碼（Bytecode Transformation）：**

   - 將 `.class` 文件轉換為 `.dex` 文件（Dalvik Executable），這是 Android 虛擬機運行的格式。

5. **打包（Packaging）：**

   - 將已轉換的 `.dex` 文件、資源文件等打包為 APK/AAB 文件。

6. **簽名（Signing）：**

   - 對生成的 APK/AAB 文件進行簽名。

     - 如果是 Debug 模式，使用預設的 Debug Keystore。

     - 如果是 Release 模式，則使用提供的 Release Keystore。

7. **對齊和優化（Alignment and Optimization）：**

   - 對 APK 文件使用 `zipalign` 進行優化，使其在運行時更加高效。

     - （可選）使用 `ProGuard` 或 `R8` 進行代碼混淆和壓縮，以減少 APK 大小並增強安全性。

   - 對 AAB 文件，則透過 Google Play 的動態交付機制進行優化。

8. **生成 APK/AAB（Generate APK/AAB）：**

   - 最終生成的 APK 文件位於 `app/build/outputs/apk/`，AAB 文件位於 `app/build/outputs/bundle/`。

   - 生成的文件包含 Debug 或 Release 版本的 APK/AAB，可用於部署或上傳到 Google Play。

---

### 4. **React Native 中的 Gradle 相關任務**

在 React Native 環境中，Gradle 還需要處理與 JavaScript 相關的構建邏輯。

這些邏輯通常由 `react.gradle` 文件注入。

- **`react.gradle`：** 這是 React Native 為 Gradle 構建的專用腳本，
  位於 `node_modules/react-native/react.gradle`，
  負責在 build 時生成或嵌入 JavaScript bundle。

  - **生成 JS bundle：** 使用 `Metro` 打包工具將 JavaScript 代碼和資源文件打包進 APK/AAB 中，
    對應的任務通常是 `bundleReleaseJsAndAssets` 或 `bundleDebugJsAndAssets`。

  - **Copy Resource：** 將 React Native 的資源（如圖片、字體等）打包進 APK/AAB。

---

### 5. **其他重要概念**

- **Gradle 任務（Tasks）：** Gradle 中的任務是 build 過程的基本單位，每個任務都代表一個特定的操作（如編譯、打包、測試等）。

  任務之間可以有依賴關係，也可以透過套件擴展。

- **Gradle Daemon：** Gradle 使用後台進程（Daemon）來加快 build process，這個進程會在系統中持續運行，減少冷啟動的時間。

- **Gradle Cache：** Gradle 支持緩存機制，會儲存先前 build result。

- **增量構建（Incremental Build）：** Gradle 支持增量 build，只會重新 build 有變更的部分代碼。

- **Android App Bundle（AAB）**： AAB 是 Google 推出的新的應用發布格式，相較於 APK，更加高效靈活。
  它允許 Google Play 根據用戶設備生成優化的 APK，提高應用的下載和安裝效率。

---

### 6. **構建失敗排查建議**

如果 build 中遇到失敗，可以從以下幾個方面進行排查：

1. **查看 Gradle 的 build log：** 會顯示詳細的錯誤信息，通常可以根據這些信息定位問題。

2. **檢查依賴：** build failed 有時與依賴版本衝突或依賴未正確解析有關，檢查 `build.gradle` 中的依賴配置。

3. **清理 build cache：** 有時候 cached build result 可能會導致問題，可以嘗試執行 `./gradlew clean` 來清除緩存後再 build。

4. **檢查網絡連接：** 如果依賴項無法下載，可能是網絡問題，確保 build 環境可以正常訪問外部資源。

5. **同步項目：** 在 Android Studio 中，嘗試同步項目（Sync Project with Gradle Files），確保所有配置更新。

6. **檢查環境配置：** 確認 JDK、Android SDK、NDK 等環境變量是否正確配置。

---

## 總結

Android 的 Gradle build life cycle 包括初始化、配置和執行三個主要階段，每個階段都有不同的任務。

在開發 React Native 應用時，會在 build 過程中注入一些額外的邏輯，來處理 JavaScript bundle 和資源的打包。
