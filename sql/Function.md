# 函式（Function）

**函式**是可以重複調用的代碼塊，主要用來執行特定的邏輯或計算，並且可以返回結果。

在 PostgreSQL 中，函式可以用 SQL 或 PL/pgSQL 編寫，
其中 PL/pgSQL 是一種類似程式語言的過程性語言，
允許使用條件、循環和異常處理等邏輯結構來處理更為複雜的業務需求。

---

## 函式語法

```sql
CREATE OR REPLACE FUNCTION function_name(param1 TYPE, param2 TYPE, ...)
RETURNS return_type AS $$
BEGIN
    -- 函式主體：執行邏輯
    -- 使用 RETURN 返回結果（如果有）
END;
$$ LANGUAGE plpgsql;
```

> [!WARNING]**注意**：
> 使用 `CREATE OR REPLACE` 可以在函式已存在時自動覆蓋它。
> 這可能會在替換的過程中短暫導致函式不可用，因此在生產環境中替換函式需謹慎，
> 建議進行測試或使用交易（Transaction）來減少替換過程的風險。

---

### 常見的函式用途

- **重複代碼的封裝**：將常用的計算或邏輯封裝成函式，便於重用，減少重複代碼。

- **數據處理和篩選**：使用函式對數據進行處理、過濾或校驗，增強 SQL 查詢的靈活性。

- **複雜查詢和計算**：適合封裝複雜邏輯，減少應用層的工作量，提升整體代碼的維護性。

---

### 函式的進階功能

1. **參數的預設值與可選參數**：函式的參數可以設置預設值，使得調用函式時可以省略某些參數。

   **範例：**

   ```sql
   CREATE OR REPLACE FUNCTION greet(name TEXT DEFAULT 'Guest') RETURNS TEXT AS $$
   BEGIN
       RETURN 'Hello, ' || name || '!';
   END;
   $$ LANGUAGE plpgsql;
   ```

   **使用此函式**：

   ```sql
   SELECT greet();         -- 結果：Hello, Guest!
   SELECT greet('Alice');  -- 結果：Hello, Alice!
   ```

2. **函式屬性**：
   在 PostgreSQL 中，函式可以指定 `IMMUTABLE`、`STABLE` 或 `VOLATILE` 屬性，以優化函式的執行。

   - **IMMUTABLE**：相同輸入下返回相同結果，可緩存以提升效能（適合無副作用的函式，如哈希計算）。

   - **STABLE**：在同一查詢中返回穩定結果（適合時間不敏感的查詢）。

   - **VOLATILE**：函式結果可能隨時改變（默認），適合隨機數、時間等操作。

   **範例**：

   ```sql
   CREATE OR REPLACE FUNCTION get_current_year() RETURNS INT AS $$
   BEGIN
       RETURN EXTRACT(YEAR FROM NOW());
   END;
   $$ LANGUAGE plpgsql STABLE;
   ```

3. **異常處理**：PL/pgSQL 支持 `EXCEPTION` 區塊來捕獲錯誤，確保函式在異常情況下能夠妥善返回。

   **範例**：

   ```sql
   CREATE OR REPLACE FUNCTION divide(a NUMERIC, b NUMERIC) RETURNS NUMERIC AS $$
   BEGIN
       RETURN a / b;
   EXCEPTION WHEN division_by_zero THEN
       RETURN NULL;  -- 若發生除零錯誤，返回 NULL
   END;
   $$ LANGUAGE plpgsql;
   ```

---

### 函式範例

#### 簡單函式範例：計算兩個數字的和

假設我們要創建一個函式，來計算兩個數字的和：

```sql
CREATE OR REPLACE FUNCTION add_numbers(a INT, b INT) RETURNS INT AS $$
BEGIN
    RETURN a + b;
END;
$$ LANGUAGE plpgsql;
```

**使用這個函式：**

```sql
SELECT add_numbers(10, 5);  -- 結果：15
```

---

#### 帶條件邏輯的函式範例：判斷是否成年

函式可以包含條件邏輯，以進行更靈活的數據處理。

例如，我們可以創建一個函式來判斷某個年齡是否達到法定成年年齡：

```sql
CREATE OR REPLACE FUNCTION is_adult(age INT) RETURNS BOOLEAN AS $$
BEGIN
    RETURN age >= 18;
END;
$$ LANGUAGE plpgsql;
```

**使用此函式：**

```sql
SELECT is_adult(20);  -- 結果：true
SELECT is_adult(15);  -- 結果：false
```

---

### 函式的應用場景

- **重複邏輯處理**：將常用的業務邏輯封裝到函式中，便於重複調用，增強代碼一致性。

- **數據處理和校驗**：可以將數據校驗邏輯放入函式，例如，根據特定規則對數據進行過濾或驗證。

- **查詢優化**：通過將複雜的查詢邏輯封裝成函式，減少 SQL 語句中的複雜度，並減少應用層的處理工作。

例如，對於需要重複調用的業務邏輯，如計算某項統計值或判斷條件成立與否，
將其封裝成函式可以避免多次重複編寫查詢邏輯，提升可維護性。

---

### 函式與其他 SQL 功能的結合使用

函式可以與其他 SQL 功能結合使用，例如：

- **在 `SELECT` 查詢中作為計算單元使用**：通過調用函式，對每一行數據進行自定義處理。

- **與[觸發器](./Trigger.md)結合**：
  函式常用於觸發器的自動化操作中，例如數據插入或更新時的自動驗證、計算等。

- **在多表聯結中使用**：將複雜的計算邏輯封裝進函式，在多表查詢中調用，減少查詢的複雜度。

---

### 函式的性能考量

- **預編譯和緩存**：PostgreSQL 將函式編譯後緩存在內存中，對頻繁使用的函式可提高執行效率。

- **函式拆分與重用**：如果函式過於複雜，可將其分解為多個小函式以便於調試和重用。

- **避免過多 I/O 操作**：
  函式內若包含頻繁的數據庫查詢或更新操作，可能會影響性能，建議將這些操作儘量簡化或優化。

---

### 總結

提升代碼復用性和查詢效率的重要工具，適合用於重複邏輯、複雜計算和數據校驗等情境。
