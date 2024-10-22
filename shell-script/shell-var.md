# Bash 中的內建變數

在 Bash 中，有許多**預設的內建變數**會在腳本執行時自動設置，
這些變數可以用來獲取有關當前腳本、執行環境和命令行參數的資訊。

這些變數對編寫腳本非常有用，因為它們提供了腳本上下文的關鍵細節。

## 1. **`$BASH_SOURCE[0]`**

- **用途**：返回當前腳本的路徑，包括文件名。
  如果腳本被 `source` 執行，會返回被 `source` 的腳本，而不是父腳本。

#### 範例

```bash
echo "Script path: ${BASH_SOURCE[0]}"
```

## 2. **`$0`**

- **用途**：返回當前執行的腳本名稱（不包括路徑）。
  如果腳本是通過 `source` 執行的，則返回 `source` 的腳本名。

#### 範例

```bash
echo "Script name: $0"
```

## 3. **`$#`**

- **用途**：返回腳本接收到的命令行參數的個數。

#### 範例

```bash
echo "Number of arguments: $#"
```

## 4. **`$@`**

- **用途**：返回傳遞給腳本的`**所有參數**`（每個參數都是單獨的一個字串）。

#### 範例

```bash
echo "All arguments: $@"
```

## 5. **`$*`**

- **用途**：返回所有傳遞給腳本的參數，與 `$@` 類似，
  但它會把所有參數當成`**一個字串**`返回（這可能會導致結果與 `$@` 略有不同）。

#### 範例

```bash
echo "All arguments as a single string: $*"
```

## 6. **`$1`, `$2`, `$3`, ...**

- **用途**：用來存放腳本的命令行參數，`$1` 是第一個參數，`$2` 是第二個參數，依此類推。

#### 範例

```bash
echo "First argument: $1"
echo "Second argument: $2"
```

## 7. **`$?`**

- **用途**：返回上一個命令的退出狀態碼（`0` 表示成功，非 `0` 表示失敗）。

#### 範例

```bash
ls /nonexistent_dir
echo "Exit status of last command: $?"
```

## 8. **`$$`**

- **用途**：返回當前腳本的進程 ID（PID）。

#### 範例

```bash
echo "Script PID: $$"
```

## 9. **`$!`**

- **用途**：返回最後執行的後台進程的進程 ID（PID）。

#### 範例

```bash
sleep 10 &
echo "Background process PID: $!"
```

## 10. **`$-`**

- **用途**：返回當前 shell 的啟動選項，也就是在啟動 shell 或腳本時被激活的模式或選項。

  這些選項可以影響 shell 的行為，通常包括像是否是交互式 shell、是否啟用了某些特定的選項（例如調試模式、錯誤處理等）。

`$-` 的值由以下一些字母組合而成，每個字母對應一個啟動選項：

- **`i`**：當 shell 以互動模式啟動時設置該選項。

- **`s`**：當 shell 是用作腳本啟動時設置該選項。

- **`h`**：當啟用了「hashall」功能時，這個選項將被設置，用於自動緩存可執行文件的位置。

- **`u`**：當啟用了「未定義變數報錯」模式（即 `set -u`）時會出現。

- **`x`**：當啟用了「命令追蹤模式」（即 `set -x`）時會出現。

- **`e`**：當啟用了「命令失敗後退出模式」（即 `set -e`）時會出現。

- **`v`**：當啟用了「逐行顯示腳本命令」（即 `set -v`）時會出現。

#### 範例

1. **查看當前 shell 的選項**：

   ```bash
   echo "Current shell options: $-"
   ```

   假設你在交互式 shell 中執行該命令，它的輸出可能是：

   ```plaintext
   Current shell options: himBH
   ```

   這表示這個 shell 是互動式 (`i`)，且啟用了 hashall (`h`) 功能。

2. **結合 `set` 指令調整 shell 選項**：

   如果你在腳本或命令行中使用 `set` 指令來啟用或禁用特定選項，`$-` 的值會隨之改變。

   **範例：**

   ```bash
   set -x  # 啟用命令追蹤
   echo "Options after enabling tracing: $-"
   ```

   執行結果：

   ```plaintext
   Options after enabling tracing: xhimBH
   ```

   在啟用了追蹤模式後，`x` 會出現在 `$-` 中。

3. **常見用法：調試腳本或腳本內條件處理**：

   你可以使用 `$-` 來檢查某些選項是否被啟用，從而做出特定處理。

   **範例：檢查是否是交互式 shell**：

   ```bash
   if [[ $- == *i* ]]; then
       echo "Interactive shell"
   else
       echo "Non-interactive shell"
   fi
   ```

   在這段腳本中，`$-` 會被檢查是否包含字母 `i`，這樣可以判斷當前是否處於互動式 shell。如果是互動式 shell，腳本會輸出 "Interactive shell"。

## 11. **`$_`**

- **用途**：這個變數有兩個主要用途：

  1. 返回上一個命令的最後一個參數。

  2. 在腳本中，存放上一個命令執行的結果。

#### 範例

```bash
echo "Hello"
echo "Last argument of previous command: $_"
```

## 12. **`$PWD`**

- **用途**：返回當前工作目錄的路徑。

#### 範例

```bash
echo "Current directory: $PWD"
```

## 13. **`$OLDPWD`**

- **用途**：返回上一次使用 `cd` 命令前的工作目錄。

#### 範例

```bash
echo "Previous directory: $OLDPWD"
```

## 14. **`$IFS`**

- **用途**：內部字段分隔符（Internal Field Separator）。
  這個變數定義了 Bash 如何分割輸入，例如在處理命令行參數時使用的分隔符，
  默認是空白字元（空格、制表符、換行符）。

#### 範例

```bash
echo "Default IFS: $IFS"
```

