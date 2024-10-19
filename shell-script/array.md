# Bash 中數組的使用與操作

在 Bash 中，`[@]` 是用於引用**數組**元素的特殊語法。

當在腳本中看到 `[@]` 或 `[*]`，通常是與數組結合使用的，
它們的主要作用是展開數組的所有元素。

這兩者的差異在於如何展開數組。讓我們詳細解釋這些符號的用途。

## 1. **`${array[@]}` 與 `${array[*]}` 的區別**

### `${array[@]}`

- 用來展開數組的所有元素。

- 每個元素都作為一個單獨的字串。

- 特別適合在 `for` 循環中使用，因為它可以保證每個數組元素都正確地分開。

### `${array[*]}`

也用來展開數組的所有元素，但

- 會將數組的所有元素視為一個單一的字串，使用默認的分隔符（通常是空格）連接。

### 範例

假設你有一個數組：

```bash
environments=("dev" "beta" "canary" "production")
```

- 使用 `${environments[@]}`：

每個元素被作為一個單獨的字串處理。

```bash
for environment in "${environments[@]}"; do
  echo "$environment"
done
```

執行結果：

```plaintext
dev
beta
canary
production
```

- 使用 `${environments[*]}`：

所有元素被當作一個字串（用空格分隔）處理，

這在某些情況下可能會導致不符合預期的結果。

```bash
for environment in "${environments[*]}"; do
  echo "$environment"
done
```

執行結果：

```plaintext
dev beta canary production
```

## 2. **數組引用的用途**

在以下範例中：

```bash
for environment in "${environments[@]}"; do
  config_file="$project_path/.config.$environment"
  if [ -f "$config_file" ]; then
    echo "Error: The file '$config_file' already exists. Please remove it and try again."
    exit 1
  fi
done
```

這裡的 `${environments[@]}`：

- `environments` 是一個數組變數，假設它包含了不同環境名稱（例如 `"dev"`, `"beta"`, `"canary"`, `"production"`）。

- `${environments[@]}` 會展開數組的每個元素，在 `for` 循環中，`env` 將依次是 `dev`, `beta`, `canary`, `production`。

## 3. **為什麼要使用雙引號 `"${environments[@]}"`**

使用雙引號包裹 `${environments[@]}` 可以確保數組的每個元素作為單獨的字串傳遞，

避免因為元素中包含空格或其他特殊字符而發生字串分割錯誤。例如：

```bash
files=("file 1" "file 2" "file 3")
for file in "${files[@]}"; do
  echo "$file"
done
```

如果沒有雙引號，包含空格的數組元素（如 `"file 1"`, `"file 2"`, `"file 3"`）會被當作多個字串處理。

## 4. **數組的定義與操作**

### 定義數組

```bash
environments=("dev" "beta" "canary" "production")
```

### 訪問數組元素

```bash
echo "${environments[0]}"   # 輸出 "dev"
echo "${environments[1]}"   # 輸出 "beta"
```

### 添加元素到數組

```bash
environments+=("testing")
```

### 取得數組長度

```bash
echo "${#environments[@]}"  # 返回數組的長度
```

### 刪除數組中的元素

可以使用 `unset` 刪除數組中的某個元素。例如，刪除 `"beta"`：

```bash
unset 'environments[1]'
echo "${environments[@]}"  # 輸出 "dev canary production"
```

### 整體刪除數組

你也可以使用 `unset` 來刪除整個數組：

```bash
unset environments
```

## 5. **將數組作為函數參數傳遞**

可以將數組作為參數傳遞給函數。

當傳遞數組時，通常使用 `"${array[@]}"`，以確保每個元素正確地傳遞。

### 範例

```bash
function print_envs {
  local env_list=("$@")
  for env in "${env_list[@]}"; do
    echo "$env"
  done
}

environments=("dev" "beta" "canary" "production")
print_envs "${environments[@]}"
```

執行結果：

```plaintext
dev
beta
canary
production
```

## 總結

- **`[@]`**：用於展開數組的所有元素，每個元素作為單獨的字串處理，特別適合在 `for` 循環中使用。

- **`[*]`**：用於展開數組的所有元素，但會將它們當作一個單獨的字串處理，元素之間以空格（或指定的分隔符）連接。

- **`"${array[@]}"`**：使用雙引號保證每個數組元素是獨立的，即使元素中包含空格也能正確處理。

- **`"${#array[@]}"`**：返回數組的長度。

這些數組操作在編寫高效的 Bash 腳本中非常有用，尤其是在處理多個參數或批量處理任務時。
