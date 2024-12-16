# 事務控制語言（TCL）

**事務**是一組操作的集合，用於保證數據的一致性和完整性。

在事務開始後，所有變更都暫時存儲於內存中，只有在執行 `COMMIT` 後才會永久寫入數據庫。

如果事務中發生錯誤，可以使用 `ROLLBACK` 回滾所有變更，恢復至事務開始前的狀態。

事務遵循 **ACID** 原則，即**原子性**、**一致性**、**隔離性**和**持久性**，
以確保數據庫的安全性和穩定性。

不同的事務隔離級別可用於控制多事務的並發影響，
根據需求設置合適的隔離級別可以更好地平衡性能和數據一致性。

---

## 語法

1. **開始事務**：

   使用 `BEGIN` 指令啟動事務，將後續的操作納入事務控制。

   ```sql
   BEGIN;
   ```

2. **提交事務**：

   使用 `COMMIT` 提交事務，使事務中所有的變更永久生效。

   ```sql
   COMMIT;
   ```

3. **回滾事務**：

   使用 `ROLLBACK` 取消從 `BEGIN` 開始的所有變更。

   ```sql
   ROLLBACK;
   ```

4. **設置事務隔離級別**：

   指定事務隔離級別，用於控制多事務間的相互影響，防止數據不一致。

   ```sql
   SET TRANSACTION ISOLATION LEVEL [READ COMMITTED | REPEATABLE READ | SERIALIZABLE];
   ```

5. **錯誤處理自動回滾**：

   使用 `SET ON_ERROR_ROLLBACK = ON`，確保事務內發生錯誤時自動回滾，避免手動疏漏。

   ```sql
   SET ON_ERROR_ROLLBACK = ON;
   ```

---

### 範例

#### 1. 資金轉帳範例

資金轉帳中，需要確保從一個帳戶減少資金並向另一帳戶增加資金的操作同時完成，避免數據不一致。

若轉帳過程中出現錯誤，應自動回滾事務。

```sql
DO $$
DECLARE
    account1_balance INT;
    account2_balance INT;
BEGIN
    BEGIN;

    -- 鎖定帳戶 1 的行並檢查餘額
    SELECT balance INTO account1_balance FROM accounts WHERE account_id = 1 FOR UPDATE;

    -- 檢查帳戶 1 餘額是否足夠
    IF account1_balance >= 500 THEN
        -- 從帳戶 1 減去 500 單位資金
        UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;

        -- 鎖定帳戶 2 的行並檢查餘額
        SELECT balance INTO account2_balance FROM accounts WHERE account_id = 2 FOR UPDATE;

        -- 向帳戶 2 增加 500 單位資金
        UPDATE accounts SET balance = balance + 500 WHERE account_id = 2;

        -- 提交事務
        COMMIT;
    ELSE
        -- 餘額不足，回滾事務
        ROLLBACK;
        RAISE NOTICE '帳戶 1 餘額不足，無法完成轉帳';
    END IF;

EXCEPTION WHEN OTHERS THEN
    -- 捕獲其他錯誤並回滾
    ROLLBACK;
    RAISE NOTICE '事務已回滾，發生錯誤';
END;
$$;
```

在此範例中，如果 `UPDATE` 操作中出現錯誤，`ON_ERROR_ROLLBACK` 將確保事務自動回滾。

這樣無需顯式 `ROLLBACK`，避免事務卡在出錯狀態。

---

#### 2. 訂單處理範例

在電商系統中，創建訂單和減少庫存應在同一事務內，避免訂單創建成功但庫存不足的情況。

若過程中出現錯誤，事務自動回滾以確保數據一致性。

```sql
DO $$
DECLARE
    available_stock INT;
BEGIN
    BEGIN;

    -- 鎖定指定產品的庫存行，檢查庫存是否足夠
    SELECT stock INTO available_stock FROM products WHERE product_id = 10 FOR UPDATE;

    -- 檢查庫存是否充足
    IF available_stock >= 2 THEN
        -- 創建訂單
        INSERT INTO orders (order_id, user_id, product_id, quantity) VALUES (101, 1, 10, 2);

        -- 減少庫存
        UPDATE products SET stock = stock - 2 WHERE product_id = 10;

        -- 提交事務
        COMMIT;
    ELSE
        -- 若庫存不足，回滾事務
        ROLLBACK;
        RAISE NOTICE '庫存不足，無法完成訂單。';
    END IF;

EXCEPTION WHEN OTHERS THEN
    -- 捕獲其他錯誤並自動回滾
    ROLLBACK;
    RAISE NOTICE '事務已回滾，發生錯誤';
END;
$$;
```

在此例中，`FOR UPDATE` 確保 `stock` 不被其他事務修改，

若插入訂單或更新庫存失敗，`EXCEPTION` 區塊會自動捕獲錯誤並回滾事務，

避免訂單創建成功但庫存未減少的數據不一致情況。

#### 3. 搶票系統範例

在搶票場景中，需確保每張票僅能被一位用戶購得，避免超賣。

可以使用 `FOR UPDATE` 鎖定行並設置自動回滾。

```sql
DO $$
DECLARE
    remaining INT;
BEGIN
    SET ON_ERROR_ROLLBACK = ON;
    BEGIN;

    -- 鎖定並檢查剩餘票數
    SELECT available_tickets INTO remaining FROM events WHERE event_id = 1 FOR UPDATE;

    -- 若有剩餘票，進行購買
    IF remaining > 0 THEN
        UPDATE events SET available_tickets = available_tickets - 1 WHERE event_id = 1;
        INSERT INTO tickets (user_id, event_id) VALUES (123, 1);
        COMMIT;
    ELSE
        -- 庫存不足，顯示通知
        RAISE NOTICE '票已售罄，無法完成購票';
    END IF;

EXCEPTION WHEN OTHERS THEN
    -- 捕獲錯誤並自動回滾
    ROLLBACK;
    RAISE NOTICE '事務已回滾，發生錯誤';
END;
$$;
```

在此例中，`FOR UPDATE` 確保 `available_tickets` 不被其他事務修改。

設置 `ON_ERROR_ROLLBACK` 確保若購票過程中出現錯誤，事務會自動回滾，防止數據不一致。

---

## FOR UPDATE 的作用與使用場景

`FOR UPDATE` 用於行級鎖定，確保選定的行在當前事務完成之前不會被其他事務修改或刪除。

適合需要數據一致性的場景，例如購票、資金轉帳等操作。

1. **行級鎖定**：

   確保選中的行不被其他事務修改，防止數據不一致。

2. **阻止其他事務的修改**：

   若有其他事務試圖修改這些行，將被阻塞直到當前事務完成。

3. **穩定數據**：

   例如在搶票場景中，這樣可以穩定地查詢 `available_tickets`，避免多用戶同時搶票而出現超賣。

---

## 使用事務隔離級別

設置事務隔離級別，避免多事務間的數據不一致：

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
BEGIN;

-- 在最高隔離級別下進行安全操作
UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;
COMMIT;
```

---

### 總結

通過 `BEGIN`、`COMMIT` 和 `ROLLBACK` 管理事務，並可使用 `FOR UPDATE` 鎖定行來防止並發衝突。

合理設置事務隔離級別和開啟自動回滾可以在高併發環境下保障數據的一致性與完整性。
