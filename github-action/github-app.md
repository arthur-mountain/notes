# 什麼是 GitHub App？

GitHub App 是一種可以與 GitHub 平台進行深度整合的應用程式。

它允許開發者創建自定義的工具和擴展，以增強和自動化各種工作流程。

與傳統的 OAuth Apps 相比，GitHub App 擁有獨立的身份、更精細的權限控制，
並能與多個倉庫、組織或用戶進行互動，無需每個用戶單獨授權。

## GitHub App 的主要特點

### 1. 獨立身份

- **獨立身份**：每個 GitHub App 擁有自己獨立的身份，與個人用戶或組織帳戶分開。應用程式可以以自身的身份進行操作，而不依賴特定用戶的權限。

- **安全性**：由於 GitHub App 擁有獨立的身份，管理和控制其訪問權限更加安全，減少了因個人帳戶權限過大而帶來的風險。

### 2. 精細的權限控制

- **精確授權**：GitHub App 可以根據需求設定具體的權限，例如只讀取特定倉庫的內容、管理 Issues、提交 Pull Request 等。遵循最小權限原則，只賦予應用程式執行其功能所需的最低權限，提升了安全性。

### 3. Webhook 集成

- **即時響應事件**：當倉庫或組織發生指定事件（如新提交、Issue 更新、Pull Request 變更等）時，GitHub App 可以透過 Webhook 接收通知，並根據事件自動執行指定操作。

### 4. API 集成

- **API 互動**：GitHub App 透過 GitHub 的 REST 和 GraphQL API 與平台進行互動，可以獲取倉庫中的變更資訊、建立或關閉 Issue、審查 Pull Request 等。

### 5. 易於整合與自動化

- **與現有工具的整合**：GitHub App 可以與多種工具和平台集成，如持續整合（CI）/持續部署（CD）工具、代碼品質分析工具等，提供無縫的開發體驗。

### 6. 安裝與管理

- **靈活安裝**：GitHub App 可以安裝在個人帳戶、組織或特定的倉庫中，不同安裝位置可以擁有不同的權限配置。

- **集中管理**：組織管理者可以集中管理所安裝的 GitHub App，監控其使用情況，並根據需要調整權限或卸載應用程式。

## GitHub App 的應用場景

### 1. 自動化工作流程

GitHub App 可用於自動化日常的開發工作流程。

透過與持續整合/持續部署（CI/CD）工具的集成，開發者可以設定自動化的測試、部署管道。

例如，每當有人提交代碼時，GitHub App 可以觸發測試工具來檢查代碼品質，並在 Pull Request 中回饋測試結果。

### 2. 改善代碼審查

代碼審查是開發過程中的關鍵步驟，GitHub App 能幫助自動化和改進這一過程。

許多 GitHub App 提供代碼分析功能，如 Lint 工具或安全漏洞掃描，自動檢查代碼中的問題，
並在提交或 Pull Request 中提出建議，減少了手動審查的負擔。

### 3. 項目管理與協作

GitHub App 可以協助進行項目管理。

例如，當開發人員創建 Issue 時，GitHub App 可以自動標記、分類，甚至將 Issue 分配給特定的開發者。

此外，許多項目管理工具（如 Jira）都有 GitHub App 集成，開發者可以在多個平台之間無縫協作和管理任務。

### 4. 社群貢獻與開源

對於開源項目，GitHub App 也能發揮重要作用。

許多開源項目需要處理大量的 Pull Request 和 Issue，
GitHub App 能夠自動化這些任務，根據貢獻者的行為評分或提供模板來指導新貢獻者，幫助維護者減輕管理負擔。

## 如何開發 GitHub App

開發 GitHub App 需要遵循以下基本步驟：

### 1. 建立 GitHub App

- **進入開發者設定**：登入 GitHub 帳戶，前往 GitHub 開發者設定頁面。

- **創建新應用程式**：點擊「**New GitHub App**」，根據需求配置應用程式的名稱、權限、Webhook 事件和其他細節。

### 2. 編寫程式碼

- **選擇程式語言**：使用你熟悉的程式語言（如 Node.js、Golang）來開發 GitHub App。

- **使用 GitHub API**：透過 GitHub 的 REST 或 GraphQL API 與平台進行互動，實現所需的功能。

### 3. 註冊 Webhook

