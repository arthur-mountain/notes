# **HIR(高級中間表示)深入解析**

## **1. 什麼是 HIR？**

HIR(**High-Level Intermediate Representation，高級中間表示**)是一種 **比 AST(抽象語法樹)更接近機器執行邏輯**，

但仍保留高階語義信息的 **中間語言**。HIR 的主要作用是：

- **優化代碼**：透過結構化控制流，編譯器可以更高效地分析並優化程式碼。

- **控制流分析**：HIR **明確建模控制流(Control Flow)**，使條件語句、循環、函式調用更容易被分析與優化。

- **內聯與記憶化優化**：透過 HIR 的結構，可以更容易進行函式內聯(Inlining)和記憶化(Memoization)。

HIR 通常採用**圖結構**(如 **控制流圖 CFG, Control Flow Graph**)，來表示函式內部的邏輯流程和變數的使用關係。

## **2. HIR 與 AST 的區別**

| **特性**     | **AST(抽象語法樹)**          | **HIR(高級中間表示)**      |
| ------------ | ---------------------------- | -------------------------- |
| **抽象層級** | 高階語法表示，忠實反映源代碼 | 低階邏輯表示，更接近控制流 |
| **結構**     | 樹狀結構                     | 圖狀結構(控制流圖 CFG)     |
| **語法糖**   | 保留語法糖(如箭頭函式)       | 去除語法糖，統一表示       |
| **控制流**   | 隱式                         | 顯式                       |
| **用途**     | 語法分析、轉換               | 控制流分析、優化           |

HIR 保留了**高階語義**，但比 AST **更結構化、更具可操作性**，適合編譯器進一步進行優化。

## **3. HIR 的基本結構**

在 HIR 中，

每個函式會被轉換成**一組基礎區塊(Basic Blocks)**，

這些區塊之間透過**邊(Edges)** 連接，

形成 **控制流圖(CFG)**。這些基礎區塊包含：

1. **變數(Place)**：表示函式中的變數，追蹤變數的來源和影響。
2. **指令(Instruction)**：HIR 內部的操作，例如變數賦值、條件跳轉等。
3. **控制流終端(Terminal)**：決定每個基礎區塊的終止方式，如 `return` 或 `goto`。

---

## **4. HIR 轉換範例**

### **🔹 原始 JavaScript 代碼**

```javascript
function add(a, b) {
  const result = a + b;
  return result;
}
```

### **🔹 HIR 轉換結果**

```plaintext
HIRFunction {
  id: "add",
  params: [
    Place { kind: "Identifier", identifier: "a", effect: "Unknown" },
    Place { kind: "Identifier", identifier: "b", effect: "Unknown" }
  ],
  body: [
    BasicBlock {
      id: "block_0",
      instructions: [
        Instruction {
          kind: "Let",
          place: "result",
          value: BinaryOperation {
            left: "a",
            operator: "+",
            right: "b"
          }
        }
      ],
      terminal: ReturnTerminal {
        kind: "Return",
        value: "result"
      }
    }
  ]
}
```

🔹 **解釋**

1. **函式 `add` 被轉換為 `HIRFunction`，包含函式名稱 `id: "add"`。**

2. **函式的參數 `a` 和 `b` 被轉換為 `Place` 類型**，標記為變數識別符。

3. **函式內部由 `BasicBlock`(基礎區塊)表示**，其中包含：

   - **指令 `Let`**：表示 `result = a + b`。

   - **終端 `ReturnTerminal`**：表示函式返回 `result`。

HIR 的這種結構化方式，使編譯器能夠對變數的影響範圍進行分析，並更容易進行優化。

## **5. HIR 中的重要概念**

### **1. 基礎區塊(Basic Block)**

- **控制流的基本單位**，每個區塊只能有一個**入口**和一個**出口**。

- 區塊內的語句**線性執行**，只有在區塊結束時才會發生跳轉(如 `return`、`goto`)。

### **2. 指令(Instructions)**

- HIR 內的操作單元，每條指令代表一個語義單位，例如：

  - **`Let` 指令**：表示變數的聲明與賦值。

  - **`BinaryOperation`**：表示二元運算，如 `+`、`-`、`*`。

  - **`FunctionCall`**：表示函式調用。

### **3. 控制流終端(Terminal)**

- 每個基礎區塊在**控制流上必須有終止語句**，如：

  - **`ReturnTerminal`**：函式返回值。

  - **`JumpTerminal`**：條件或無條件跳轉到其他區塊。

---

## **6. 控制流分析：從 AST 到 HIR**

### **🔹 例子：含條件語句的函式**

```javascript
function compute(x, y) {
  let result = x + y;
  if (result > 10) {
    result = result * 2;
  } else {
    result = result / 2;
  }
  return result;
}
```

### **🔹 轉換為 HIR**

```plaintext
HIRFunction {
  id: "compute",
  params: [x, y],
  body: [
    BasicBlock {
      id: "block_0",
      instructions: [
        Let { place: "result", value: BinaryOperation { left: x, operator: "+", right: y } },
      ],
      terminal: ConditionalJump {
        condition: BinaryOperation { left: "result", operator: ">", right: 10 },
        ifTrue: "block_1",
        ifFalse: "block_2"
      }
    },
    BasicBlock {
      id: "block_1",
      instructions: [
        Let { place: "result", value: BinaryOperation { left: "result", operator: "*", right: 2 } },
      ],
      terminal: JumpTerminal { target: "block_3" }
    },
    BasicBlock {
      id: "block_2",
      instructions: [
        Let { place: "result", value: BinaryOperation { left: "result", operator: "/", right: 2 } },
      ],
      terminal: JumpTerminal { target: "block_3" }
    },
    BasicBlock {
      id: "block_3",
      instructions: [],
      terminal: ReturnTerminal { value: "result" }
    }
  ]
}
```

🔹 **解釋**

1. **`block_0`**：初始化 `result = x + y`，然後透過 `ConditionalJump` 判斷 `result > 10`。

2. **`block_1`**(條件為真)：`result = result * 2`，執行後跳轉到 `block_3`。

3. **`block_2`**(條件為假)：`result = result / 2`，執行後跳轉到 `block_3`。

4. **`block_3`**：函式返回 `result`。

這種方式讓條件分支變得清晰，編譯器可以更容易地進行優化。

## **7. HIR 在 React Compiler 中的應用**

React Compiler 使用 HIR 來進行**優化與編譯 React 組件**，例如：

1. **狀態管理優化** → 確保 `useState` 只在必要時更新，避免不必要的重新渲染。

2. **記憶化函式計算** → 透過 HIR 來決定 `useMemo` 是否需要重新計算。

3. **副作用分析** → 透過 HIR 標記哪些變數影響 `useEffect`，從而最小化不必要的副作用執行。

## **8. 總結**

- **HIR 是 AST 與低階代碼之間的中間層**，它去除了語法糖，並且強調控制流分析與優化。

- **HIR 使用基礎區塊和控制流圖(CFG)來表示函式的執行路徑**，這對於優化至關重要。

- **React Compiler 透過 HIR 來優化 React 應用，減少不必要的渲染與運算，提高性能。**
