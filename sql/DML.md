# 數據操作語言（DML）

用於在 SQL 中進行數據查詢、篩選、分組和排序等操作。

DML 是數據庫查詢和分析的重要組成部分，

通常包括 `WHERE`、`ORDER BY`、`GROUP BY`、`HAVING` 等語句，以及一些聚合函式和聯結（JOIN）操作。

---

## 基本語句

### 1. **WHERE** – 篩選數據

`WHERE` 用於篩選符合特定條件的數據。

```sql
SELECT * FROM users WHERE age > 18;
```

### 2. **ORDER BY** – 排序數據

`ORDER BY` 用於對查詢結果進行排序，默認為升序排序（ASC），可以使用 `DESC` 指定降序排序。

```sql
SELECT * FROM users ORDER BY username ASC;
```

### 3. **GROUP BY** – 分組數據

`GROUP BY` 用於將數據分組，通常與聚合函式（如 `COUNT`、`SUM`、`AVG`）一起使用，以便對每組數據進行匯總計算。

```sql
SELECT age, COUNT(*) FROM users GROUP BY age;
```

### 4. **HAVING** – 過濾分組結果

`HAVING` 用於在分組後過濾數據，通常與 `GROUP BY` 和聚合函式一起使用。

```sql
SELECT age, COUNT(*) FROM users GROUP BY age HAVING COUNT(*) > 1;
```

---

## 聚合函式

聚合函式通常與 `SELECT` 語句一起使用，用於對數據進行匯總計算。

1. **COUNT** – 計算行數

   ```sql
   SELECT COUNT(*) FROM users;
   ```

2. **SUM** – 計算數值列的總和

   ```sql
   SELECT SUM(age) FROM users;
   ```

3. **AVG** – 計算數值列的平均值

   ```sql
   SELECT AVG(age) FROM users;
   ```

4. **MIN 和 MAX** – 分別返回數值列中的最小值和最大值

   ```sql
   SELECT MIN(age), MAX(age) FROM users;
   ```

---

## JOIN 操作及集合操作

`JOIN` 用於將多個表的數據聯合起來查詢。

常用的聯結操作包括 `INNER JOIN`、`LEFT JOIN` 和 `RIGHT JOIN`。

不同的 JOIN 類型和組合可用來創建交集、差集等集合操作效果。

假設有兩個表 `A` 和 `B`：

- 表 `A` 包含欄位 `id` 和 `name`

- 表 `B` 包含欄位 `id` 和 `order_date`

```plaintext
表 A                     表 B
+----+--------+          +----+-------------+
| id | name   |          | id | order_date  |
+----+--------+          +----+-------------+
| 1  | Alice  |          | 1  | 2023-01-01  |
| 2  | Bob    |          | 2  | 2023-01-02  |
| 3  | Carol  |          | 4  | 2023-01-04  |
+----+--------+          +----+-------------+
```

---

### 1. **INNER JOIN** – 返回兩表中匹配的行（交集）

`INNER JOIN` 僅返回 `A` 表和 `B` 表中都有匹配記錄的行，即兩表的**交集**部分。

**語法：**

```sql
SELECT A.id, A.name, B.order_date
FROM A
INNER JOIN B ON A.id = B.id;
```

**結果：**

```plaintext
+----+--------+-------------+
| id | name   | order_date  |
+----+--------+-------------+
| 1  | Alice  | 2023-01-01  |
| 2  | Bob    | 2023-01-02  |
+----+--------+-------------+
```

返回的部分：

`A` 和 `B` 表中 `id` 匹配的行，即交集部分（id = 1 和 id = 2）。

---

### 2. **LEFT JOIN** – 返回左表的所有行，右表無匹配則以 `NULL` 填充（左差集和交集）

`LEFT JOIN` 返回 `A` 表的所有行。

若 `A` 表中某行在 `B` 表中有匹配，則顯示匹配數據；
否則，以 `NULL` 填充。結果包括左差集和交集。

**語法：**

```sql
SELECT A.id, A.name, B.order_date
FROM A
LEFT JOIN B ON A.id = B.id;
```

**結果：**

```plaintext
+----+--------+-------------+
| id | name   | order_date  |
+----+--------+-------------+
| 1  | Alice  | 2023-01-01  |
| 2  | Bob    | 2023-01-02  |
| 3  | Carol  | NULL        |
+----+--------+-------------+
```

返回的部分：`A` 表的所有行，

其中交集部分（id = 1 和 id = 2）正常匹配，