- **訂閱事件**：在 GitHub App 設定頁面中，訂閱你要接收的 Webhook 事件，如 `push`、`pull_request`、`issues` 等。

- **配置 Webhook URL**：設定 Webhook 的接收端點，確保你的應用程式能夠接收到 GitHub 發送的事件通知。

### 4. 測試與部署

- **測試環境**：在開發和測試過程中，使用 GitHub 提供的沙盒環境或創建測試倉庫來模擬應用程式的行為。

- **部署應用程式**：測試完成後，將應用程式部署到生產環境中，確保其在真實環境下正常運行。

## 如何設置 GitHub App

以下是詳細的步驟，教你如何從頭開始創建並配置一個 GitHub App。

### 步驟一：建立 GitHub App

1. **登入 GitHub 帳戶**：登入你的 GitHub 帳戶。

2. **進入開發者設定**：

   - 點擊右上角的個人頭像，選擇「**Settings（設定）**」。

   - 在左側選單中，點擊「**Developer settings（開發者設定）**」。

3. **創建新的 GitHub App**：

   - 在「Developer settings」頁面，點擊左側的「**GitHub Apps**」。

   - 點擊「**New GitHub App**」按鈕開始創建。

4. **配置應用程式詳細資訊**：

   - **應用名稱（GitHub App name）**：為你的應用程式命名。

   - **描述（Description）**：簡短描述應用程式的用途。

   - **首頁 URL（Homepage URL）**：填入你的應用程式首頁或相關網站的 URL。

   - **Webhook URL（可選）**：如果需要接收 GitHub 事件，填入 Webhook 接收端點的 URL。

   - **Webhook Secret（可選）**：設定一個 Webhook Secret，用於驗證來自 GitHub 的 Webhook 請求。

   - **權限設定（Permissions）**：根據應用程式的需求，設定倉庫權限和使用者權限。

   - **訂閱事件（Subscribe to events）**：選擇應用程式需要監聽的 GitHub 事件。

5. **完成創建**：填寫完所有必要資訊後，點擊「**Create GitHub App**」完成創建。

### 步驟二：生成並下載私鑰

1. **生成私鑰**：

   - 在剛創建的 GitHub App 設定頁面，滾動至「**Private keys**」區域。

   - 點擊「**Generate a private key**」按鈕。這將下載一個 `.pem` 格式的私鑰文件到你的電腦。

   - **重要**：請妥善保管這個私鑰文件，因為它只會顯示一次，且後續無法再次下載。

2. **保存 App ID**：

   - 在 GitHub App 的設定頁面，你會看到「**App ID**」。記下這個 ID，後續在配置應用程式時會用到。

### 步驟三：安裝 GitHub App

1. **安裝應用程式**：

   - 在 GitHub App 設定頁面，點擊「**Install App**」按鈕。

2. **選擇安裝範圍**：

   - 選擇將應用程式安裝到個人帳戶、組織或特定的倉庫。

   - 選擇需要安裝的倉庫，根據應用程式的用途選擇適當的範圍。

3. **確認安裝**：

   - 完成範圍選擇後，點擊「**Install**」完成安裝。

### 步驟四：配置應用程式

1. **使用 GitHub App Token**：

   - GitHub App 需要使用 JWT（JSON Web Token）來進行身份驗證，並生成訪問 Token。

2. **設定 Secrets**：

   - 前往你的倉庫，點擊「**Settings**」>「**Secrets and variables**」>「**Actions**」。

   - 點擊「**New repository secret**」並新增以下 Secrets：

     - `GITHUB_APP_ID`：填入 GitHub App 的 App ID。

     - `GITHUB_APP_PRIVATE_KEY`：填入私鑰的內容（建議使用 Base64 encode 或其他安全方式儲存）。

     - `GITHUB_APP_INSTALLATION_ID`：填入應用程式的 Installation ID。

