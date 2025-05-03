# docker-compose 快速總覽與常見陷阱整理

## ✅ 基本概念

`docker-compose` 是 Docker 提供的工具，
可透過一個 `docker-compose.yml` 檔案，定義與管理整組服務(containers)。

---

## ✅ 執行流程

1. 讀取當前路徑下的 `.env` 檔案(非 container 的 env)

2. 解析 `docker-compose.yml`

   - 可以想像成：

     在執行之前，`docker-compose` 會先載入執行目錄下的 `.env` 檔案，

     並將其中的變數套用到 `docker-compose.yml` 中使用 `${}` 語法的位置。

   - 這個過程有點像 GitHub Actions 中的變數替換機制：

     先將整份 YAML 當成模板，把變數代入成實際的值，然後才進行執行。

- 類似於 github action yaml，也是會先把整份 yaml 當作 string 替換掉變數，在執行

3. 建立資源與準備階段

   - **若有 build 設定**，則執行 `docker build`，傳入 build context、Dockerfile 與 build args 等。

     - build.context

       build.context 是相對於執行 docker-compose 指令時的當前工作目錄。

       在 Dockerfile 內的路徑也會以 context 為基礎來配置。

     - build.dockerfile

       build.dockerfile 是相對於 context 的路徑，可以指定自定義的 Dockerfile 文件路徑。(注意，檔名一定要寫！)

       如果 docker-compose.yml 和 Dockerfile 不在同一目錄下，可以透過此參數來明確指定 Dockerfile 的位置。

   - **若使用 image**，則嘗試從本地或遠端拉取(`docker pull`)。

   - **建立 volumes、networks 等資源**(如有定義，或預設 network)。

4. 啟動 container

   - 將建立好的 volumes 掛載進 container。

   - 接上對應 network，設定 ports、env 等配置。

   - 啟動 container，準備進入執行階段。

5. 執行 container 的 `CMD` 或 `ENTRYPOINT`

   - 啟動後，執行 Dockerfile 中定義的 `CMD` 或 `ENTRYPOINT`，正式進入應用執行邏輯。

---

## ✅ docker-compose.yml 結構重點

```yaml
services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
      args:
        - BUILD_ENV=${BUILD_ENV}
    image: my-app
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    env_file:
      - .env.app
    volumes:
      - ./src:/app
    depends_on:
      - db

  db:
    image: postgres
    environment:
      - POSTGRES_USER=xxx
      - POSTGRES_PASSWORD=xxx
```

---

## ✅ 環境變數的三種來源與差異

| 方式          | 用途         | 插值支援 | 範例                    |
| ------------- | ------------ | -------- | ----------------------- |
| `.env`        | compose file | ✅       | `${APP_PORT}`           |
| `environment` | container    | ✅       | `NODE_ENV=${ENV}`       |
| `env_file`    | container    | ❌       | `.env.app` 檔案中的變數 |

> `.env` 僅影響 compose 檔案本身，**不會自動傳入 Dockerfile 或 container**

---

## ✅ Dockerfile 與變數傳遞

- **ARG**：只存在於 **build 階段**

- **ENV**：存在於 **build + runtime**

- **CMD**：執行於 **runtime**，所以無法使用 ARG

```dockerfile
ARG MY_ARG
ENV MY_ENV=${MY_ARG}
CMD echo ${MY_ENV} # OK

CMD echo ${MY_ARG} # ❌ 無效
```

在 compose 傳入變數：

```yaml
build:
  args:
    - MY_ARG=${VALUE}
environment:
  - MY_ENV=${VALUE}
```

---

## ✅ 常見誤區與解法

| 問題                  | 原因                    | 解法                           |
| --------------------- | ----------------------- | ------------------------------ |
| Dockerfile 取不到變數 | `.env` 不會傳進 build   | 用 `build.args` 傳入           |
| CMD 無法讀 ARG        | ARG 不存在於 runtime    | 改用 ENV 並明確定義            |
| `.env` 改了沒效果     | 舊 container 沒 rebuild | 用 `docker-compose up --build` |
| `env_file` 變數無插值 | 不支援 `${}` 語法       | 改用 `environment` 明確指定    |

---

## ✅ 建議使用方式

- `.env`：統一集中管理 compose 中的變數
- `env_file`：集中管理 container 的環境變數
- `environment`：動態/特定變數，或需要插值時使用
- `build.args`：傳值給 Dockerfile 的 ARG
- `ENV`：build + runtime 都要用的變數
- `ARG`：只用在 build 階段的變數(不能用在 CMD)

---

## 🧠 小結

- `.env` 是 compose 的變數檔，不等同於 container 環境變數
- `env_file` 方便集中管理，但不支援變數插值
- `environment` 最靈活，支援從 `.env` 取值
- ARG 和 ENV 使用時要考慮 **build vs runtime**
- CMD 不能用 ARG，記得轉成 ENV

---

## 補充

在使用 `docker-compose` 執行多個 YAML 文件時，可以透過 `-f` 參數來指定多個 `docker-compose.yml` 文件。

這樣的操作會將這些 YAML 文件中的內容合併，並且以一種層疊的方式進行處理。

這裡是具體範例：

```bash
docker-compose \
  -f docker-compose.network.yml \
  -f ./client/docker-compose.yml \
  -f ./server/docker-compose.yml \
  up
```

### 如何工作？

- 當使用多個 `-f` 參數時，`docker-compose` 會根據文件的順序依次加載各個 YAML 文件，並合併其內容。

- 在合併過程中，後面指定的文件會覆蓋前面文件中相同的設定。

  也就是說，**後面的文件會優先使用**，並且會覆蓋掉前面文件中已經定義過的相同內容。

  例如：

  - 在 `docker-compose.network.yml` 中可能定義了一些網路設定，然後在 `./server/docker/docker-compose.yml` 中重新定義或調整了一些網路設置。

    此時，`./server/docker/docker-compose.yml` 中的設定會覆蓋掉 `docker-compose.network.yml` 中的設定。

### 使用場景

這樣的方式通常用於以下情境：

1. **分離環境配置**:

   例如，可能有一個基礎的 `docker-compose.yml` 文件用於開發環境，然後有一個額外的文件 `docker-compose.prod.yml` 用於生產環境設定。

   這樣，可以根據不同的環境加載不同的設定。

2. **共享配置**：

   將共用的服務配置(如網路、資料卷等)放在一個文件中，然後將具體的應用服務配置放在另一個文件中，這樣便於管理和維護。
