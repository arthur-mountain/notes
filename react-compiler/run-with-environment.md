# `runWithEnvironment` 深入解析 (React Compiler)

compiler/packages/babel-plugin-react-compiler/src/Entrypoint/Pipeline.ts#runWithEnvironment

## 什麼是 `runWithEnvironment`？

`runWithEnvironment` 是 **React Compiler** 中的一個 **生成器函式**，

負責執行 React 函式的完整編譯流程，包括 **轉換、優化、檢查**，最終輸出一個 **編譯後的函式 (`CodegenFunction`)**。

這個函式構建了一條 **完整的編譯管線**，從 **AST（抽象語法樹）** 轉換開始，經過一系列優化與檢查，最後生成 **可執行的程式**。

## `runWithEnvironment` 的主要功能

- 在指定的 **環境 (`env`)** 下編譯 React 函式。

- 逐步轉換函式的結構，使其 **更優化、更高效**。

- **記錄日誌**，方便 **調試與驗證**。

- 確保函式符合 **React Hooks、作用域、上下文等最佳實踐**。

- 最終生成 **AST（抽象語法樹）**，作為程式的最終輸出。

## **完整的編譯過程解析**

### 1. **初始轉換：低層級表示（HIR）**

```typescript
const hir = lower(func, env).unwrap();
yield log({ kind: "hir", name: "HIR", value: hir });
```

- **`lower(func, env)`**：將初始 **AST** 轉換為 **HIR（高級中間表示）**。

- HIR 更適合進行 **優化與轉換**，是後續步驟的基礎。

### 2. **初步優化與檢查**

- **`pruneMaybeThrows`**：刪除不必要的異常處理，提高效能。

- **`validateContextVariableLValues`** & **`validateUseMemo`**：

  - 確保 **context 變數** & `useMemo` 使用合法。

- **`dropManualMemoization`**：

  - **刪除手動 memo 優化**（若非必要），讓 React 自動處理。

### 3. **函式內聯與區塊合併**

- **`inlineImmediatelyInvokedFunctionExpressions`**：

  - **內聯立即執行的函式**，減少不必要的函式呼叫。

- **`mergeConsecutiveBlocks`**：

  - **合併相鄰的程式區塊**，提高可讀性與執行效率。

### 4. **更深入的優化與檢查**

- **進入 SSA（靜態單一賦值）模式**

  - **`enterSSA`** & **`eliminateRedundantPhi`**：減少重複變數，提升可優化性。

- **常數傳播**

  - **`constantPropagation`**：將 **常數值向前推導**，消除冗餘運算。

- **類型推斷**

  - **`inferTypes`**：推斷變數類型，提高安全性與效能。

### 5. **條件優化與驗證**

- **React 規範驗證**

  - **`validateHooksUsage`**：檢查 **Hooks 是否正確使用**。

  - **`validateNoCapitalizedCalls`**：避免 **錯誤的函式命名**。

- **死程式碼移除**

  - **`deadCodeElimination`**：刪除 **永遠不會執行的程式**，減少程式大小。

### 6. **指令排序與範圍推斷**

- **`instructionReordering`**：重新排序指令，提高效能。

- **`inferMutableRanges`**：推斷變數 **哪些是可變的**。

### 7. **作用域與範圍優化**

- **`inferReactivePlaces`** & **`inferReactiveScopeVariables`**：

  - 確保變數在 **正確的作用域內被反應性地更新**。

- **`rewriteInstructionKindsBasedOnReassignment`**：

  - 根據變數 **是否被重新賦值**，調整指令類型。

### 8. **函式與 JSX 提取**

- **`outlineJSX`** & **`outlineFunctions`**：

  - 提取 **JSX** 和 **函式**，讓它們可 **重用** 或進一步優化。

### 9. **最終優化與反應函式生成**

- **`buildReactiveFunction`**：

  - **最終將 HIR 轉換為「反應函式（Reactive Function）」**。

- **額外的優化**

  - **`pruneUnusedLabels`**：刪除 **未使用的標籤**。

  - **`propagateScopeDependencies`**：推斷作用域的依賴關係。

  - **`promoteUsedTemporaries`**：優化 **臨時變數的使用**。

### 10. **最終 AST 生成**

```typescript
const ast = codegenFunction(reactiveFunction, {
  uniqueIdentifiers,
  fbtOperands,
}).unwrap();
yield log({ kind: "ast", name: "Codegen", value: ast });
```

- **`codegenFunction`**：將 **反應函式轉換為 AST**（抽象語法樹）。

- 這就是 **最終的編譯結果**。

### 🔚 **測試與返回**

```typescript
if (env.config.throwUnknownException__testonly) {
  throw new Error("unexpected error");
}
return ast;
```

- 在測試模式下，**模擬異常** 以測試錯誤處理。
- 最後，返回 **編譯完成的 AST**。

## **總結**

`runWithEnvironment` 是 **React Compiler** 的核心之一，完整地 **從 AST 轉換到程式生成**。它的主要步驟如下：

1. **轉換 AST → HIR**（高級中間表示）。

2. **初步優化**（如刪除不必要的異常處理）。

3. **函式內聯與程式結構優化**。

4. **SSA、常數傳播與類型推斷**，提升最佳化機會。

5. **React Hooks & 規範檢查**，確保符合 React 最佳實踐。

6. **死程式碼刪除 & 指令排序**，提高效能。

7. **作用域對齊**，確保變數在正確範圍內變更。

8. **函式與 JSX 提取**，提高可重用性。

9. **生成反應函式（Reactive Function）**，作為最終輸出。

10. **編譯 AST**，並返回最終的程式碼。

這套 **編譯管線** 讓 React 函式經過 **最佳化、檢查、作用域管理**，最終生成 **高效且可維護的程式碼**。🚀
