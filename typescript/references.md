# `tsconfig.json` 中的 `references`

`references` 可以實現 **專案引用（Project References）**

這個功能允許將大型專案拆分為多個子專案，從而提升編譯效率、促進模組化設計並增強可維護性。

## 什麼是 Project References？

**專案引用** 是 TypeScript 提供的一種機制，允許一個專案依賴於其他專案。

這對於大型 code base、monorepo 或需要分層管理的應用特別有用。

通過專案引用，可以實現增量編譯，減少編譯時間，並促進 source code 的模組化。

## 如何在 `tsconfig.json` 中配置 `references`

### 步驟一：為每個子專案設置 `tsconfig.json`

每個子專案的 `tsconfig.json` 必須包含以下關鍵配置：

- **`compilerOptions.composite`**：設置為 `true`，這是使用 references 的前提條件。

**範例：**

```json
// 子專案 A 的 tsconfig.json
{
  "compilerOptions": {
    "composite": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true
  },
  "include": ["src"]
}
```

### 步驟二：在根專案中配置 `references`

根專案的 `tsconfig.json` 將引用所有子專案，通過 `references` 屬性來指定依賴關係。

**範例：**

```json
// 根專案的 tsconfig.json
{
  "files": [],
  "references": [{ "path": "./packages/projectA" }, { "path": "./packages/projectB" }]
}
```

### 步驟三：編譯專案

使用 `tsc --build` 命令來編譯專案，TypeScript 會根據 `references` 的配置，自動處理依賴關係並進行增量編譯。

```bash
tsc --build
```

## `references` 的用途

1. **提高編譯效率**：通過增量編譯，只重新編譯發生變化的部分，顯著減少編譯時間。

2. **模組化管理**：將大型專案拆分為多個小型、可管理的子專案，便於維護和擴展。

3. **依賴管理**：明確定義專案之間的依賴關係，避免循環依賴，提高 source code 質量。

4. **協作開發**：在團隊中不同成員可以專注於不同的子專案，提高協作效率。

## 配置子專案之間的依賴關係

當子專案 B 引用子專案 A 時，編譯子專案 B 時，TypeScript 會自動檢查並編譯子專案 A（如果有變更），然後再編譯子專案 B。

**範例：**

```json
// 子專案 B 的 tsconfig.json
{
  "compilerOptions": {
    "composite": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true
  },
  "include": ["src"],
  "references": [{ "path": "../projectA" }]
}
```

### 編譯行為

1. **編譯子專案 A**：

   - 只會編譯子專案 A 自己。

2. **編譯子專案 B**：
   - 會先編譯子專案 B 所引用的子專案 A，然後編譯子專案 B 自己。

## 子專案的 `compilerOptions` 和 `include` 設定

每個子專案的 `tsconfig.json` 中的 `compilerOptions` 和 `include` 設定是 **相互獨立** 的，不會直接互相影響。

這允許每個子專案根據自身需求進行特定的編譯配置。

### 使用 `extends` 共享配置

若希望多個子專案擁有相同的 `compilerOptions`，可以使用基礎配置（如 `tsconfig.base.json`），並在子專案中使用 `extends` 來繼承這些設定。

**範例：**

```json
// 根專案的 tsconfig.base.json
{
  "compilerOptions": {
    "strict": true,
    "target": "ES6",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src"
  }
}
```

```json
// 子專案 A 的 tsconfig.json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "composite": true
  },
  "include": ["src"],
  "references": []
}
```

```json
// 子專案 B 的 tsconfig.json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "composite": true
  },
  "include": ["src"],
  "references": [{ "path": "../projectA" }]
}
```

子專案 A 和 B 都繼承了根專案的 `compilerOptions`，確保配置的一致性，同時允許子專案進行獨立的自定義(override 根專案的 compilerOptions)。

## 最佳實踐

1. **統一專案結構**：保持子專案的目錄結構一致，便於管理和引用。

