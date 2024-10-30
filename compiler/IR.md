# 中間表示（IR）的介紹與應用

## **中間表示（Intermediate Representation，IR）**

中間表示（IR）在編譯器中扮演著關鍵的橋樑角色，連接著高階的源代碼和低階的目標代碼。

IR 有多種形式，旨在在編譯過程中有效地表示和處理程序，以便進行分析、優化和最終的代碼生成。

---

### 1. 常見的 IR 表示方式

#### (a) 三地址碼（Three-Address Code）

三地址碼是一種線性且接近於組合語言的 IR，每條指令最多包含三個地址（操作數）。

其形式通常為：

```plaintext
result = operand1 op operand2
```

**Source Code 範例：**

```c
d = (a + b) * (c - d);
```

**Alternative Compiled Code 範例（轉換為三地址碼）：**

```plaintext
// t1, t2, t3 為臨時變數
t1 = a + b;
t2 = c - d;
t3 = t1 * t2;
d = t3;
```

**解釋：**

- **t1 = a + b;** 計算 `a + b`，結果存入臨時變數 `t1`。

- **t2 = c - d;** 計算 `c - d`，結果存入臨時變數 `t2`。

- **t3 = t1 \* t2;** 將 `t1` 和 `t2` 相乘，結果存入 `t3`。

- **d = t3;** 將結果賦值給變數 `d`。

---

#### (b) 靜態單賦值形式（Static Single Assignment, SSA）

SSA 是一種特殊的 IR，要求每個變數在程序中只被賦值一次。

對於需要重新賦值的變數，會引入新的變數名稱，這有助於簡化數據流分析和優化。

**Source Code 範例：**

```c
a = 5;
a = a + 1;
if (condition) {
    a = a * 2;
}
// 使用 a 的後續操作
```

**Alternative Compiled Code 範例（轉換為 SSA 形式）：**

```plaintext
// a1, a2, a3 為 SSA 形式的變數
a1 = 5;
a2 = a1 + 1;
if (condition) {
    a3 = a2 * 2;
} else {
    a3 = a2;
}
// 後續使用 a3 作為 a 的值
```

**解釋：**

- **a1 = 5;** 初始賦值，產生變數 `a1`。

- **a2 = a1 + 1;** 更新 `a` 的值，產生新的變數 `a2`。

- **if...else 結構：** 根據條件，`a3` 可能是 `a2 * 2` 或 `a2` 本身。

- **後續操作：** 使用 `a3` 作為變數 `a` 的最新值。

---

### 2. AST 算是 IR 的一種嗎？

#### (b) 抽象語法樹（Abstract Syntax Tree, AST）

它在編譯器的前端生成，表示源代碼的語法結構和語義資訊。

- **AST 的角色：** AST 是源代碼經過詞法和語法分析後生成的樹狀結構，節點代表語法元素，如運算符、變數、常量等。

- **作為 IR：** AST 作為編譯器的第一種 IR，為後續的語義分析、優化和代碼生成提供基礎。

**Source Code 範例：**

```c
int sum(int x, int y) {
    return x + y;
}
```

**Alternative Compiled Code 範例（AST 表示）：**

```plaintext
FunctionDecl: sum
- ReturnType: int
- Parameters:
  - x: int
  - y: int
- Body:
  - ReturnStmt:
    - BinaryOperator: '+'
      - Left: x
      - Right: y
```

**解釋：**

- **FunctionDecl:** 表示函數宣告，名稱為 `sum`。

- **Parameters:** 參數列表，包含 `x` 和 `y`，類型均為 `int`。

- **Body:** 函數主體，包含返回語句。

- **ReturnStmt:** 返回語句，包含一個二元運算符。

- **BinaryOperator:** 二元運算符，加號 `'+'`，左右操作數為 `x` 和 `y`。

---

## 總結

- **中間表示（IR）：** 在編譯器中，用於連接源代碼和目標代碼的橋樑。

- **三地址碼：** 一種線性的 IR，每條指令包含最多三個操作數，便於優化和轉換為機器碼。

- **靜態單賦值形式（SSA）：** 每個變數只被賦值一次，通過引入新變數名稱，簡化數據流分析。

- **抽象語法樹（AST）：** 編譯器前端生成的 IR，表示源代碼的語法和語義結構。
