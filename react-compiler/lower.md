# `lower` 深入解析 (React Compiler)

compiler/packages/babel-plugin-react-compiler/src/HIR/BuildHIR.ts#lower

## **什麼是 `lower`？**

`lower` 是 React Compiler 的一個關鍵函式，負責將 JavaScript **函式 (`func`) 轉換為**「**高級中間表示**（HIR, High-Level Intermediate Representation）」。這種表示形式將函式**結構化為控制流圖（CFG, Control Flow Graph）**，從而使函式可以進行更精確的 **控制流分析** 和 **memoization（記憶化）優化**。

🔹 **HIR 的特點**：

- **比 AST 更高層級**，更適合分析與優化。

- **控制流導向**，方便處理 **作用域、變數影響、優化機制**。

- **跳過 `try/catch` 和異常處理**，因為異常可能在 JavaScript 的任何地方發生，難以精確建模。

## **`lower` 的主要流程**

`lower` 負責將 JavaScript **函式轉換為 HIR 表示**，主要流程如下：

1. **初始化 HIR 建構器**

2. **建立捕獲的上下文變量**（處理閉包變數）

3. **處理函式 ID**（取得函式名稱）

4. **處理函式參數**（處理識別符、解構賦值、剩餘參數）

5. **轉換函式主體**（處理函式體並轉換成 HIR）

6. **錯誤檢查與返回**

7. **插入 `return` 語句**（確保函式結束有明確的返回點）

8. **返回 HIR 表示**

## **詳細步驟解析**

### 1. **初始化 HIR 建構器**

```typescript
const builder = new HIRBuilder(env, parent ?? func, bindings, capturedRefs);
const context: Array<Place> = [];
```

- `HIRBuilder`：負責 **HIR 的建構**，管理函式的 **作用域、變數、指令**。

- `context`：保存 **函式捕獲的變數**（閉包變數）。

### 2. **建立捕獲的上下文變量**

```typescript
for (const ref of capturedRefs ?? []) {
  context.push({
    kind: "Identifier",
    identifier: builder.resolveBinding(ref),
    effect: Effect.Unknown,
    reactive: false,
    loc: ref.loc ?? GeneratedSource,
  });
}
```

- **捕獲閉包變數**（`capturedRefs`）：函式內部引用的外部變數。

- **轉換為 `Place`**（HIR 表示變數的結構）。

- **標記 `reactive: false`**：這些變數不是 React 的反應性變數。

### 3. **處理函式 ID**

```typescript
let id: string | null = null;
if (func.isFunctionDeclaration() || func.isFunctionExpression()) {
  const idNode = (func as NodePath<t.FunctionDeclaration | t.FunctionExpression>).get("id");
  if (hasNode(idNode)) {
    id = idNode.node.name;
  }
}
```

- 取得函式名稱（適用於函式宣告 `function foo()` 或函式表達式 `const foo = function() {}`）。

- 如果有名稱，則儲存 `id`，否則設為 `null`。

### 4. **處理函式參數**

```typescript
const params: Array<Place | SpreadPattern> = [];
func.get('params').forEach(param => {
  if (param.isIdentifier()) {
    const binding = builder.resolveIdentifier(param);
    if (binding.kind !== 'Identifier') {
      builder.errors.push({ ... });
      return;
    }
    const place: Place = { ... };
    params.push(place);
  } else if (param.isObjectPattern() || param.isArrayPattern() || param.isAssignmentPattern()) {
    const place: Place = { ... };
    promoteTemporary(place.identifier);
    params.push(place);
    lowerAssignment(builder, ...);
  } else if (param.isRestElement()) {
    const place: Place = { ... };
    params.push({ kind: 'Spread', place });
    lowerAssignment(builder, ...);
  } else {
    builder.errors.push({ ... });
  }
});
```

- **識別符 (`Identifier`)** → 解析參數，轉換為 `Place`。

- **解構賦值 (`ObjectPattern`, `ArrayPattern`)** → 產生臨時變數並對應賦值。

- **剩餘參數 (`RestElement`)** → 處理 `...args` 參數，轉換為 `SpreadPattern`。

### 5. **處理函式主體**

```typescript
let directives: Array<string> = [];
const body = func.get('body');
if (body.isExpression()) {
  const fallthrough = builder.reserve('block');
  const terminal: ReturnTerminal = { ... };
  builder.terminateWithContinuation(terminal, fallthrough);
} else if (body.isBlockStatement()) {
  lowerStatement(builder, body);
  directives = body.get('directives').map(d => d.node.value.value);
} else {
  builder.errors.push({ ... });
}
```

- **函式體為表達式 (`=>`)** → 轉換為 `ReturnTerminal`，確保函式正確返回。

- **函式體為區塊 (`{}`)** → 轉換每個語句 (`lowerStatement`)。

- **提取 `directives`**（如 `"use strict"`）。

### 6. **錯誤檢查**

```typescript
if (builder.errors.hasErrors()) {
  return Err(builder.errors);
}
```

- 如果轉換過程發生錯誤，則返回 `Err`，包含錯誤資訊。

### 7. **插入 `return` 語句**

```typescript
builder.terminate(
  {
    kind: "return",
    loc: GeneratedSource,
    value: lowerValueToTemporary(builder, {
      kind: "Primitive",
      value: undefined,
      loc: GeneratedSource,
    }),
    id: makeInstructionId(0),
  },
  null,
);
```

- 確保函式**總是有一個 `return`**，即使程式碼未明確指定（例如 `function foo() {}` 會隱式回傳 `undefined`）。

- 這樣可以 **確保控制流的完整性**。

### 8. **返回 HIR 表示**

```typescript
return Ok({
  id,
  params,
  fnType: parent == null ? env.fnType : "Other",
  returnTypeAnnotation: null,
  returnType: makeType(),
  body: builder.build(),
  context,
  generator: func.node.generator === true,
  async: func.node.async === true,
  loc: func.node.loc ?? GeneratedSource,
  env,
  effects: null,
  directives,
});
```

- **`id`**：函式名稱（如果存在）。

- **`params`**：參數的 HIR 表示。

- **`fnType`**：函式類型（頂層函式或一般函式）。

- **`body`**：HIR 主體（經過 `builder.build()` 構建）。

- **`context`**：捕獲的上下文變數。

- **`generator`** 和 **`async`**：函式是否為 `async` 或 `generator`。

- **`directives`**：函式內的指令（如 `"use strict"`）。

## **總結**

`lower` 負責將 **JavaScript 函式轉換為 HIR**，這是 **React Compiler 的第一個關鍵步驟**，使函式能夠進一步進行優化與編譯。

🔹 **HIR 轉換的優勢**

✅ **控制流圖（CFG）** → 更好地分析函式執行流程。

✅ **避免 `try/catch`** → 異常難以建模，避免編譯問題。

✅ **支持記憶化（Memoization）** → 提高效能。

✅ **結構化函式** → 方便後續 **優化、指令調整、作用域分析**。

這使得 React Compiler **能夠高效地優化函式元件**，提升效能與可維護性！ 🚀
