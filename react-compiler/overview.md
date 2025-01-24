# **React Compiler 流程總結：從 Input 到 Output**

React Compiler 的主要目標是將 **React 組件的程式碼** 轉換為 **最佳化的 JavaScript 代碼**，以 **提升效能** 並 **減少不必要的重新渲染**。這個流程包含多個 **中間表示（IR, Intermediate Representation）** 的轉換，每一層都有不同的優化策略。以下是從 **輸入（原始 React 代碼）到輸出（最終最佳化代碼）** 的完整流程。

## **1. React Compiler 的完整流程**

### **🔹 Step 1：解析（Parsing）**

**📥 Input**：開發者撰寫的 **React 組件程式碼（JavaScript / TypeScript / JSX）**

```javascript
function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return <button onClick={handleClick}>Clicked {count} times</button>;
}
```

**🔧 處理**：

- 使用 **Babel** 或 **其他前端編譯工具** 解析代碼，轉換為 **AST（抽象語法樹，Abstract Syntax Tree）**。

- 這個階段會剖析 **JSX、函式結構、變數聲明、Hooks 使用情況** 等。

### **🔹 Step 2：AST（抽象語法樹）**

**📥 AST（Abstract Syntax Tree）**：

- 將原始代碼結構化為樹狀表示，這是 **編譯器處理代碼的第一步**。

- 例如，上述 `Counter` 函式可能會被表示為：

```plaintext
FunctionDeclaration {
  id: "Counter",
  body: BlockStatement {
    VariableDeclaration { kind: "const", id: "count", init: CallExpression { callee: "useState" } },
    FunctionDeclaration { id: "handleClick", body: {...} },
    ReturnStatement { argument: JSXElement }
  }
}
```

**🔧 處理**：

- 標記 **React Hooks**、狀態變數 (`useState`)、事件處理 (`handleClick`)。

- 將 **JSX 轉換為 JavaScript 表示**。

### **🔹 Step 3：HIR（高級中間表示，High-Level Intermediate Representation）**

**📥 HIR（High-Level IR）**

- HIR 是 **AST 的簡化版本**，它保留 **程式邏輯**，但去除了語法糖，並且將代碼拆解為 **基礎區塊（Basic Blocks）**。

**🔧 處理**：

- **轉換 JSX** → `button` 變成 JavaScript 函式調用：

```javascript
function Counter() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(ADD(count, 1)); // 使用 HIR 內建的 `ADD`
  };

  return CREATE_ELEMENT("button", { onClick: handleClick }, CONCAT("Clicked ", count, " times"));
}
```

**HIR 的優勢**：

- 明確標記 `setCount` 是否需要重新執行（避免不必要的 `useState` 更新）。

- 轉換 JSX 為 JavaScript 可執行函式 (`CREATE_ELEMENT`)。

### **🔹 Step 4：SSA（靜態單一賦值，Static Single Assignment）**

**📥 SSA（Static Single Assignment）**

- **每個變數只能賦值一次**，這使數據流分析更簡單，並允許更深層的優化。

**🔧 處理**：

```javascript
function Counter() {
  const [count0, setCount] = useState(0);

  function handleClick() {
    let count1 = count0 + 1; // 變數版本化
    setCount(count1);
  }

  return CREATE_ELEMENT("button", { onClick: handleClick }, CONCAT("Clicked ", count0, " times"));
}
```

**SSA 的優勢**：

- **明確標記變數版本** (`count0` → `count1`)，防止不必要的重新計算。

- 幫助 **React Compiler 確定哪些變數需要記憶化**（memoization）。

### **🔹 Step 5：LIR（低級中間表示，Low-Level Intermediate Representation）**

**📥 LIR（Low-Level IR）**

- LIR 更接近 **匯編語言**，它將函式拆解為 **指令序列**，如：

```plaintext
FUNC Counter
    LOAD 0 -> count0
    FUNC handleClick
        ADD count0, 1 -> count1
        CALL setCount, count1
    FUNC_END
    RETURN CREATE_ELEMENT("button", { onClick: handleClick }, CONCAT("Clicked ", count0, " times"))
FUNC_END
```

**LIR 的優勢**：

- 讓 **最終 JavaScript 代碼更加高效**，避免不必要的函式調用。

- **最佳化 React Hooks 處理**，減少不必要的 `useState` 計算。

### **🔹 Step 6：最佳化（Optimizations）**

**🔧 進行優化處理**

- **死代碼消除（DCE, Dead Code Elimination）**：移除未使用的變數或函式。

- **函式內聯（Function Inlining）**：將小型函式直接內聯，減少函式調用成本。

- **指令重新排序（Instruction Reordering）**：提高效能，例如提前計算靜態變數，減少不必要的執行。

### **🔹 Step 7：代碼生成（Code Generation）**

**📥 最終 JavaScript 代碼**

```javascript
function Counter() {
  let count = 0;

  function handleClick() {
    count += 1;
    render();
  }

  return h("button", { onClick: handleClick }, `Clicked ${count} times`);
}
```

**🎯 這段代碼比原始 React 代碼更高效：**

- **去除了 `useState`，因為 `count` 被標記為不需要記憶化**。

- **內聯 `handleClick`，減少函式調用開銷**。
- **JSX 被轉換為 `h("button", {...})`，提高執行效能**。

## **React Compiler 主要使用的中間表示（IR）**

| **IR 層級** | **作用**     | **主要優化**             |
| ----------- | ------------ | ------------------------ |
| **AST**     | 表示語法結構 | JSX 轉換、語法分析       |
| **HIR**     | 保留高階語義 | 控制流分析、Hooks 優化   |
| **SSA**     | 單一變數賦值 | 記憶化優化、數據流分析   |
| **LIR**     | 更接近機器碼 | 指令重新排序、死代碼消除 |

## **結論**

🔹 **React Compiler 透過 AST → HIR → SSA → LIR 逐步轉換，優化 React 組件的運行效能。**

🔹 **HIR 幫助處理 Hooks、狀態管理，使 `useState` 更高效**。

🔹 **SSA 幫助 React 追蹤變數，減少不必要的計算與重新渲染**。

🔹 **LIR 讓最終 JavaScript 代碼更加精簡，提升執行速度**。

這個 **IR 轉換流程讓 React Compiler 可以比傳統 React 更加高效地運行，減少性能瓶頸，優化整體應用體驗！** 🚀
