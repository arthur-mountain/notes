# HIR

探索編譯器中的 IR、HIR 和 SSA：從 JavaScript 範例到 React Compiler 的應用

## 範例程式

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

---

## 1. 中間表示（IR）

中間表示（Intermediate Representation，IR）是編譯器在分析和優化過程中使用的一種抽象表示形式。

它將源代碼轉換為一系列簡單的指令，通常不保留高級語言的結構，便於機器進行處理。

上述函數的一種可能的 IR 表示，採用類似匯編語言的形式：

```plaintext
FUNC compute
    LOAD x -> r1
    LOAD y -> r2
    ADD r1, r2 -> r3      ; result = x + y
    COPY r3 -> r4         ; 保存 result
    CONST 10 -> r5
    CMP r3, r5            ; 比較 result > 10
    JLE label_else        ; 如果小於等於 10，跳轉到 else
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

**解釋**：

- **指令序列化**：代碼被轉換為簡單的指令序列，每條指令只執行一個操作，易於機器理解和優化。

- **控制流展平**：高級結構（如 `if-else`）被展平成低級的跳轉控制流，使用標籤和跳轉指令。

- **數據操作顯式化**：所有的數據操作都通過寄存器（如 `r1`, `r2`）進行，便於追蹤和優化。

---

## 2. 高級中間表示（HIR）

高級中間表示（High-level Intermediate Representation，HIR）保留了原始代碼的高級結構，
包括控制流語句和複雜的表達式形式。

這種表示更接近源代碼，便於進行高級語義分析和優化。

上述函數的 HIR 表示：

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

**解釋**：

- **保留高級結構**：`if-else` 控制流和變量作用域得以保留，代碼結構更接近原始源代碼。

- **操作抽象化**：運算被表示為高級函數調用（如 `ADD`、`MUL`、`DIV`、`GT`），便於進行代數化的優化。

- **可讀性提高**：這種表示方式使代碼更易於理解，也便於實施高級優化策略。

---

## 3. 靜態單賦值形式（SSA）

靜態單賦值形式（Static Single Assignment，SSA）是一種中間表示形式，
在其中每個變量僅被賦值一次。

對於需要重新賦值的變量，創建新的版本，並在控制流匯合處使用 φ（Phi）函數合併不同路徑的變量值。

上述函數轉換為 SSA 形式:

```javascript
function compute(x0, y0) {
  let result0 = x0 + y0;
  let result1, result2;

  if (result0 > 10) {
    result1 = result0 * 2;
  } else {
    result2 = result0 / 2;
  }

  // 使用 φ 函數在控制流匯合處選擇正確的結果
  let result3 = φ(result1, result2);

  return result3;
}
```

**解釋**：

- **變量版本化**：每次對變量進行賦值，創建一個新的變量版本（如 `result0`、`result1`、`result2`）。

- **φ 函數**：在控制流匯合處使用 φ 函數合併來自不同控制路徑的變量值，確保後續計算使用正確的值。

- **單一賦值**：每個變量僅賦值一次，這種特性簡化了數據流分析，有助於編譯器進行更深入的優化。

---

## 總結

- **IR（中間表示）**：將代碼轉換為低級的指令序列，便於編譯器進行基本的分析和優化。
  高級結構被展平，控制流使用跳轉和標籤表示。

- **HIR（高級中間表示）**：保留了原始代碼的高級結構，包括控制流語句和複雜的表達式，
  便於進行高級優化和保持代碼的可讀性。

- **SSA（靜態單賦值形式）**：通過確保每個變量僅被賦值一次，並使用 φ 函數合併變量，
  簡化了數據流分析，允許更深入的優化，提高代碼性能。

---

## 在 React Compiler 中的應用

在 React 編譯器中，這些概念被用於優化 React 組件的代碼。

- **HIR 的重要性**：React Compiler 使用 HIR 來保留代碼的高級結構，這有助於在編譯過程中進行特定於 React 的優化，
  同時保持生成代碼的可讀性和可調試性。

- **SSA 的應用**：通過將代碼轉換為 SSA 形式，React Compiler 能夠更精確地跟蹤變量的依賴關係，
  避免不必要的重新計算，從而提高應用的性能。

---

## 範例：在 React 組件中的應用

原始 React Component:

```javascript
function MyComponent(props) {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return <button onClick={handleClick}>Clicked {count} times</button>;
}
```

HIR 表示:

```javascript
function MyComponent(props) {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(ADD(count, 1));
  };

  return <button onClick={handleClick}>CONCAT("Clicked ", count, " times")</button>;
}
```

SSA 形式:

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

**解釋**：

- **版本化狀態管理**：在每次渲染中，`count` 的值可能會改變，使用 SSA 形式可以精確地跟蹤 `count` 的不同版本，避免狀態混亂。

---

## 結論

- **IR、HIR 和 SSA** 是編譯器中不同的中間表示形式，各自在不同的階段發揮重要作用。

- **HIR** 保留了代碼的高級結構，允許進行高級優化，這對於像 React 這樣的框架尤為重要。

- **SSA** 通過消除變量的重新賦值，使數據流分析更簡單，從而能進行更深入的優化，提高代碼性能。

這些概念在 React Compiler 中被用來優化 React 應用的性能，
同時保持代碼的可讀性和可維護性，為開發者提供更高效的開發體驗。
