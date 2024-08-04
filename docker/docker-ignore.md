# Summary

`.dockerignore` 是用來把特定檔案或目錄排除在 Docker build 的過程中的設定檔.

1. .dockerignore v.s. .gitignore

- .dockerignore 跟 .gitignore 是不同的東西, docker 並不會去讀 .gitignore 的設定

- git 主要是排除一些不想被 git 追蹤，也不想被推到遠端的東西，但在 docker build 的過程中，這些檔案可能還是會需要被加入到 image 中

2. 如果多個目錄內有多個 .dockerignore 的話，會去讀哪個

- 在 docker build 的過程中，會讀當前 context 的 .dockerignore，而不會去讀別的 .dockerignore

  舉例：

  ```bash
  docker build .  # 讀取當前目錄的 .dockerignore，且 docker image 內會以`當前`目錄當作 build context

  docker build .. # 讀取上層目錄的 .dockerignore，且 docker image 內會以`上層`目錄當作 build context
  ```

  > NOTE:
  >
  > docker image 內會以哪層當作 build context 是什麼意思？
  >
  > 代表在 Dockerfile 裡面任何指令的路經都是以 build context 為基準的
  >
  > 例如：
  >
  > COPY ./src /app/ # 這裡的 ./src 就是指 build context 的 src 目錄
  >
  > COPY ../src /app/ # 這裡的 ../src 就是指 build context 的上一層目錄的 src 目錄

以此類推

# Syntax

要如何指定要排除的檔案或目錄呢？

#### 以當前 context 為 root，以下是一些範例

- `.nvmrc` -> root 中的 .nvmrc 檔案和 .nvmrc 目錄

- `.nvmrc/` -> root 中的 .nvmrc 目錄

- `.nvmrc*` -> root 中的有 .nvmrc 前綴的檔案，如: .nvmrc-1, .nvmrc-2, ...

- `.nvmr?` -> root 中的有 .nvmr{任何字元} 的檔案，如: .nvmrb, .nvmrz, ...

- `*.nvmrc*` -> root 中的有 .nvmrc 在檔名中的檔案，如: test.nvmrc-1, test-2.nvmrc, ...

- `!.nvmrc` -> 反向表示法，代表要包含該檔案，通常和其他語法一起使用.

  例如: root 目錄下，README.md 會被加進 docker build 中，但其他的 markdown 檔案不會

  ````txt

  ```txt
  *.md
  !README.md
  ````

#### 以上都只會在 root 中有效，如果要在所有子目錄中也有效的話，可以使用以下語法

- `**/.nvmrc` -> 所有子目錄中的 .nvmrc 檔案和 .nvmrc 目錄

- `**/.nvmrc/` -> 所有子目錄中的 .nvmrc 目錄

#### `**` 代表所有子目錄，如果只要指定特定幾層目錄的話可以這樣

7. `*/*/.nvmrc/` -> root 底下`兩層`目錄中的 .nvmrc 目錄 -> 如：first/second/.nvmrc

#### 寫註解在 .dockerignore

可以使用 `#` 來做註解

```txt
# This is a comment
```

#### 以上語法可以自行組合使用

## .dockerignore 範例

<strong style="color:yellow">
  請記得！ 根據自己的需求來撰寫 .dockerignore，以下只是一個範例
</strong>

```txt
# Github
.github

# Git repo
.git

# System
.DS_Store
**/.DS_Store

# IDEs and editors
# .vscode 大部分情況通常只會在 root
# 如果子目錄也有，可以再加上 **/.vscode
# 但 .editorconfig 在子目錄可能也有，所以像下面這樣寫
.vscode
.editorconfig
**/.editorconfig

# Docker self
# 排除各式的 Dockerfile
# 像是 TestDockfile, Dockerfile.dev, Dockerfile.prod, ...
*Dockerfile*
.dockerignore
**/*Dockerfile*
**/.dockerignore

# Logs
# 像是 yarn.debug.log, npm-debug.log, ...
*debug*
**/*debug*

# Dependency directories
node_modules
**/node_modules

# Compiled output
dist
tmp
build
**/dist
**/tmp
**/build

# Markdown
*.md
**/*.md

# typescript
*.tsbuildinfo
**/*.tsbuildinfo
```
