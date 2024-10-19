# Bash 中的 `set` 命令筆記

在 Bash 中，`set` 命令用來改變 shell 的一些行為和特性。

它可以開啟或關閉特定的 shell 選項，並用來設置腳本執行過程中的不同模式。

`set` 既可以用來設定全局的 shell 行為，也可以影響腳本內部的變數處理。

## 1. `set` 常用選項

### (1) `set -e`

- 當腳本中某個命令執行失敗時，使用 `set -e` 可以強制終止腳本的執行。

這對於需要在錯誤時及時中止腳本的情境非常有用。

#### 範例

```bash
#!/usr/bin/env bash
set -e  # 開啟錯誤終止模式

echo "Before error"
false   # 此命令會失敗
echo "After error"  # 這行不會被執行
```

執行結果：

```plaintext
Before error
```

### (2) `set -u`

- 當腳本中使用了未定義的變數時，`set -u` 會讓腳本報錯並終止執行。

這可以避免在變數未正確初始化時導致的意外結果。

#### 範例

```bash
#!/usr/bin/env bash
set -u  # 開啟未定義變數錯誤模式

echo "Before using undefined variable"
echo $my_var  # my_var 未定義，腳本會在此處報錯並終止
echo "After using undefined variable"
```

執行結果：

```plaintext
Before using undefined variable
bash: my_var: unbound variable
```

### (3) `set -x`

- `set -x` 開啟命令追蹤模式，這會使每個執行的命令在執行前都被顯示出來，這對於調試腳本非常有用。

#### 範例

```bash
#!/usr/bin/env bash
set -x  # 開啟命令追蹤模式

my_var="Debugging"
echo $my_var
```

執行結果：

```plaintext
+ my_var=Debugging
+ echo Debugging
Debugging
```

### (4) `set +e` 和 `set +x`

- `set +e` 可以關閉 `set -e` 模式，允許後續命令即使失敗也不會終止腳本執行。

- `set +x` 則可以關閉命令追蹤模式。

#### 範例

```bash
#!/usr/bin/env bash
set -e  # 開啟錯誤終止模式
false   # 此處命令失敗，腳本會終止

set +e  # 關閉錯誤終止模式
false   # 這次命令失敗，不會終止腳本
echo "Script continues"
```

執行結果：

```plaintext
Script continues
```

## 2. 其他有用的 `set` 選項

### (1) `set -o pipefail`

- 當使用管道時，默認情況下，腳本只會返回最後一個命令的退出狀態。

`set -o pipefail` 使得如果管道中的任何一個命令失敗，腳本會返回失敗的退出狀態，這對於保證腳本的穩定性非常重要。

#### 範例

```bash
#!/usr/bin/env bash
set -o pipefail  # 確保管道中的任意命令失敗時返回錯誤

false | true   # false 命令失敗，腳本會返回錯誤狀態
echo "This will not be printed"
```

### (2) `set -n`

- `set -n` 可以讓腳本進入語法檢查模式，腳本中的命令不會被真正執行，但會進行語法檢查。

這對於確認腳本的正確性非常有用。

#### 範例

```bash
#!/usr/bin/env bash
set -n  # 開啟語法檢查模式

echo "This will not be executed"
```

執行結果：

```plaintext
（無輸出，因為命令未執行）
```

### (3) `set -v`

- `set -v` 開啟逐行輸出模式，這會讓每行腳本在執行之前顯示出來，幫助進一步了解腳本的運行過程。

#### 範例

```bash
#!/usr/bin/env bash
set -v  # 開啟逐行輸出模式

my_var="Verbose mode"
echo $my_var
```

執行結果：

```plaintext
#!/usr/bin/env bash
set -v
my_var="Verbose mode"
echo $my_var
Verbose mode
```

## 3. `set` Scope

當你在 `scriptA.sh` 中使用 `. ./scriptB.sh`，這是一個「source」的操作，

表示 `scriptB.sh` 會在當前的 shell 中執行，而不是在子 shell 中執行。

因此，`scriptB.sh` 中的 `set` 命令會覆蓋 `scriptA.sh` 中的 `set` 命令，並且不會開啟新的 shell 環境。

讓我們逐步來看發生了什麼：

1. `scriptA.sh` 一開始設置了 `set -xe`：

   - `-x`: 在執行每個命令之前，會顯示該命令（用於調試）。

   - `-e`: 當命令出錯時（返回非零值），腳本會立即退出。

2. 接著執行了 `. ./scriptB.sh`，這不是子 shell，
   而是 source，也就是說 `scriptB.sh` 的內容會在當前 shell 中執行。

3. 當進入 `scriptB.sh` 時，它執行了 `set -u`：

   - `-u`: 當使用未設置的變量時，會報錯並終止執行。

由於 `scriptB.sh` 中的 `set -u` 覆蓋了 `scriptA.sh` 中的 `set -xe`，
因此腳本執行到這裡時，`-x` 和 `-e` 的效果會消失，只剩下 `-u` 生效。

### 執行順序及效果

1. `scriptA.sh` 進入時，開啟了 `-xe`，所以會顯示執行的每條命令並在遇錯誤時停止。

2. 進入 `scriptB.sh` 後，`set -u` 會覆蓋 `set -xe`，因此從此時開始：

   - 未設置的變量使用會導致腳本出錯。

   - 不再顯示每個命令的執行過程（`-x` 失效）。

   - 也不會在出錯時自動退出（`-e` 失效）。

3. `scriptB.sh` 中會顯示 "scriptB"（因為 `echo "scriptB"` 沒有使用未設置的變量，不受 `-u` 的影響）。

4. 如果接下來回到 `scriptA.sh`，會繼續執行剩下的部分，
   但此時的 `set` 狀態仍然是 `scriptB.sh` 中設置的 `set -u`。

   `scriptA.sh` 中的 `set -xe` 不會自動恢復，
   除非你手動在 `scriptB.sh` 中將它們重新設置。

因此，最後的結果是 `scriptB.sh` 中的 `set -u` 覆蓋了 `scriptA.sh` 中的 `set -xe`。

## 5. 組合使用 `set`

你可以將多個 `set` 選項結合使用，來控制腳本的行為。例如：

```bash
#!/usr/bin/env bash
set -euxo pipefail  # 開啟多個選項

my_var="Combining set options"
echo $my_var
```

這會同時開啟：

- `-e`：命令失敗時終止腳本

- `-u`：未定義變數報錯

- `-x`：命令追蹤

- `-o pipefail`：管道命令失敗時報錯

## 6. 總結

- `set` 命令可以幫助你控制腳本的執行方式，根據需要開啟或關閉不同的 shell 行為。

- 常用選項如 `-e`、`-u`、`-x` 等，可以大大提升腳本的可調試性和健壯性。

- 透過組合使用 `set` 選項，你可以靈活地管理腳本的執行流程，避免常見錯誤並提高效率。

- 當你使用 `source` (`. ./scriptB.sh`) 來執行腳本時，當前 shell 中的 `set` 設定
  會被覆蓋，因此需要特別注意作用範圍與 `set` 的行為變更。
