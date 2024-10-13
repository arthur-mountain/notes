# Git rev-list

用於列出指定範圍內提交（commit）物件的強大工具。

它可以按時間順序或拓撲順序輸出提交歷史，並支援各種過濾和限制選項，
方便開發者深入了解項目的提交歷史。

## 參數介紹

### 基本用法

```bash
git rev-list [<options>] <commit> [--] [<path>...]
```

- `<commit>`：指定起始的提交物件，可以是分支名、標籤名或提交哈希值。

- `<path>`：可選，限制輸出僅包含修改過指定路徑的提交。

---

### 常用參數

#### `--max-count=<n>`

**說明**：限制輸出的提交數量。

**用法**：

```bash
git rev-list --max-count=10 HEAD
```

**示例**：只列出最新的 10 個提交。

---

#### `--since` 和 `--until`

**說明**：按照指定的日期範圍過濾提交。

**用法**：

```bash
git rev-list --since="2 weeks ago" HEAD
git rev-list --until="2023-01-01" HEAD
```

**示例**：列出過去兩週內的提交或在特定日期之前的提交。

---

#### `--author` 和 `--committer`

**說明**：過濾提交作者或提交者。

**用法**：

```bash
git rev-list --author="Alice" HEAD
git rev-list --committer="Bob" HEAD
```

**示例**：列出由 Alice 撰寫或由 Bob 提交的提交。

---

#### `--grep`

**說明**：按照提交訊息中的關鍵字過濾。

**用法**：

```bash
git rev-list --grep="bug fix" HEAD
```

**示例**：列出提交訊息包含 "bug fix" 的提交。

---

#### `--invert-grep`

**說明**：反轉 `--grep` 的匹配結果。

**用法**：

```bash
git rev-list --grep="feature" --invert-grep HEAD
```

**示例**：列出提交訊息不包含 "feature" 的提交。

---

#### `--merges` 和 `--no-merges`

**說明**：只列出合併提交或非合併提交。

**用法**：

```bash
git rev-list --merges HEAD
git rev-list --no-merges HEAD
```

**示例**：列出所有合併提交或所有非合併提交。

---

#### `--branches`、`--tags` 和 `--remotes`

**說明**：指定引用範圍，包括所有分支、標籤或遠端分支。

**用法**：

```bash
git rev-list --branches
git rev-list --tags
git rev-list --remotes
```

**示例**：列出所有分支或標籤中的提交。

---

#### `--objects`

**說明**：列出提交及其相關的物件（樹、blob 等）。

**用法**：

```bash
git rev-list --objects HEAD
```

**示例**：列出提交和相關的物件哈希值及其路徑。

---

#### `--all`

**說明**：列出儲存庫中所有引用的提交。

**用法**：

```bash
git rev-list --all
```

**示例**：列出儲存庫中所有提交。

---

#### `--boundary`

**說明**：在限定範圍時，顯示邊界提交（即範圍之外的直接父提交）。

**用法**：

```bash
git rev-list A..B --boundary
```

**示例**：列出從 A 到 B 的提交，並標記邊界提交。

---

#### `--cherry-pick` 和 `--left-only`、`--right-only`

**說明**：比較兩個分支的差異，特別是在尋找未合併的提交時非常有用。

**用法**：

```bash
git rev-list --cherry-pick --left-only A...B
```

**示例**：列出在 A 中但不在 B 中的提交，忽略已被 cherry-pick 的提交。

---

#### `--graph`

**說明**：以 ASCII 圖形顯示提交歷史的結構。

**用法**：

```bash
git rev-list --graph HEAD
```

**示例**：以圖形方式展示提交之間的關係。

---

## 應用範例

### 範例一：列出特定作者的提交

**情境**：想查看由 "Alice" 提交的所有變更。

**指令**：

```bash
git rev-list --author="Alice" --pretty HEAD
```

**說明**：列出所有由 Alice 撰寫的提交，並顯示詳細資訊。

---

### 範例二：統計過去一個月的提交數

**情境**：需要了解過去一個月內的開發活躍度。

**指令**：

```bash
git rev-list --count --since="1 month ago" HEAD
```

**說明**：計算從一個月前到現在的提交數量。

---

### 範例三：找出未合併到主分支的提交

**情境**：在開發分支 `feature` 上工作，想知道有哪些提交尚未合併到 `main`。

**指令**：

```bash
git rev-list main..feature
```

**說明**：列出在 `feature` 分支中，但不在 `main` 分支中的提交。

---

### 範例四：查看特定檔案的修改歷史

**情境**：想追蹤 `app.js` 文件的所有修改。

**指令**：

```bash
git rev-list HEAD -- app.js
```

**說明**：列出對 `app.js` 進行修改的所有提交。

---

### 範例五：列出所有合併提交

**情境**：需要審查所有的合併操作。

**指令**：

```bash
git rev-list --merges HEAD
```

**說明**：列出儲存庫中所有的合併提交。

---

### 範例六：找出包含特定關鍵字的提交

**情境**：尋找提交訊息中包含 "fix memory leak" 的提交。

**指令**：

```bash
git rev-list --grep="fix memory leak" HEAD
```

**說明**：過濾出提交訊息符合條件的提交。

---

### 範例七：比較兩個分支的差異

**情境**：想知道分支 `dev` 和 `main` 之間的差異。

**指令**：

```bash
git rev-list --left-right --count dev...main
```

**說明**：顯示各自分支中獨有的提交數量。

---

### 範例八：導出提交哈希值到檔案

**情境**：需要將所有提交的哈希值保存到檔案中。

**指令**：

```bash
git rev-list HEAD > commits.txt
```

**說明**：將提交哈希值輸出到 `commits.txt`。

---

### 範例九：查看提交歷史的圖形結構

**情境**：想直觀地查看提交之間的關係。

**指令**：

```bash
git rev-list --graph --oneline HEAD
```

**說明**：以圖形和簡潔的方式展示提交歷史。

---

### 範例十：找出某段時間內對特定檔案的修改

**情境**：查看過去一週內對 `README.md` 的修改。

**指令**：

```bash
git rev-list --since="1 week ago" HEAD -- README.md
```

**說明**：列出指定時間範圍內對 `README.md` 的提交。

---

### 範例十一：取得最新一筆特定的 tag name

**情境**：查看為 v2.0.0 開頭，最新一筆 tag name

**指令**：

```bash
git rev-list --tags='v2.0.0*' --max-count=1 | git describe --tags
```

**說明**：列出 v2.0.0 開頭的 tag 的 commit，並 pipe into describe 取得 tag name

---

## 總結

- **強大且靈活**：`git rev-list` 提供了豐富的參數，能夠滿足各種提交歷史查詢需求。

- **結合其他工具**：常與 `git log`、`git diff`、`git describe` 等指令結合使用，進一步分析提交內容。

- **高效篩選**：通過各種過濾選項，快速定位需要的提交，提升開發效率。

---

## 資料來源

[git rev-list](https://git-scm.com/docs/git-rev-list)
