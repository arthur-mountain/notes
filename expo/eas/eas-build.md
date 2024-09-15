# EAS Build

**EAS Build** 是 Expo 提供的雲端build服務，允許開發者在雲端環境中為 iOS 和 Android 平台build高效且可定制的 React Native 應用。

---

## EAS Build 的工作原理

EAS Build 利用雲端基礎設施，接收 source code，根據配置進行依賴安裝、編譯和打包，最終生成可分發的應用包。
例如 APK、AAB for Android, IPA for iOS

其核心流程包括：

1. **提交源碼**：通過 EAS CLI 將源碼上傳至 Expo 的build服務。

2. **環境設置**：根據 `eas.json` 中的配置，建立相應的build環境，包括 install dependencies, environment variables, etc.

3. **憑證管理**：自動處理或使用開發者提供的簽名憑證。

4. **build 過程**：在雲端服務器上執行build任務，生成目標平台的應用包。

5. **分發與下載**：build完成後，提供下載鏈接，並可通過 Expo 控制台或 CLI 進行分發。

---

## 核心概念

### build配置 (Build Profiles)

`eas.json` 文件中定義的build配置，稱為**build 配置檔案**（Build Profiles）。

每個配置檔案可以針對不同的環境（例如開發、測試、生產）設置不同的build選項。

**範例：**

```json
{
  "build": {
    "development": {
      "distribution": "internal",
      "android": {
        "buildType": "apk"
      },
      "ios": {
        "simulator": true
      }
    },
    "production": {
      "distribution": "store",
      "android": {
        "buildType": "app-bundle"
      },
      "ios": {
        "image": "latest"
      }
    }
  }
}
```

### 憑證管理 (Credentials Management)

EAS Build 提供自動和手動管理應用簽名憑證的功能。

- **自動管理**：EAS Build 會自動生成和更新必要的憑證。

- **手動管理**：允許開發者上傳自有的憑證。

### 環境變數 (Environment Variables)

EAS Build 支援在build過程中使用環境變數。

**範例：**

在 `eas.json` 中配置環境變數：

```json
{
  "build": {
    "production": {
      "env": {
        "API_URL": "https://api.production.com",
        "SENTRY_DSN": "your-production-sentry-dsn"
      }
    }
  }
}
```

---

## 基本指令與範例

以下是一些常用的 EAS CLI 指令及其簡單範例。

### 初始化 EAS 配置

初始化 EAS 配置並生成 `eas.json`，該指令會引導完成初始設置，
包括選擇 build 平台、設定 build config、配置憑證等。

```bash
eas build:configure
```

### 開始 build

- **build Android：**

  ```bash
  eas build --platform android
  eas build -p android # -p is shorthand for --platform
  ```

- **build iOS：**

  ```bash
  eas build --platform ios
  eas build -p ios # -p is shorthand for --platform
  ```

- **同時build Android 和 iOS：**

  ```bash
  eas build --platform all
  eas build -p all # -p is shorthand for --platform
  ```

- 透過 `eas.json` 中的配置，使用特定的 bulid config 進行 build：

  ```bash
  eas build --platform ios --profile production # bulid ios with production profile
  ```

### 查看build狀態

查看當前專案的 build 狀態：

```bash
eas build:list
```

查看特定build的詳細日誌：

```bash
eas build:logs --build-id <BUILD_ID>
```

**範例：**

```bash
eas build:logs --build-id 12345
```

---

## 範例：`eas.json` 配置文件

以下是一個包含多個build配置的 `eas.json` 範例，展示如何為不同環境設置不同的選項：

```json
{
  "cli": {
    "version": ">= 3.0.0"
  },
  "build": {
    "development": {
      "distribution": "internal",
      "android": {
        "buildType": "apk",
        "env": {
          "API_URL": "https://api.dev.example.com"
        }
      },
      "ios": {
        "simulator": true,
        "env": {
          "API_URL": "https://api.dev.example.com"
        }
      }
    },
    "staging": {
      "distribution": "internal",
      "android": {
        "buildType": "app-bundle",
        "env": {
          "API_URL": "https://api.staging.example.com"
        }
      },
      "ios": {
        "image": "latest",
        "env": {
          "API_URL": "https://api.staging.example.com"
        }
      }
    },
    "production": {
      "distribution": "store",
      "android": {
        "buildType": "app-bundle",
        "env": {
          "API_URL": "https://api.example.com"
        }
      },
      "ios": {
        "image": "latest",
        "env": {
          "API_URL": "https://api.example.com"
        }
      }
    }
  }
}
```

**說明：**

- **cli.version**：指定 EAS CLI 的最低版本要求。

- **build**：定義多個build配置檔案（development、staging、production）。
  即上述的 build command 所指定的 profile

- **distribution**：指定build的分發方式（internal 表示內部測試，store 表示發布至應用商店）。

- **android.buildType**：指定 Android 的build類型（apk 或 app-bundle）。

- **ios.simulator**：指定是否為模擬器build。

- **env**：定義build過程中使用的環境變數。

---

## 常見應用場景

### 持續整合與持續部署 (CI/CD)

**範例:** 透過 GitHub Actions 觸發 build：

```yaml
name: EAS Build

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: expo/expo-github-action@v6
        with:
          expo-version: latest
      - run: npm install
      - run: eas build --platform all --profile production
        env:
          EXPO_TOKEN: ${{ secrets.EXPO_TOKEN }}
```

## 參考資源

- [Expo 官方文檔 - EAS Build](https://docs.expo.dev/build/introduction)
- [EAS CLI GitHub Repository](https://github.com/expo/eas-cli)
