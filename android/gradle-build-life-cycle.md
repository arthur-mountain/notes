# Gradle bulid life cycle

在React Native的開發過程中，Android端，底層依賴Gradle來完成build流程。

Gradle是一個自動化build工具，它透過定義一系列任務和依賴關係來完成編譯、打包和部署Android應用。

在運行`react-native run-android`或`./gradlew assembleRelease`等命令時，Gradle會啟動並執行Androidbuild生命週期中的不同階段。

## Android Gradlebuild生命週期詳細介紹

Gradle 的 build 生命週期由多個階段組成，這些階段定義了應用從源代碼到生成可執行的APK文件的過程。主要可以分為三大部分：

- **初始化階段**

- **配置階段**

- **執行階段**。

下面是對這些階段的詳細解釋：

---

### 1. **初始化階段 (Initialization Phase)**

在這個階段，Gradle 會決定當前 build 環境下需要執行哪些項目，並創建 `Gradle` 和 `Project` 物件。

- **多模組 build (Multi-Module Builds):** 如果項目中包含多個模組（e.g. `app`模組和其他 library 模組），
  Gradle會遍歷整個項目結構，建立所有子項目。

- **建立 Project 物件:** Gradle 會為每個模組建立一個 `Project` 物件，代表每個模組的 build 設定，依賴、build 腳本, etc.

---

### 2. **配置階段 (Configuration Phase)**

在這個階段，Gradle會執行每個模組的 `build.gradle` 文件，解析出所有的配置並建立執行任務。

- **解析`build.gradle`文件:** Gradle 會讀取每個模組的 `build.gradle` 文件，解析各種任務定義、依賴、套件等。

- **建立任務依賴圖:** 根據模組之間的依賴關係，Gradle 會建立任務圖(task graph)，以確定執行順序。
  例如，某個模組依賴另一個模組完成編譯，那麼它的 build 必須在依賴模組的 build 完成後進行。

- **任務配置:** 每個任務會在這個階段被設置好，但不會實際執行。例如，編譯 Java/Kotlin 的任務、打包資源的任務等。

在 React Native 的環境下，這個階段還包括一些特殊的配置，
例如將 JS bundle 的路徑設置到原生代碼中，通過 `react.gradle`(在 node_modules/react-native/react.gradle) ，
將 React Native 的 build 邏輯注入到 Gradle 中。

---

### 3. **執行階段 (Execution Phase)**

這是 build 的核心階段，Gradle 會依次執行所有已配置的任務。主要包括以下步驟：

1. **依賴解析 (Dependency Resolution):**

   - Gradle 首先會下載並解析所有的依賴項目，包括外部庫、套件和文件等。

2. **資源處理 (Resource Processing):**

   - 編譯和處理應用中的資源（例如XML佈局文件、圖片等）。

   - 生成 `R.java` 文件，這個文件包含所有資源的引用。

   - 通過 `aapt2(Android Asset Packaging Tool)` 工具來打包資源。

3. **編譯源代碼 (Compile Sources):**

   - 編譯 Java/Kotlin 為字節碼（.class文件）。

   - 如果應用包含原生代碼（e.g. C/C++），會使用 `NDK` 進行編譯。

   - 在 React Native 中，這個階段還會執行 JS bundle 打包任務，使用 `Metro` 打包工具生成 JavaScript 並嵌入到 APK 中。

4. **打包 (Packaging):**

   - 將編譯後的字節碼、資源文件等打包為一個 APK 文件。

   - 生成 `DEX` 文件（Dalvik Executable），這是 Android 虛擬機運行的格式。

5. **簽名 (Signing):**

   - 在生成的 APK 文件上進行簽名。

     - 如果是 Debug 模式，使用預設的 Debug Keystore

     - 如果是 Release 模式，則使用 Release Keystore

6. **對齊和優化 (Alignment and Optimization):**

   - 使用 `zipalign` 優化APK文件，使其在運行時更加高效。

   - (Optional) 使用 `ProGuard` 進行代碼混淆和壓縮，以減少APK大小並增加安全性。

7. **生成APK (Generate APK):**

   - 最後生成的APK文件會位於 `app/build/outputs/apk/`，包含 Debug or Release 版本的 APK。

---

### 4. **React Native中的Gradle相關任務**

在 React Native 環境中，Gradle 還需要處理 JavaScript 相關的 build 邏輯。這些邏輯通常由`react.gradle`文件注入。

- **`react.gradle`:** React Native 專門為 Gradlebuild 的腳本，這個文件通常位於`node_modules/react-native/react.gradle`，負責在 build 時生成或嵌入 JavaScript bundle。

  - **生成JS bundle:** 將JS和資源文件打包進APK中，對應的任務通常是 `bundleReleaseJsAndAssets` 或 `bundleDebugJsAndAssets`

  - **拷貝資源:** 將 React Native 資源（如圖片、字體等）打包進APK

---

### 5. **其他重要概念**

- **Gradle任務 (Tasks):** Gradle中的任務是build過程中的基本單位，每個任務都代表一個特定的操作（如編譯、打包、測試等）。任務之間可以有依賴關係，也可以透過套件擴展。

- **Gradle Daemon:** Gradle使用後台進程（Daemon）來加快build過程，這個進程會在系統中持續運行，減少冷啟動的時間。

- **Gradle Cache:** Gradle支持緩存機制，會儲存先前build的結果，從而避免不必要的重複build。

- **增量build (Incremental Build):** Gradle支持增量build，只會重新build有變更的部分代碼，從而提高build效率。

---

### 6. **build失敗排查建議**

如果build過程中遇到失敗，可以從以下幾個方面來排查：

1. **查看Gradle的build日誌：** build日誌會顯示詳細的錯誤訊息，通常可以根據這些訊息定位問題。

2. **檢查依賴：** build失敗有時與依賴版本衝突或依賴未正確解析有關。

3. **清理build緩存：** 有時候緩存的build結果可能會導致問題，可以嘗試執行`./gradlew clean`來清除緩存後再build。

---

## 總結

Android的Gradlebuild生命週期包括初始化、配置和執行三個主要階段，每個階段都有不同的任務。

當你開發React Native應用時，這個build過程還會注入一些額外的邏輯來處理JavaScript bundle和資源的打包。

如果遇到build失敗，可以根據錯誤日誌和build流程的不同階段來逐步排查問題。
