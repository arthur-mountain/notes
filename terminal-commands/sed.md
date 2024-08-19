# sed

sed 是一個流編輯器，主要用於處理和轉換文本

sed 可以處理文件或管道輸入的文本數據，並能夠對其中的內容進行過濾、替換、插入、刪除等操作。

Examples:

- 文本替換：

  ```bash
  # 將 input.txt 中的 "apple" 全部替換為 "orange"，並將結果寫入 output.txt。
  sed 's/apple/orange/g' input.txt > output.txt
  ```

- 文本插入和添加：

  ```bash
  # 在指定行之前或之後插入新行內容。
  # 第二行之前插入 "This is a new line"。
  sed '2i\This is a new line' input.txt
  ```

- 刪除行：

  ```bash
  # 刪除 input.txt 的第三行
  sed '3d' input.txt
  ```

- 選擇性處理文本：

  ```bash
  # 將所有包含 pattern 的行中的 old 替換為 new。
  sed '/pattern/s/old/new/' input.txt
  ```

- 文本轉換：

  ```bash
  # 將 input.txt 中的所有數字替換為 [NUMBER]。
  sed 's/[0-9]\+/[NUMBER]/g' input.txt
  ```
