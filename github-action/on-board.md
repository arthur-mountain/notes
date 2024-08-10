# 什麼是 GitHub Actions？

GitHub Actions 是 GitHub 提供的一個自動化工具，
允許你在指定的事件（例如提交代碼、開 Pull Request等）發生時，觸發特定的工作流程（Workflow）。

你可以用它來進行自動測試、部署、打包等操作。

舉例：

- 可以模擬在不同的機器或環境中執行

- 可以使用 Docker 容器

- 可以是定 scheduler 任務

- etc.

## 基本概念

由大至小為：

- `Workflow` (工作流程): `由一個或一系列的 Jobs 組成`，定義了你想自動化的整個過程。
  通常定義在 .github/workflows/ 目錄下的 YAML 檔案中。

- `Job` (任務): 是 Workflow 中的`一個步驟集合，由一個或一系列的 Steps 組成`。

- `Step` (步驟): 是 Job 中的`一個具體操作，由一個或一系列的 Actions 組成`。
  可以執行命令或運行其他 Action。

- `Action` (動作): 是可以重複使用的命令或任務單位。
  GitHub 提供了許多內建的 Actions，並且你也可以創建自定義的 Action。

## 簡單的 Workflow 範例

- `name`: 工作流程的名稱，在 GitHub 的 Actions 標籤頁中顯示。

- `on`: 定義了觸發工作流程的事件，當 push 到 main 分支或開 PR 到 main 分之 時，執行 npm install 和 npm test。

- `jobs`: 定義了工作流程中的任務集合。範例為 test Job。

  - `runs-on`: 指定任務運行的機器/環境，這裡使用 ubuntu-latest。

- `steps`: 定義了任務中的各個步驟，包括：

  - 使用 actions/checkout Action，checkout 到我們的 repository。

    可以理解為 -> 在一個全新的 CI 的環境內執行 git clone

    因為沒有指定參數，所以就是會 git clone 我們當前定義 .github 的 repository

  - 設定 Node.js 環境。

  - 安裝 dependencies。

  - 運行測試命令。

```yaml
# .github/workflows/ci.yaml
name: CI

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "16"

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test
```
