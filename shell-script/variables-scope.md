# Bash 中腳本與變數作用域筆記

## 1. 變數作用範圍概述

- 在 Bash 中，變數的作用範圍默認是**局部的**，這意味著在一個腳本中定義的變數無法直接被外部 shell 或其他腳本訪問。

## 2. 外部 Shell 訪問腳本內變數

### (1) 直接執行腳本（`./scriptA.sh`）

當你直接執行一個腳本（如 `./scriptA.sh`），腳本會在一個**子進程**中運行，這時：

- 腳本中的變數對外部 shell 不可見。

- 外部 shell 無法訪問這些變數。

#### 範例

```bash
#!/usr/bin/env bash
my_var="Hello from scriptA"
```

```bash
./scriptA.sh  # 執行腳本A
echo $my_var  # 無法訪問 my_var，因為它是局部變數
```

### (2) 使用 `source` 或 `.` 執行腳本

如果你使用 `source ./scriptA.sh` 或 `. ./scriptA.sh` 來執行腳本，腳本內容會在**當前 shell**中執行，這樣：

- 變數會被保留在當前的 shell 環境中。

- 外部 shell 可以訪問這些變數。

#### 範例

```bash
$ source ./scriptA.sh  # 或 . ./scriptA.sh
$ echo $my_var         # 可以訪問 my_var
Hello from scriptA
```

## 3. 腳本之間的變數訪問

### (1) `scriptA.sh` 執行 `scriptB.sh`

如果在 `scriptA.sh` 中執行 `scriptB.sh`，默認情況下：

- `scriptB.sh` 無法訪問 `scriptA.sh` 的變數，因為它們是在不同的子進程中執行。

#### 範例

**`scriptA.sh`**:

```bash
#!/usr/bin/env bash
my_var="Hello from scriptA"
./scriptB.sh
```

**`scriptB.sh`**:

```bash
#!/usr/bin/env bash
echo "In scriptB: $my_var"  # 無法訪問 scriptA 的 my_var
```

執行結果：

```
In scriptB:
```

### (2) 使用 `export` 導出變數

為了讓 `scriptB.sh` 訪問 `scriptA.sh` 的變數，可以使用 `export` 將變數導出為環境變數。

這樣子進程（如 `scriptB.sh`）可以繼承這些變數。

#### 範例

**`scriptA.sh`**:

```bash
#!/usr/bin/env bash
my_var="Hello from scriptA"
export my_var  # 將變數導出
./scriptB.sh   # 執行 scriptB.sh
```

**`scriptB.sh`**:

```bash
#!/usr/bin/env bash
echo "In scriptB: $my_var"  # 可以訪問 my_var
```

執行結果：

```
In scriptB: Hello from scriptA
```

### (3) 使用 `source` 執行 `scriptB.sh`

你可以使用 `source` 或 `.` 來執行 `scriptB.sh`，這樣它會在與 `scriptA.sh` 相同的進程中運行，
因此可以直接訪問 `scriptA.sh` 中的變數。

#### 範例

**`scriptA.sh`**:

```bash
#!/usr/bin/env bash
my_var="Hello from scriptA"
source ./scriptB.sh  # or . ./scriptB.sh 在同一個 shell 中執行 scriptB.sh
```

**`scriptB.sh`**:

```bash
#!/usr/bin/env bash
echo "In scriptB: $my_var"  # 可以訪問 my_var
```

執行結果：

```
In scriptB: Hello from scriptA
```

---

## 4. 總結

- **直接執行腳本**（`./scriptA.sh`）：變數在子進程中，外部 shell 無法訪問。

- **使用 `source` 或 `.`**：變數會保留在當前 shell，可以訪問腳本中的變數。

- **在腳本中執行其他腳本**：

  - 如果不導出變數，其他腳本無法訪問父腳本中的變數。

  - 使用 `export` 可以讓子進程（其他腳本）訪問變數。

  - 使用 `source` 可以讓腳本在相同進程中運行，從而訪問變數。
