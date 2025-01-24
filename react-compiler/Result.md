# **在 JavaScript 中實作 Rust 的 `Result` 介面**

compiler/packages/babel-plugin-react-compiler/src/Utils/Result.ts

Rust 語言中的 `Result<T, E>` 是一種泛型介面，用於表示可能的「**成功 (`Ok`)**」或「**失敗 (`Err`)**」的結果。

React Compiler 也在 **JavaScript / TypeScript** 中實作類似的 `Result` 介面，提供更加**安全、可組合**的錯誤處理方式。

## **1. `Result<T, E>` 的基本概念**

`Result<T, E>` 是一個泛型介面，主要有兩種可能的狀態：

- **`Ok<T>`**：表示操作成功，內部包含一個值 `T`。

- **`Err<E>`**：表示操作失敗，內部包含一個錯誤值 `E`。

在 JavaScript / TypeScript 中，我們可以透過 **類別（Class）** 來實作這兩種狀態：

- **`OkImpl<T>`**：代表 `Ok` 狀態，包含成功的值。

- **`ErrImpl<E>`**：代表 `Err` 狀態，包含錯誤的值。

這樣的結構讓我們可以像在 Rust 中一樣，透過 **函數組合**（`map`、`andThen`）、**錯誤處理**（`mapErr`）、**預設值處理**（`unwrapOr`）等方式來處理 `Result`，而不需要使用 `try/catch`。

## **2. `Result` 主要方法解析**

### **1. 映射函數（Map Functions）**

#### **`map(fn)`**

對 `Ok` 值應用函數 `fn`，但 `Err` 值保持不變。

```typescript
const result = Ok(5).map((x) => x * 2); // Ok(10)
const errorResult = Err("Error").map((x) => x * 2); // Err("Error")
```

#### **`mapErr(fn)`**

對 `Err` 值應用函數 `fn`，但 `Ok` 值保持不變。

```typescript
const error = Err("Error").mapErr((e) => e + " happened"); // Err("Error happened")
```

### **2. 值的處理（Handling Values）**

#### **`mapOr(fallback, fn)`**

如果是 `Ok`，應用函數 `fn`；如果是 `Err`，則返回提供的 `fallback`。

```typescript
const value = Ok(10).mapOr(5, (x) => x + 1); // 11
const fallbackValue = Err("oops").mapOr(5, (x) => x + 1); // 5
```

#### **`mapOrElse(fallbackFn, fn)`**

類似 `mapOr`，但 `fallback` 是一個函數（`fallbackFn`），允許更靈活的處理。

```typescript
const value = Ok(10).mapOrElse(
  () => 5,
  (x) => x + 1,
); // 11

const fallbackValue = Err("oops").mapOrElse(
  () => 5,
  (x) => x + 1,
); // 5
```

### **3. 鏈式函數（Chaining Functions）**

#### **`andThen(fn)`**

如果當前 `Result` 是 `Ok`，則將值傳遞給函數 `fn`，並返回新的 `Result`；如果當前為 `Err`，則直接返回 `Err`。

```typescript
const chainedResult = Ok(2).andThen((x) => Ok(x * 2)); // Ok(4)
const errorChainedResult = Err("error").andThen((x) => Ok(x * 2)); // Err("error")
```

#### **`or(res)`**

如果當前 `Result` 是 `Err`，則返回 `res`；如果是 `Ok`，則保持不變。

```typescript
const result = Ok(10).or(Err("fallback")); // Ok(10)
const errorResult = Err("error").or(Ok(5)); // Ok(5)
```

### **4. 狀態檢查（Checking Status）**

#### **`isOk()`**

判斷 `Result` 是否為 `Ok`。

```typescript
const success = Ok(1).isOk(); // true
const error = Err("Error").isOk(); // false
```

#### **`isErr()`**

判斷 `Result` 是否為 `Err`。

```typescript
const success = Ok(1).isErr(); // false
const error = Err("Error").isErr(); // true
```

### **5. 非安全取值（Unsafe Extraction）**

#### **`expect(msg)`**

如果 `Result` 是 `Ok`，則返回 `Ok` 的值；如果是 `Err`，則拋出異常，並帶有 `msg` 訊息。

```typescript
const val = Ok(1).expect("should not fail"); // 1
// Err("failure").expect("failed") -> Throws "failed: failure"
```

#### **`unwrap()`**

如果 `Result` 是 `Ok`，則返回 `Ok` 的值；如果是 `Err`，則拋出異常。

```typescript
const val = Ok(1).unwrap(); // 1
// Err("failure").unwrap() -> Throws "Can't unwrap `Err` to `Ok`"
```

### **6. 預設值處理（Handling Default Values）**

#### **`unwrapOr(fallback)`**

如果 `Result` 是 `Ok`，則返回該值；如果是 `Err`，則返回 `fallback`。

```typescript
const val = Ok(1).unwrapOr(2); // 1
const fallback = Err("oops").unwrapOr(2); // 2
```

#### **`unwrapOrElse(fallbackFn)`**

與 `unwrapOr` 類似，但 `fallback` 是一個函數，用於處理 `Err`。

```typescript
const val = Ok(1).unwrapOrElse(() => 2); // 1
const fallback = Err("oops").unwrapOrElse(() => 2); // 2
```

## **3. `OkImpl<T>` 和 `ErrImpl<E>` 的實作**

- **`OkImpl<T>`**：負責 `Ok` 狀態的實作，內部存儲成功的值 `T`，並提供上述方法的具體邏輯。

- **`ErrImpl<E>`**：負責 `Err` 狀態的實作，內部存儲錯誤值 `E`，並提供錯誤處理邏輯。

這樣的類別設計讓 `OkImpl` 和 `ErrImpl` **各自負責處理自己的邏輯**，確保 `Result` 介面的行為與 Rust 的 `Result` 一致。

## **4. 總結**

這段 TypeScript 實作了 Rust 的 `Result<T, E>` 介面，提供了**更安全**的錯誤處理方式，避免了 `try/catch` 帶來的程式可讀性問題。這種設計具備以下優勢：

1. **清晰的錯誤處理機制**

   - 使用 `mapErr`、`orElse` 來處理錯誤，而不依賴 `try/catch`。

2. **函數式組合（Chaining）**

   - 透過 `map`、`andThen` 可以**鏈式調用**，讓程式碼更簡潔。

3. **提供預設值與錯誤回退**
   - `unwrapOr`、`unwrapOrElse` 允許開發者安全地處理 `Err`。
