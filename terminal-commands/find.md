# find

## Syntax

```bash
  find [path] [options]
```

## Options

1. -type: 指定要查找的文件類型。
   1.1. f: 檔案

   Example:

   ```bash
   find . -type f
   ```

2. -name: 指定要查找的文件名。

   2.1. \(-name $name1 -o -name $name2 ...\): 查找多個文件名。

   Example:

   ```bash
   find . -name '*.txt'

   find . \(-name '*.ts' -o -name '*.tsx' \)
   ```

3. ! -path: 排除指定路徑。

   Example:

   ```bash
   find . ! -path "*/node_modules/*" -exec cat {} \;
   ```

4. -exec: 對查找到的文件執行指定命令。

   4.1. {} 代表查找到的文件。

   4.2. \; 表示命令結束。

   4.3. \+ 表示命令結束，但是可以一次執行多個命令。

   Example:

   ```bash
   find . -name "\*.txt" -exec cat {} \;
   ```

TBD
