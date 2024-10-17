# Select

Bash 中的 `select` 語法提供了一種方便的方式來建立選單，讓使用者選擇多個選項之一。

### `select` 的基本語法

```bash
select var in list
do
    commands
done
```

- **`var`**: 這是用來存放使用者選擇的值的變數。

- **`list`**: 這是選單中顯示的選項，可以是一個用空格分隔的字串列表，或是變數陣列。

- **`commands`**: 根據使用者選擇執行的指令。這些指令在 `do` 和 `done` 之間。

- `select` 會自動列出選項，並提示使用者輸入對應的數字來選擇。

### `select` 的運作流程

1. **顯示選項列表**: `select` 會列出 `list` 中的所有選項，並為每個選項自動分配一個數字（從 1 開始）。

2. **等待輸入**: 使用者根據提示輸入一個數字來選擇選項。選擇的數字對應的選項會被存放在變數 `var` 中。

3. **執行循環**: `select` 會進入一個循環，每次根據使用者的選擇執行 `do` 到 `done` 之間的命令。如果輸入有效的數字，`var` 會被設置為對應選項。如果輸入無效或直接按 Enter，`var` 將設為空。

4. **退出循環**: 使用 `break` 可以退出 `select` 循環。

### 簡單範例

```bash
#!/usr/bin/env bash

PS3="Please select a fruit: "  # PS3 是選擇提示符
fruits=("Apple" "Banana" "Orange" "Quit")

select fruit in "${fruits[@]}"; do
    case $fruit in
        "Apple")
            echo "You selected Apple."
            ;;
        "Banana")
            echo "You selected Banana."
            ;;
        "Orange")
            echo "You selected Orange."
            ;;
        "Quit")
            echo "Exiting."
            break
            ;;
        *)
            echo "Invalid option. Please try again."
            ;;
    esac
done
```

### 運作方式

1. `PS3` 是一個專門用於 `select` 的提示符變數。在這個例子中，會提示 "Please select a fruit:"。

2. `select fruit in "${fruits[@]}"`：

   - `select` 會自動列出 `fruits` 陣列中的項目：

     ```
     1) Apple
     2) Banana
     3) Orange
     4) Quit
     ```

   - 使用者會輸入對應的數字來選擇，比如輸入 `1` 來選擇 `Apple`。

   - 選擇的值會存放在 `fruit` 變數中。

3. 根據使用者的選擇，進入 `case` 語句，執行相應的命令。如果選擇了 `Quit`，腳本會 `break` 循環，結束 `select`。

### 如何處理無效輸入

當使用者輸入一個無效選項，比如一個不在範圍內的數字，`select` 不會退出，只是將變數設為空，並再次提示用戶輸入。

這可以通過檢查變數是否為空來判斷無效輸入：

-z: 檢查變數是否為空，如果為空回傳 true。

```bash
select option in "Option 1" "Option 2" "Option 3"; do
    if [ -z "$option" ]; then
        echo "Invalid selection, please try again."
    else
        echo "You selected $option."
    fi
done
```

-n: 檢查變數是否不為空，如果不為空回傳 true。

```bash
select option in "Option 1" "Option 2" "Option 3"; do
    if [ -n "$option" ]; then
      echo "You selected $option."
    else
      echo "Invalid selection, please try again."
    fi
done
```

### 關鍵點

1. **`PS3` 提示符**:
   - `PS3` 是一個特殊變數，用於定義 `select` 提示符，這樣可以讓選擇過程更友好。默認情況下，`select` 的提示是空的，通常會顯示為 `#?`，因此最好設置 `PS3` 來提示使用者輸入。
2. **空輸入處理**:

   - 如果使用者直接按 Enter，`select` 不會更改變數的值，而是再次顯示選單並繼續循環。

3. **無效輸入**:

   - 如果輸入的數字超過選單範圍，變數會設為空，這時可以顯示錯誤訊息，提示使用者再次輸入。

4. **退出循環**:
   - `select` 本身是一個循環。要退出這個循環，可以使用 `break` 命令，通常是在使用者選擇某個選項時，比如 `Quit`。

### 完整的運作示例

當你執行上面的範例腳本時，Bash 會自動顯示如下的輸出（基於上面的範例）：

```
1) Apple
2) Banana
3) Orange
4) Quit
Please select a fruit:
```

當使用者輸入對應的數字後，會輸出相應的結果。

### `select` 常見用法

1. **用於多選項選擇**：`select` 最常用於讓使用者選擇多個選項之一，特別是在互動式腳本中。

2. **與陣列結合使用**：`select` 會非常方便地和陣列結合，使用者選擇的選項可以存入變數並用於後續操作。

3. **輔助用戶輸入**：`select` 提供了一個簡單的方式來避免用戶手動輸入錯誤的選項，因為選項是顯示給使用者的。

### 結論

`select` 是 Bash 中一個非常實用的工具，用來建立互動式選單。

它簡單易用，並且可以自動處理選項的顯示和選擇邏輯。結合 `case` 語句或條件判斷，能夠有效地處理使用者的輸入並執行對應的命令。
