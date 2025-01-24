# **HIR (高級中間表示) 深入解析**

## **1. 什麼是 IR、HIR 和 SSA？**

在編譯器中，**中間表示（IR, Intermediate Representation）** 是一種用來表示代碼的中間形式。

它介於高級語言（如 JavaScript、C++）與機器碼之間，使編譯器能夠 **分析、優化、轉換** 代碼。

在 **React Compiler**（React 編譯器）中，這些技術被用來 **優化組件代碼，提高運行時性能**。

## **2. 範例程式**

假設我們有以下 JavaScript 函式：

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

這段代碼在不同的 IR 形式中會有不同的表示方式。

## **3. IR（中間表示）**

IR 是一種低級別的表示形式，類似於**匯編指令**，用來描述程式的基本操作：

```plaintext
FUNC compute
    LOAD x -> r1
    LOAD y -> r2
    ADD r1, r2 -> r3      ; result = x + y
    COPY r3 -> r4         ; 保存 result
    CONST 10 -> r5
    CMP r3, r5            ; 比較 result > 10
    JLE label_else        ; 如果 result <= 10，跳轉到 else
label_if:
    MUL r3, 2 -> r6       ; result = result * 2
    COPY r6 -> r4         ; 更新 result
    JMP label_end
label_else:
    DIV r3, 2 -> r7       ; result = result / 2
    COPY r7 -> r4         ; 更新 result
label_end:
    RETURN r4
END_FUNC
```

🔹 **特點**：

- **序列化指令** → 代碼被轉換成簡單的低級指令，類似於機器碼。

- **展平控制流** → `if-else` 被轉換成跳轉語句 (`JLE`、`JMP`)。

- **顯式寄存器** → 使用 `r1, r2, r3...` 來保存變數，便於優化。

## **4. HIR（高級中間表示）**

**HIR（High-level Intermediate Representation）** 保留了 **高級語法結構**，比 IR 更接近原始代碼：

```javascript
function compute(x, y) {
  let result = ADD(x, y);
  if (GT(result, 10)) {
    result = MUL(result, 2);
  } else {
    result = DIV(result, 2);
  }
  return result;
}
```

🔹 **特點**：

- **保留 `if-else` 結構** → 控制流沒有被展平，使其更易讀。

- **使用高級操作符 (`ADD`, `MUL`, `GT`)** → 便於 **邏輯優化**。

- **適合語法分析與優化** → React Compiler 會在 **HIR 層面** 進行**React Hooks 優化、狀態管理分析**等。

## **5. SSA（靜態單賦值形式）**

**SSA（Static Single Assignment）** 確保每個變數「只被賦值一次」，透過**變數版本化**來消除賦值的副作用：

```javascript
function compute(x0, y0) {
  let result0 = x0 + y0;
  let result1, result2;

  if (result0 > 10) {
    result1 = result0 * 2;
  } else {
    result2 = result0 / 2;
  }

  // 使用 φ（Phi）函數選擇正確的結果
  let result3 = φ(result1, result2);

  return result3;
}
```

🔹 **特點**：

- **變數版本化** → `result0`、`result1`、`result2`，確保變數**不會被重複賦值**。

- **使用 `φ` 函數** → 在控制流合併時選擇正確的變數值。

- **適合進一步優化** → 減少變數重新賦值的開銷，提高**數據流分析能力**。

## **6. 在 React Compiler 中的應用**

React 編譯器使用 **HIR 和 SSA 來優化 React 組件的代碼**，提升執行效率。

### **HIR 在 React 中的應用**

原始 React 組件：

```javascript
function MyComponent(props) {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return <button onClick={handleClick}>Clicked {count} times</button>;
}
```

HIR 形式：

```javascript
function MyComponent(props) {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(ADD(count, 1));
  };

  return <button onClick={handleClick}>CONCAT("Clicked ", count, " times")</button>;
}
```

🔹 **特點**：

- **狀態更新函數 `setCount` 被抽象為 `ADD(count, 1)`**，便於 React 編譯器分析。

- **字串拼接 `CONCAT` 可進一步優化，減少重繪次數**。

### **SSA 在 React 中的應用**

SSA 形式：

```javascript
function MyComponent(props) {
  const [count0, setCount] = useState(0);

  function handleClick() {
    let count1 = count0 + 1;
    setCount(count1);
  }

  return <button onClick={handleClick}>Clicked {count0} times</button>;
}
```

🔹 **特點**：

- **變數版本化 (`count0` → `count1`)** → 確保變數狀態不會發生衝突。

- **React 的 Hooks 依賴計算** 可以利用 SSA **優化 `useState` 的執行**，減少不必要的重新渲染。

## **7. 總結**

1. **IR（中間表示）** → 轉換成低級指令，適合進行基礎優化，如死碼刪除、代碼折疊。

2. **HIR（高級中間表示）** → 保留高級語法，適合進行 React Hooks 優化與語法分析。

3. **SSA（靜態單賦值）** → 消除變數賦值的副作用，提高數據流分析能力，使編譯器能夠 **優化 React 組件的狀態管理**。

## **8. React Compiler 如何利用 HIR 和 SSA 提高效能**

- **HIR 幫助編譯器保留高級語法結構，使 React Hooks、狀態管理優化變得可能**。

- **SSA 使 React 內部能夠追蹤每個變數的版本，減少不必要的 `useState` 重新計算，提升渲染效能**。
