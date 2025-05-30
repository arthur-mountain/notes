# Bash 中的 Here String `<<<` 使用筆記

在 Bash 中，`<<<` 是一種**Here String** 語法，
它用來將一個字符串作為標準輸入（stdin）提供給命令，

這是一種簡便的方法，特別是在處理短字符串時非常有用。

## 1. `<<<` 的含義

- **`<<<`**：表示將後面的字符串作為標準輸入（stdin）傳遞給命令，而不是來自文件或其他標準輸入源。

它常用於 `read` 命令，來直接處理一個字符串，而不需要臨時創建文件或使用管道。

### 範例

在這個例子中：

```bash
IFS=' ' read -r -a envs <<< "${SOME_ENVS:-"dev beta canary production"}"
```

這行代碼的作用如下：

1. **`${SOME_ENVS:-"dev beta canary production"}`**：這是 Bash 的參數替代。
   如果環境變數 `SOME_ENVS` 沒有定義，則會使用默認值 `"dev beta canary production"` 作為字符串。

2. **`IFS=' '`**：設置 `IFS`（Internal Field Separator，內部字段分隔符）為空格，這樣 `read` 會將字符串按空格分隔成多個字段。

3. **`read -r -a envs`**：`read` 命令會將 `<<<` 提供的字符串按空格分割，並將結果存入陣列 `envs` 中，`-r` 防止反斜杠轉義，`-a` 表示將讀取的內容作為陣列元素存入。

## 2. `<<<`、`<<` 和 `<` 的區別

### (1) `<<<` (Here String)

- **功能**：用來將一個字符串直接作為標準輸入。例如：

  ```bash
  read var <<< "Hello"
  ```

  這行代碼相當於將 `"Hello"` 作為標準輸入傳遞給 `read` 命令，並將其存入變數 `var` 中。

### (2) `<<` (Here Document)

- **功能**：用來在多行字符串中提供輸入，通常用於傳遞多行文本給命令。例如：

  ```bash
  cat << EOF
  Line 1
  Line 2
  EOF
  ```

  這段代碼將多行文本 `Line 1` 和 `Line 2` 作為輸入傳遞給 `cat` 命令。

### (3) `<` (Input Redirection)

- **功能**：用來從文件中讀取內容作為命令的標準輸入。例如：

  ```bash
  read var < file.txt
  ```

  這行代碼會將 `file.txt` 中的內容作為輸入傳遞給 `read` 命令，並將其存入變數 `var`。

## 3. 可否換成 `<<` 或 `<`？

- **不能換成 `<<`**：`<<` 是 Here Document，適合多行輸入的場景，並不適合用來處理單行字符串，像上面的情境。

- **不能換成 `<`**：`<` 用於文件重定向，從文件讀取內容，而這裡是處理一個字符串，因此無法替代 `<<<`。

### 範例比較

- **`<<<` 使用範例**：

  ```bash
  read var <<< "Hello"
  ```

  這會將字符串 `"Hello"` 作為輸入傳遞給 `read`。

- **`<<` 使用範例**：

  ```bash
  cat << EOF
  Line 1
  Line 2
  EOF
  ```

  這會將多行內容作為輸入傳遞給 `cat`。

- **`<` 使用範例**：

  ```bash
  read var < file.txt
  ```

  這會將 `file.txt` 的內容作為輸入。

## 4. 小結

- **`<<<`** 是用來將一個字符串作為標準輸入傳遞給命令的方式，快捷而適合短字符串場景。

- **`<<`** 是用來傳遞多行輸入的 Here Document，不適合處理單行字符串。

- **`<`** 是用來從文件讀取輸入，無法替代 `<<<`。

因此，`<<<` 的使用場景與 `<<` 或 `<` 不同，不能互相替代。
