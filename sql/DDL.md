# 數據定義語言（DDL）

用於創建、修改和刪除數據庫中的**各種結構性對象**。

常見的 DDL 語句包括 `CREATE`、`ALTER`、`DROP` 和 `TRUNCATE`，

這些語句使得數據庫管理員和開發者能夠靈活管理數據庫結構。

---

## 1. **CREATE** – 創建

`CREATE` 語句用來創建新的數據庫對象，包括資料庫、表、索引等。

**語法：**

```sql
CREATE [DATABASE | TABLE | INDEX] object_name;
```

**範例：**

- **創建資料庫**：

  ```sql
  CREATE DATABASE my_database;
  ```

- **創建表**：

  ```sql
  CREATE TABLE users (
      id SERIAL PRIMARY KEY,
      username VARCHAR(50) NOT NULL,
      email VARCHAR(100) UNIQUE NOT NULL
  );
  ```

- **創建索引**：

  ```sql
  CREATE INDEX idx_username ON users(username);
  ```

  索引的作用是加速查詢，但在更新和插入數據時可能會影響性能，因此應根據查詢需求合理設置索引。

---

## 2. **ALTER** – 修改

`ALTER` 語句用於修改現有的數據庫對象，

例如添加、修改或刪除表的欄位屬性，並可進行表的重命名或添加約束。

- **為表添加新欄位**：

  ```sql
  ALTER TABLE table_name ADD COLUMN column_name data_type;
  ```

  **範例**：

  ```sql
  ALTER TABLE users ADD COLUMN age INT;
  ```

- **修改欄位數據類型**：

  ```sql
  ALTER TABLE table_name ALTER COLUMN column_name SET DATA TYPE new_data_type;
  ```

  **範例**：

  ```sql
  ALTER TABLE users ALTER COLUMN age SET DATA TYPE BIGINT;
  ```

- **設置欄位為 NOT NULL**：

  ```sql
  ALTER TABLE table_name ALTER COLUMN column_name SET NOT NULL;
  ```

  **範例**：

  ```sql
  ALTER TABLE users ALTER COLUMN email SET NOT NULL;
  ```

- **移除 NOT NULL 限制**：

  ```sql
  ALTER TABLE table_name ALTER COLUMN column_name DROP NOT NULL;
  ```

  **範例**：

  ```sql
  ALTER TABLE users ALTER COLUMN age DROP NOT NULL;
  ```

- **重命名欄位**：

  ```sql
  ALTER TABLE table_name RENAME COLUMN old_column_name TO new_column_name;
  ```

  **範例**：

  ```sql
  ALTER TABLE users RENAME COLUMN username TO user_name;
  ```

- **刪除欄位**：

  ```sql
  ALTER TABLE table_name DROP COLUMN column_name;
  ```

  **範例**：

  ```sql
  ALTER TABLE users DROP COLUMN age;
  ```

- 重命名資料庫

  ```sql
  ALTER DATABASE old_database_name RENAME TO new_database_name;
  ```

  範例：

  ```sql
  ALTER DATABASE my_database RENAME TO new_database;
  ```

- 設置資料庫特定參數

  設置資料庫的特定參數。這些設置會影響連接到該資料庫的所有會話。

  ```sql
  ALTER DATABASE database_name SET parameter_name TO 'parameter_value';
  ```

  範例： 設定資料庫的預設時區

  ```sql
  ALTER DATABASE my_database SET timezone TO 'UTC';
  ```

`ALTER` 語句非常靈活，允許進行結構上的變動，

如添加或刪除欄位、修改欄位數據類型、設置或移除欄位的 NOT NULL 屬性、重命名欄位等。

使用時需謹慎，特別是在有大量數據的表上進行操作時可能會暫時鎖定表，影響性能。

---

## 3. **DROP** – 刪除

`DROP` 語句用於刪除數據庫中的對象，例如表、索引或整個資料庫。

**語法：**

```sql
DROP [DATABASE | TABLE | INDEX] object_name;
```

**範例：**

- **刪除表**：

  ```sql
  DROP TABLE users;
  ```

- **刪除資料庫**：

  ```sql
  DROP DATABASE my_database;
  ```

**注意**：`DROP` 操作是不可逆的，一旦刪除，數據和結構無法恢復，因此建議操作前做好備份。

---

## 4. **TRUNCATE** – 清空數據

`TRUNCATE` 語句用來清空表中的所有數據，但保留表的結構。

與 `DELETE` 不同的是，`TRUNCATE` 更快且不記錄每行刪除的操作，因此不會觸發觸發器。

**語法：**

```sql
TRUNCATE TABLE table_name;
```

**範例：**

```sql
TRUNCATE TABLE users;
```

`TRUNCATE` 比 `DELETE` 更快，因為它直接釋放表空間，適合在不需要記錄每一行刪除的情況下使用。

**注意**：`TRUNCATE` 操作也無法回滾，因此應在使用前確認不會影響關鍵數據。

---

### 總結

`CREATE`、`ALTER`、`DROP` 和 `TRUNCATE` 語句提供了強大的結構操作功能，

使得開發者和數據庫管理員能靈活創建和調整數據庫結構。

在操作這些語句時，需特別注意對生產環境的影響，例如 `DROP` 和 `TRUNCATE` 的不可逆性。
