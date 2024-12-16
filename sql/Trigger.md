# 觸發器（Trigger）

**觸發器**是一種特殊的數據庫機制，能在表中某些事件發生時自動執行預設的操作。

觸發器通常與表相關聯，適用於 `INSERT`、`UPDATE` 和 `DELETE` 等操作事件。

觸發器的設置能夠有效地自動化數據操作，確保數據的一致性和完整性。

---

## 語法

### 創建觸發器函式的語法

在創建觸發器之前，通常需要編寫一個觸發器函式，以定義事件觸發時執行的邏輯：

```sql
CREATE OR REPLACE FUNCTION trigger_function_name() RETURNS TRIGGER AS $$
BEGIN
  -- 觸發器函式邏輯
  -- 使用 NEW 訪問新記錄，使用 OLD 訪問舊記錄
  RETURN NEW;  -- 對於 BEFORE 插入或更新操作，必須返回 NEW
END;
$$ LANGUAGE plpgsql;
```

### 創建觸發器的語法

```sql
CREATE TRIGGER trigger_name
[BEFORE | AFTER | INSTEAD OF] [INSERT | UPDATE | DELETE]
ON table_name
FOR EACH ROW
EXECUTE FUNCTION trigger_function_name();
```

- `BEFORE`、`AFTER`、`INSTEAD OF`：指觸發器的執行時間是操作之前、之後或代替操作。

- `INSERT`、`UPDATE`、`DELETE`：指定觸發器響應的操作類型。

- `FOR EACH ROW`：表示每行觸發，若要針對整個語句則使用 `FOR EACH STATEMENT`。

- `EXECUTE FUNCTION`：指定在觸發器中執行的函式。

---

## 觸發器的結構

觸發器由兩部分組成：

1. **觸發器函式**：

   定義在事件發生時執行的操作，是包含特定邏輯的函式。

2. **觸發器**：

   定義觸發條件，
   指定在哪個表上、在何種情況下（如 `BEFORE INSERT` 或 `AFTER UPDATE`）執行觸發器函式。

---

## 常見的觸發器用途

- **自動記錄操作日誌**：如在表的某行被更新時，自動記錄操作人員和修改時間。

- **數據校驗**：如自動檢查插入或更新的數據是否符合特定條件。

- **自動化數據庫操作**：如在主表更新後自動更新相關聯的子表。

---

## 範例

### 範例：自動設置 `created_at` 欄位

假設我們有一個 `users` 表，

要求在每次插入新記錄時，自動設置 `created_at` 欄位的時間戳。

可以通過觸發器來實現這一功能。

#### 步驟 1：創建觸發器函式

```sql
CREATE OR REPLACE FUNCTION set_created_at() RETURNS TRIGGER AS $$
BEGIN
  NEW.created_at := NOW();  -- 設置當前時間為 `created_at` 的值
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

#### 步驟 2：創建觸發器

```sql
CREATE TRIGGER before_insert_set_created_at
BEFORE INSERT ON users
FOR EACH ROW
EXECUTE FUNCTION set_created_at();
```

在此例中，

當在 `users` 表插入新記錄時，

`before_insert_set_created_at` 觸發器會自動執行 `set_created_at()` 函式。

`set_created_at()` 函式則會將 `created_at` 欄位的值設為當前時間。

---

### 觸發器的應用場景

- **自動執行計算和更新**：

  當插入或更新某行時，自動計算並設置某欄位的值，例如計算總價或更新最後修改時間。

- **數據同步和維護**：

  在更新主表後，自動更新相關的子表，以保證數據一致性。

- **複雜邏輯處理**：

  如在刪除記錄前進行檢查，或在插入記錄時進行數據處理和篩選。

---

## 總結

適合需要在數據變更時執行特定邏輯的場景。

它能有效簡化應用層代碼，讓數據庫自動化執行維護操作，提高整體系統的一致性和可靠性。
