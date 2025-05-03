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
