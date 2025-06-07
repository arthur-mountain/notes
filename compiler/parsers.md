# Parser 類型

## 🌟 recursive descent parser 是一種「手寫 parser」風格

**用程式碼明確寫出 parseStatement、parseExpression 這些 function，然後自己遞迴呼叫**。

優點：

- 好寫好懂，容易 debug

- 彈性超大(可以配合 semantic action 做特殊邏輯)

- 適合 expression 很複雜，語法有些 hack 的語言(例如 JavaScript)

缺點：

- 人工維護的 parser，改 grammar 比較費工

- 適合 LL 語法(不能處理 left recursion，雖然可以改寫)

---

## 🌟 還有哪些 parser 演算法？

### 1️⃣ **LL parser(top-down parsing)**

- Recursive descent parser 就是一種 **LL parser**。

- 有些人不用自己手寫，而是用 parser generator 工具(如 ANTLR)。

- ANTLR 幫你產生 LL(\*) parser → 可以 lookahead 很多 token。

### 2️⃣ **LR parser(bottom-up parsing)**

這是一個「不同哲學」：

- 不自己寫 parseX() function。

- 而是用一張 **parsing table** + **狀態機** 去驅動 parsing。

- LR parser 自己會「還原」grammar 裡的規則，組出 parse tree。

常見的工具：

- **Yacc / Bison** → LALR(1) parser generator

- **GLR** → 支援 ambiguous grammar(多解)，像 C++ 的語法很複雜，常用 GLR

優點：

- 可以處理 **左遞迴**。

- 很適合 **語法很形式化、很規則** 的語言，像 C、Java。

缺點：

- Table 很大、debug 較難。

- 自己改 grammar 要小心會破 table。

### 3️⃣ **PEG parser(Parsing Expression Grammar)**

- 一種比較「現代」的 parsing 方法。

- 用 parsing expression 描述 grammar，本質上是 top-down parsing。

- 工具：PEG.js、LPEG(Lua)、Rust 的 Pest。

優點：

- 語法易讀，支援 backtracking，適合模糊語法。

- 不用 parsing table，直接寫「規則」。

缺點：

- Backtracking 容易出現效率問題(要小心 left recursion)。

- 不保證能處理所有語法問題(要細寫規則)。

---

## 🌟 總結

| Parser 類型       | 驅動邏輯                   | 優缺點                        | 常見用途       |
| ----------------- | -------------------------- | ----------------------------- | -------------- |
| Recursive descent | 手寫 function 遞迴呼叫     | 簡單彈性大 / 不支援左遞迴     | JS / DSL       |
| LL / LL(\*)       | Top-down + Lookahead       | 工具生成，語法要 LL 型        | Java / C#      |
| LR / LALR / GLR   | Bottom-up + Table 驅動     | 處理左遞迴 / Table 複雜       | C / C++        |
| PEG               | Parsing Expression Grammar | 很靈活，backtracking 效率問題 | DSL / 配置語言 |

👉 不是所有 compiler 都用 statement / expression function 來遞迴呼叫，**只有 recursive descent 和 LL parser 是這種風格**。

👉 用 LR parser 的 compiler(很多正式語言)是透過 **parsing table 驅動**，不是手寫 function 呼叫。

👉 PEG parser 又是另一種思路，像文法驅動的 recursive parsing，有 backtracking，程式碼型態不同。
