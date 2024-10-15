# Git rev-parse

用於解析各種參考（references）和輸出指定格式資訊的強大工具。

可以將標籤、分支、HEAD 等轉換為對應的 commit 哈希值，

還可以提供關於repository狀態的詳細資訊，對腳本和自動化任務非常有用。

## 參數介紹

### 基本用法

```bash
git rev-parse [<options>] [<args>]
```

- `<args>`：可以是 commit 、標籤、分支名、文件路徑等。

---

### 常用參數

#### 解析 commit 物件

**用法**：

```bash
git rev-parse <revision>
```

**說明**：將給定的參考解析為 commit 哈希值。

**示例**：

```bash
git rev-parse HEAD
# 輸出：當前 HEAD 的 commit 哈希值
```

---

#### `--short`

**說明**：將 commit 哈希值縮短為指定長度。

**用法**：

```bash
git rev-parse --short=7 HEAD
```

**示例**：

```bash
git rev-parse --short HEAD
# 輸出：短格式的 commit 哈希值（預設 7 個字元）
```

---

#### `--symbolic` 和 `--symbolic-full-name`

**說明**：輸出參考的符號名稱而非哈希值。

**用法**：

```bash
git rev-parse --symbolic HEAD
git rev-parse --symbolic-full-name HEAD
```

**示例**：

- `--symbolic`：可能輸出 `HEAD`
- `--symbolic-full-name`：可能輸出 `refs/heads/main`

---

#### `--abbrev-ref`

**說明**：顯示當前分支的簡短名稱。

**用法**：

```bash
git rev-parse --abbrev-ref HEAD
```

**示例**：

```bash
git rev-parse --abbrev-ref HEAD
# 輸出：當前分支名稱，例如 `main`
```

---

#### `--git-dir` 和 `--show-toplevel`

**說明**：

- `--git-dir`：顯示 `.git` 目錄的路徑。

- `--show-toplevel`：顯示repository的頂層目錄。

**用法**：

```bash
git rev-parse --git-dir
git rev-parse --show-toplevel
```

**示例**：

```bash
git rev-parse --git-dir
# 輸出：.git

git rev-parse --show-toplevel
# 輸出：/path/to/repository
```

---

#### `--is-inside-work-tree` 和 `--is-inside-git-dir`

**說明**：檢查當前目錄是否在工作目錄或 `.git` 目錄內。

**用法**：

```bash
git rev-parse --is-inside-work-tree
git rev-parse --is-inside-git-dir
```

**示例**：

```bash
git rev-parse --is-inside-work-tree
# 輸出：true 或 false
```

---

#### `--verify`

**說明**：驗證參考是否存在，並輸出其哈希值。

**用法**：

```bash
git rev-parse --verify <revision>
```

**示例**：

```bash
git rev-parse --verify HEAD~1
# 輸出：上一個 commit 的哈希值
```

---

#### `--parseopt`

**說明**：解析自定義選項，通常在腳本中使用。

**用法**：

```bash
git rev-parse --parseopt [options]
```

**示例**：

用於解析腳本傳遞的自定義參數。

---

#### `--sq-quote`

**說明**：將輸入的字符串轉換為適合於 shell 的單引號格式。

**用法**：

```bash
git rev-parse --sq-quote <string>
```

**示例**：

```bash
git rev-parse --sq-quote "Hello World"
# 輸出：'Hello World'
```

---

#### `--flags`

**說明**：顯示傳遞給 `git rev-parse` 的原始選項。

**用法**：

```bash
git rev-parse --flags
```

**示例**：

顯示命令行中使用的選項，方便調試。

---

#### `--show-prefix` 和 `--show-cdup`

**說明**：

- `--show-prefix`：顯示當前目錄相對於repository頂層的路徑前綴。

- `--show-cdup`：顯示返回repository頂層目錄需要的路徑。

**用法**：

```bash
git rev-parse --show-prefix
git rev-parse --show-cdup
```

**示例**：

```bash
git rev-parse --show-prefix
# 輸出：src/utils/

git rev-parse --show-cdup
# 輸出：../../
```

---

#### `--absolute-git-dir`

**說明**：顯示 `.git` 目錄的絕對路徑。

**用法**：

```bash
git rev-parse --absolute-git-dir
```

**示例**：

```bash
git rev-parse --absolute-git-dir
# 輸出：/path/to/repository/.git
```

---

## 應用範例

### 範例一：取得當前 commit 的哈希值

**情境**：需要在腳本中取得當前 commit 的完整哈希值。

**指令**：

```bash
git rev-parse HEAD
```

**說明**：輸出當前 commit 的完整哈希值，可在腳本中用於標識當前版本。

---

### 範例二：取得當前分支名稱

**情境**：在自動化部署腳本中，需要知道當前所在的分支。

**指令**：

```bash
git rev-parse --abbrev-ref HEAD
```

**說明**：輸出當前分支的名稱，例如 `main`、`develop`。

---

### 範例三：檢查是否在 Git repository中

**情境**：編寫腳本時，需要確認當前目錄是否在 Git repository內。

**指令**：

```bash
git rev-parse --is-inside-work-tree
```

**說明**：如果輸出 `true`，表示在repository中；`false` 則否。

---

### 範例四：取得repository的頂層目錄

**情境**：需要在腳本中切換到repository的根目錄。

**指令**：

```bash
git rev-parse --show-toplevel
```

**說明**：輸出repository的頂層目錄路徑，可用於 `cd` 命令。

---

### 範例五：驗證參考是否存在

**情境**：在執行操作前，確認某個 commit 或分支是否存在。

**指令**：

```bash
git rev-parse --verify feature/awesome-feature
```

**說明**：如果該參考存在，將輸出其哈希值；否則命令將失敗。

---

### 範例六：取得 `.git` 目錄的路徑

**情境**：需要知道 `.git` 目錄的位置，特別是在子模組或工作樹中。

**指令**：

```bash
git rev-parse --git-dir
```

**說明**：輸出 `.git` 目錄的相對路徑。

---

### 範例七：將參考解析為完整路徑

**情境**：需要取得參考的完整名稱，例如 `refs/heads/main`。

**指令**：

```bash
git rev-parse --symbolic-full-name HEAD
```

**說明**：輸出參考的完整符號名稱。

---

### 範例八：在腳本中安全地引用路徑

**情境**：在腳本中處理文件路徑時，避免因空格或特殊字元導致問題。

**指令**：

```bash
safe_path=$(git rev-parse --sq-quote "$path")
```

**說明**：將路徑轉換為安全的單引號格式，適合於 shell 使用。

---

### 範例九：檢查當前目錄相對於repository頂層的位置

**情境**：需要知道當前工作目錄相對於repository根目錄的相對路徑。

**指令**：

```bash
git rev-parse --show-prefix
```

**說明**：輸出當前目錄相對於repository頂層的路徑前綴。

---

### 範例十：取得上一個 commit 的哈希值

**情境**：需要引用上一個 commit ，例如在回退操作中。

**指令**：

```bash
git rev-parse HEAD~1
```

**說明**：輸出上一個 commit 的哈希值，可用於 `git reset` 等操作。

---

## 總結

- **`git rev-parse` 的強大性**：它不僅可以解析各種參考，還能提供關於repository的詳細資訊。

- **腳本友好**：特別適合在腳本和自動化任務中使用，確保操作的準確性和可靠性。

- **靈活性**：透過不同的參數組合，可以滿足各種資訊查詢和處理需求。

---

## 資料來源

[git rev-parse 官方文件](https://git-scm.com/docs/git-rev-parse)
