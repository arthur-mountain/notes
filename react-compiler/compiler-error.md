# **React Compiler Error**

React Compiler 使用一組錯誤類別 (`Error Classes`) 和介面 (`Interfaces`) 來 **報告、分類** 及 **處理錯誤**。這些錯誤類別的繼承關係如下：

**`CompilerErrorDetailOptions` → `CompilerErrorDetail` → `CompilerError`**

其中，`CompilerError` 是 **React Compiler** 的主要錯誤類別，它繼承自 JavaScript 的內建 `Error` 類別。

## **1. `CompilerErrorDetailOptions`**

這是一個 **類型（Type）**，用於管理 `CompilerErrorDetail` 的靜態錯誤資訊，包含以下屬性：

- **`reason`**：錯誤的發生原因。

- **`description`**：錯誤的詳細描述。

- **`severity`**：錯誤的嚴重程度。

- **`loc`**：錯誤發生的程式碼位置。

- **`suggestions`**：修正錯誤的建議（例如 ESLint 提示）。

這個類型確保錯誤資訊 **結構化**，方便後續處理與顯示。

## **2. `CompilerErrorDetail`**

這是一個 **類別（Class）**，負責 **管理 `CompilerErrorDetailOptions`**，並提供以下功能：

- 透過 **getter** 取得錯誤資訊 (`reason`, `description`, `severity`, `loc`, `suggestions`)。

- **方法 (`method`)**：

  - **格式化錯誤訊息**，方便輸出錯誤資訊。

這個類別負責 **封裝錯誤的細節**，確保錯誤資訊可讀、可存取。

## **3. `CompilerError`**

這是 **React Compiler 的主要錯誤類別**，負責管理所有 `CompilerErrorDetail` 實例。

它提供一系列 **靜態方法（Static Methods）**，用來建立並拋出不同類型的錯誤。

### **`CompilerError` 提供的錯誤類型**

- **`invariant`**（內部錯誤）

  - 發生在 **編譯器內部的非預期錯誤**，可能導致編譯器崩潰。

  - 這類錯誤通常表示 **編譯器的邏輯問題**，需要開發人員修正。

- **`throwTodo`**（未實作的功能）

  - 代表編譯器遇到了 **尚未支援的語法**。

  - 這是開發中的錯誤，提醒開發者需要補充對應的編譯邏輯。

- **`throwInvalidJS`**（無效的 JavaScript 語法）

  - 表示使用者提供的 JavaScript **語法錯誤**，或是**語法正確但語意錯誤**的程式碼。

  - 這可能代表使用者誤解了語言特性或語法規則。

- **`throwInvalidReact`**（無效的 React 代碼）

  - 代表程式碼違反了 **React 的規則**（例如錯誤的 Hooks 使用方式）。

  - 這些錯誤有助於確保程式碼符合 **React 的最佳實踐**。

- **`throwInvalidConfig`**（編譯器設定錯誤）

  - 表示編譯器的設定 **不正確或無效**。

  - 可能發生於 **開發人員錯誤配置了 React Compiler**。

## **4. 參考資料**

[🔗 React Compiler Source Code (GitHub)](https://github.com/facebook/react/blob/main/compiler/packages/babel-plugin-react-compiler/src/CompilerError.ts)

## **總結**

React Compiler 透過 `CompilerError` 系統化處理錯誤，主要特點如下：

1. **錯誤類別層級化** → `CompilerErrorDetailOptions` → `CompilerErrorDetail` → `CompilerError`。

2. **封裝錯誤細節** → 透過 `CompilerErrorDetail` 統一管理錯誤資訊。

3. **錯誤類型明確** → `throwInvalidJS`, `throwInvalidReact`, `throwInvalidConfig` 等，確保錯誤清楚分類。

4. **便於 Debug** → 透過 `severity`, `loc`, `suggestions` 讓開發人員快速定位錯誤並修正。

這樣的錯誤管理方式，讓 **React Compiler 更加健壯**，並提升 **開發者體驗** 🚀
