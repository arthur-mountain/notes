# Subtree

`git subtree` 是 Git 中用於管理子專案（subproject）的指令。

它提供了一種將其他 Git 倉庫嵌入到主倉庫的方法，並允許在主倉庫中直接推送和拉取子倉庫的更新。

與子模組（submodule）相比，
`git subtree` 更加簡單直觀，避免了子模組的複雜性，適合需要頻繁同步子專案內容的情況。

## 常用指令及用法

### 1. 添加子專案

將另一個倉庫作為子專案添加到當前倉庫中：

```bash
git subtree add --prefix=<目錄> <遠端倉庫> <分支>
```

- `--prefix=<目錄>`：指定子專案放置的目錄。

- `<遠端倉庫>`：子專案的遠端倉庫 URL。

- `<分支>`：子專案的分支名稱。

**範例**：

將 `https://github.com/other/repo.git` 的 `master` 分支添加到主倉庫的 `subtree` 目錄中：

```bash
git subtree add --prefix=subtree https://github.com/other/repo.git master
```

### 2. 從子專案拉取更新

從子專案的遠端倉庫拉取更新並合併到主倉庫：

```bash
git subtree pull --prefix=<目錄> <遠端倉庫> <分支>
```

**範例**：

從子專案的 `master` 分支拉取更新：

```bash
git subtree pull --prefix=subtree https://github.com/other/repo.git master
```

### 3. 推送更新到子專案

將主倉庫中子專案的修改推送到子專案的遠端倉庫：

```bash
git subtree push --prefix=<目錄> <遠端倉庫> <分支>
```

**範例**：

將主倉庫中 `subtree` 目錄的修改推送到子專案的 `master` 分支：

```bash
git subtree push --prefix=subtree https://github.com/other/repo.git master
```

### 4. 分割子專案

將子專案從主倉庫中分離出來，創建一個新分支包含這些變更：

```bash
git subtree split --prefix=<目錄> --branch=<新分支>
```

**範例**：

將 `subtree` 目錄中的內容分割到名為 `subtree-branch` 的新分支中：

```bash
git subtree split --prefix=subtree --branch=subtree-branch
```

## 總結

`git subtree` 提供了一種有效的方式來管理和同步嵌入的子倉庫，避免了子模組的複雜性。

通過使用 `add`、`pull`、`push` 和 `split` 等指令，
可以方便地在主倉庫和子專案之間同步變更，適合需要頻繁更新子專案的情況。