3. **在 GitHub Actions 中使用 Token**：

   - 在你的工作流程文件（例如 `.github/workflows/main.yml`）中，
     使用上述步驟生成的 Token 來進行需要身份驗證的操作。

     Example：

     ```yaml
     jobs:
       build:
         runs-on: ubuntu-latest
         steps:
           - name: 🔑 Create GitHub Token
             uses: tibdex/github-app-token@v1
             id: github-app-token
             with:
               app_id: ${{ secrets.GITHUB_APP_ID }}
               private_key: ${{ secrets.GITHUB_APP_PRIVATE_KEY }}
               installation_id: ${{ secrets.GITHUB_APP_INSTALLATION_ID }}

           - name: 使用 Token 進行操作
             run: |
               echo "使用生成的 Token 進行操作"
             env:
               GITHUB_TOKEN: ${{ steps.github-app-token.outputs.token }}
     ```

   **注意**：上述示例使用了第三方的 GitHub Action [tibdex/github-app-token@v1](https://github.com/tibdex/github-app-token) 來生成 GitHub App 的 Token。
   請在使用前確認該 Action 的安全性和可靠性。

## Webhook 配置完成後，GitHub App 的自動化流程如何運行

當你完成了 Webhook URL、Webhook Secret 以及訂閱事件的設置後，

GitHub App 將在指定的事件發生時自動執行相應的自動化流程。以下是整個運行過程的詳細說明：

### 1. 事件觸發

當你在 GitHub 倉庫中進行某些操作（如推送代碼、創建 Pull Request、開啟 Issue 等），這些操作會觸發你所訂閱的事件。

### 2. GitHub 發送 Webhook 請求

GitHub 會向你設置的 Webhook URL 發送一個 HTTP **`POST`** 請求，包含以下內容：

- **事件類型**：標明是哪種類型的事件（如 `push`、`pull_request`）。

- **事件 Payload**：包含有關事件的詳細數據，例如推送的分支、提交資訊、Pull Request 的詳情等。

- **簽名頭（`X-Hub-Signature-256`）**：用於驗證請求的真實性，這是根據你設置的 Webhook Secret 生成的。

### 3. 應用程式接收並驗證 Webhook 請求

你的應用程式（指向 Webhook URL 的服務）收到這個請求後，會進行以下操作：

1. **驗證簽名**：

   - 使用你設置的 Webhook Secret，計算請求的簽名並與請求頭中的簽名進行比對，確保請求確實來自 GitHub 並且未被篡改。

2. **解析 Payload**：

   - 分析請求體中的事件數據，提取需要的資訊。

### 4. 根據事件執行自動化任務

驗證通過後，應用程式根據事件的類型和內容執行相應的自動化任務。以下是一些常見的自動化操作示例：

- **自動合併 Pull Request**：

  - 檢查 Pull Request 是否滿足自動合併的條件，例如所有測試通過、沒有衝突等。

  - 使用 GitHub API 自動將 Pull Request 合併到目標分支。

  - 發送通知告知合併結果。

- **自動部署**：

  - 當有代碼推送到主分支時，觸發部署流程，將最新的代碼部署到生產環境。

  - 記錄部署過程中的日誌，更新部署狀態。

- **自動管理 Issues**：

  - 為新創建的 Issues 自動添加標籤，或將 Issue 分配給相關的團隊成員。

### 5. 與 GitHub Actions 結合

你也可以將 GitHub App 與 GitHub Actions 結合使用，
實現在 GitHub Actions 中使用 GitHub App 的 Token 進行身份驗證，執行更複雜的自動化流程。

例如：

1. **Webhook 觸發 GitHub Action**：

   - 當 Webhook 接收到事件後，可以觸發一個 GitHub Actions 的工作流程。

2. **工作流程運行**：

   - 在 GitHub Actions 中，使用之前創建的 GitHub Token 來進行身份驗證，執行所需的操作，如自動合併、部署、測試等。

### 6. 回應 GitHub

應用程式在處理完 Webhook 請求後，需要返回適當的 HTTP 狀態碼（通常是 `200 OK`），以確認已成功接收和處理該請求。

如果處理過程中出現錯誤，應返回相應的錯誤碼，GitHub 可能會根據設定進行重試。

## GitHub App 的優勢

### 優勢

- **強大的自動化能力**：GitHub App 能自動化處理大量繁瑣任務，減少人為錯誤，提升開發效率。

- **精確的權限管理**：應用程式可以針對特定的倉庫或事件進行授權，確保安全性。

- **靈活的整合能力**：GitHub App 可以與多種工具和平台集成，提供無縫的開發體驗。

## 結論

- 自動化工作流程、提升協作效率，還能幫助團隊更好地管理項目。