左差集部分（id = 3）顯示 `NULL`。

---

### 3. **RIGHT JOIN** – 返回右表的所有行，左表無匹配則以 `NULL` 填充（右差集和交集）

`RIGHT JOIN` 返回 `B` 表的所有行。

若 `B` 表中某行在 `A` 表中有匹配，則顯示匹配數據；
否則，以 `NULL` 填充。結果包括右差集和交集。

**語法：**

```sql
SELECT A.id, A.name, B.order_date
FROM A
RIGHT JOIN B ON A.id = B.id;
```

**結果：**

```plaintext
+----+--------+-------------+
| id | name   | order_date  |
+----+--------+-------------+
| 1  | Alice  | 2023-01-01  |
| 2  | Bob    | 2023-01-02  |
| 4  | NULL   | 2023-01-04  |
+----+--------+-------------+
```

返回的部分：`B` 表的所有行，

其中交集部分（id = 1 和 id = 2）正常匹配，

右差集部分（id = 4）顯示 `NULL`。

---

### 4. **FULL OUTER JOIN** – 返回兩表的所有行，無匹配則以 `NULL` 填充（聯集）

`FULL OUTER JOIN` 返回 `A` 表和 `B` 表的所有行。

若行在兩表中均有匹配，則顯示匹配數據；

若在其中一表無匹配，則以 `NULL` 填充。

結果包括聯集（交集和左右差集）。

**語法：**

```sql
SELECT A.id, A.name, B.order_date
FROM A
FULL OUTER JOIN B ON A.id = B.id;
```

**結果：**

```plaintext
+----+--------+-------------+
| id | name   | order_date  |
+----+--------+-------------+
| 1  | Alice  | 2023-01-01  |
| 2  | Bob    | 2023-01-02  |
| 3  | Carol  | NULL        |
| 4  | NULL   | 2023-01-04  |
+----+--------+-------------+
```

返回的部分：

包含 `A` 和 `B` 表的所有行，

其中交集部分（id = 1 和 id = 2）正常匹配，

左差集部分（id = 3）、右差集部分（id = 4），以 `NULL` 填充。

---

## 子查詢（Subquery）

子查詢是嵌套在另一個 SQL 語句中的查詢，用於查詢結果作為主查詢的一部分。

子查詢可以位於 `SELECT`、`FROM`、`WHERE` 等部分，用來執行更複雜的查詢。

### 子查詢的常見用法

1. **作為 `SELECT` 中的值**：

   ```sql
   SELECT username, (SELECT COUNT(*) FROM orders WHERE orders.user_id = users.id) AS order_count
   FROM users;
   ```

2. **作為 `FROM` 中的虛擬表**：

   ```sql
   SELECT sub.username, sub.order_count
   FROM (SELECT username, COUNT(*) AS order_count FROM users JOIN orders ON users.id = orders.user_id GROUP BY username) AS sub
   WHERE sub.order_count > 5;
   ```

3. **作為 `WHERE` 條件的一部分**：

   ```sql
   SELECT username FROM users
   WHERE id IN (SELECT user_id FROM orders WHERE total > 100);
   ```

子查詢通常用於過濾、篩選、聚合結果，也可以在同一語句中執行多層嵌套。

---

## SQL 執行順序

在 SQL 查詢中，不同語句的執行順序與語句在查詢中的排列順序不完全一致。

SQL 的執行順序如下：

1. **FROM** – 確定數據來源，包含 JOIN 操作。

2. **WHERE** – 篩選符合條件的數據。

3. **GROUP BY** – 根據指定欄位進行分組。

4. **HAVING** – 對分組後的結果進行篩選。

5. **SELECT** – 選擇需要顯示的欄位。

6. **ORDER BY** – 對結果排序。

7. **LIMIT**– 限制返回的行數。

**範例：**

```sql
SELECT age, COUNT(*) AS user_count
FROM users
WHERE age > 18
GROUP BY age
HAVING user_count > 1
ORDER BY age DESC;
```

在這個查詢中：

- `FROM` 確定表 `users` 為數據來源。

- `WHERE` 篩選 `age > 18` 的記錄。

- `GROUP BY` 將結果按 `age` 分組。

- `HAVING` 保留分組後 `user_count > 1` 的記錄。

- `SELECT` 提取 `age` 和 `user_count` 欄位。

- `ORDER BY` 將結果按年齡降序排列。

---

### 總結

SQL 中常用的數據操作語句和功能，包括查詢、篩選、分組、排序、聯結操作，以及子查詢和執行順序。