## 15. **`$RANDOM`**

- **用途**：每次引用時會生成一個隨機數，範圍在 `0` 到 `32767` 之間。

#### 範例

```bash
echo "Random number: $RANDOM"
```

## 16. **`$UID`**

- **用途**：返回當前用戶的用戶 ID。

#### 範例

```bash
echo "User ID: $UID"
```

## 17. **`$EUID`**

- **用途**：返回當前用戶的有效用戶 ID。這個變數在使用 `sudo` 或其他方式獲得臨時權限時會特別有用。

#### 範例

```bash
echo "Effective User ID: $EUID"
```

## 18. **`$HOSTNAME`**

- **用途**：返回當前系統的主機名。

#### 範例

```bash
echo "Hostname: $HOSTNAME"
```

## 19. **`$LINENO`**

- **用途**：返回當前腳本執行到的行號，這在調試腳本時非常有用。

#### 範例

```bash
echo "This is line number $LINENO"
```

## 20. **`${var}`**

- 用來引用變數 `var` 的值，通常在變數後面有其他字元時使用，以避免混淆。

- **範例**：

  ```bash
  name="John"
  echo "Hello, ${name}Doe!"  # 使用大括號來明確變數邊界
  ```

## 21. **`${#var}`**

- 返回變數 `var` 的長度（字串長度或數組元素數量）。

- **範例**：

  ```bash
  str="Hello"
  echo "${#str}"  # 輸出 5，字串的長度
  ```

  ```bash
  arr=("apple" "banana" "cherry")
  echo "${#arr[@]}"  # 輸出 3，數組的元素數量
  ```

## 22. **`${var:-default}`**

- 如果變數 `var` 未設置或為空，則返回 `default`。否則返回變數 `var` 的值。

- **範例**：

  ```bash
  unset myvar
  echo "${myvar:-default_value}"  # 輸出 "default_value"
  ```

## 23. **`${var:=default}`**

- 如果變數 `var` 未設置或為空，將其設置為 `default`，並返回該值。

- **範例**：

  ```bash
  unset myvar
  echo "${myvar:=default_value}"  # 輸出 "default_value"，並且 myvar 被設置為 "default_value"
  ```

## 24. **`${var:+replacement}`**

- 如果變數 `var` 已設置且非空，則返回 `replacement`；如果變數未設置或為空，則返回空字串。

- **範例**：

  ```bash
  myvar="Hello"
  echo "${myvar:+world}"  # 輸出 "world" 因為 myvar 已經設置
  ```

## 25. **`${var:?error_message}`**

- 如果變數 `var` 未設置或為空，則顯示 `error_message`
  並退出腳本。這通常用於確保某些關鍵變數必須被設置。

- **範例**：

  ```bash
  unset myvar
  echo "${myvar:?Variable not set}"  # 將會顯示錯誤並退出 "Variable not set"
  ```

## 26. **`${var%pattern}` 和 `${var%%pattern}`**

- 刪除變數 `var` 中匹配 `pattern` 的**最短部分**（`%`）或**最長部分**（`%%`）從尾部開始。

- **範例**：

  ```bash
  file="filename.txt"
  echo "${file%.txt}"  # 輸出 "filename"，最短匹配 .txt 被刪除
  ```

  ```bash
  path="/home/user/file.txt"
  echo "${path%%/*}"  # 輸出空字串，最長匹配 / 開始的部分被刪除
  ```

## 27. **`${var#pattern}` 和 `${var##pattern}`**

- 刪除變數 `var` 中匹配 `pattern` 的**最短部分**（`#`）或**最長部分**（`##`）從頭部開始。

- **範例**：

  ```bash
  path="/home/user/file.txt"
  echo "${path#*/}"  # 輸出 "home/user/file.txt"，最短匹配 / 開始的部分被刪除
  ```

  ```bash
  echo "${path##*/}"  # 輸出 "file.txt"，最長匹配 / 開始的部分被刪除
  ```

## 28. **`${!var}`**

- 用來間接引用變數。即，`var` 的值被當作一個變數名，再返回該變數名對應的值。

- **範例**：

  ```bash
  myvar="hello"
  varname="myvar"
  echo "${!varname}"  # 輸出 "hello"，間接引用 myvar 的值
  ```

## 29. **`${var^}` 和 `${var,}`**

- 這些符號用於字母大小寫轉換。

  - `${var^}`：將變數 `var` 的第一個字母轉為大寫。

  - `${var^^}`：將變數 `var` 的所有字母轉為大寫。

  - `${var,}`：將變數 `var` 的第一個字母轉為小寫。

  - `${var,,}`：將變數 `var` 的所有字母轉為小寫。

- **範例**：

  ```bash
  text="hello"
  echo "${text^}"  # 輸出 "Hello"
  echo "${text^^}"  # 輸出 "HELLO"

  text="HELLO"
  echo "${text,}"  # 輸出 "hELLO"
  echo "${text,,}"  # 輸出 "hello"
  ```

## 30. **`${var:offset:length}`**

- 提取變數 `var` 的子串，從 `offset` 開始，提取 `length` 個字符。

- **範例**：

  ```bash
  str="Hello, World"
  echo "${str:7:5}"  # 輸出 "World"，從索引 7 開始提取 5 個字符
  ```

---

## 總結

- 這些預設的變數可以幫助你在腳本中獲取各種與系統和腳本執行環境相關的重要資訊。
  尤其是在處理命令行參數、調試腳本、處理進程和管理路徑時經常使用。

- 變數擴展語法提供了靈活的方式來操作變數和參數。
  通過這些語法，你可以輕鬆地進行字串處理、參數處理和變數管理。
  它們在編寫高效的 Bash 腳本時非常有用，特別是在處理文本和自動化任務時。