2. **自動化腳本**：編寫腳本來自動生成和更新 `tsconfig.json` ，減少手動配置。

3. **持續集成（CI）**：在 CI 流程中整合 `tsc --build`，確保每次提交都經過完整的編譯和檢查。

4. **清晰的文檔**：為專案引用設置清晰的文檔，幫助團隊成員理解專案結構和依賴關係。

5. **避免循環依賴**：確保專案之間沒有循環引用。可以使用工具如 [Madge](https://github.com/pahen/madge) 來檢查循環依賴。

## 範例專案結構

以下是一個使用有 references 的範例 monorepo：

```plain
my-monorepo/
├── tsconfig.base.json
├── tsconfig.json
├── packages/
│   ├── projectA/
│   │   ├── src/
│   │   │   └── index.ts
│   │   └── tsconfig.json
│   └── projectB/
│       ├── src/
│       │   └── index.ts
│       └── tsconfig.json
└── node_modules/
```

### 根專案的 `tsconfig.base.json`

定義所有子專案共用的 `compilerOptions`。

```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "skipLibCheck": true,
    "outDir": "./dist",
    "rootDir": "./src"
  }
}
```

### 根專案的 `tsconfig.json`

負責引用所有子專案，便於一次性編譯整個 monorepo。

```json
{
  "files": [],
  "references": [{ "path": "./packages/projectA" }, { "path": "./packages/projectB" }]
}
```

### 子專案 A 的 `tsconfig.json`

只會編譯 src/libs 目錄下的 TypeScript。

編譯後的 JavaScript 會根據 tsconfig 中的 outDir 設定，輸出到 outDir。

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "composite": true
  },
  "include": ["src/libs"],
  "references": []
}
```

### 子專案 B 的 `tsconfig.json`

只會編譯自身 src 目錄下的 TypeScript。

編譯子專案 B 時，TypeScript 會先檢查並編譯子專案 A（如果有變更）。

子專案 B 依賴於子專案 A 的編譯輸出（e.g. 子專案 A 目錄 outDir 中的 JavaScript），而非直接重新編譯子專案 A。

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "composite": true
  },
  "include": ["src"],
  "references": [{ "path": "../projectA" }]
}
```

## 上述範例的關鍵點總結

獨立編譯範圍：

- 子專案 A 和子專案 B 各自有獨立的編譯範圍，分別由各自的 include 設定決定。

- 子專案 A 只負責編譯其 src/libs，子專案 B 只負責編譯其 src。

- 專案引用的作用：

  - 子專案 B 通過 references 引用了 子專案 A，確保在編譯子專案 B 時，子專案 A 已經被編譯並且是最新的。

  - 子專案 B 不會直接包含或重新編譯子專案 A，而是依賴於子專案 A 的編譯 outDir。除非子專案 A 的 outDir 不存在，才會重新編譯子專案 A。

- 配置繼承與獨立性：

  - 兩個子專案通過 extends 繼承了基礎配置 (tsconfig.base.json)，確保共用相同的 compilerOptions 設定。

  - 每個子專案仍然可以在自己的 tsconfig.json 中去 override tsconfig.base.json 的 `compilerOptions`

## 結論

TypeScript 的 `references` 功能為大型專案提供了模組化和高效的編譯方式。

通過合理配置和使用，可以：

- **提高編譯效率**：只編譯必要的部分，減少不必要的編譯時間。

- **促進專案模組化**：將大型專案拆分為更小、更易管理的子專案。

- **確保依賴一致性**：自動處理專案之間的依賴，避免版本衝突和錯誤。

結合良好的專案結構和自動化工具，`references` 的使用將變得更加簡便和高效。

## 相關資源

- [TypeScript `tsconfig.json` 官方參考](https://www.typescriptlang.org/tsconfig)

- [TypeScript 官方文檔：Project References](https://www.typescriptlang.org/docs/handbook/project-references.html)

- [Madge GitHub 頁面](https://github.com/pahen/madge)
