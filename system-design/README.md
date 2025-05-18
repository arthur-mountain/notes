# 軟體系統設計入門 — 常見架構模式總整理

## 常見架構模式（Architectural Patterns）

| 架構模式              | 說明                                        |
| --------------------- | ------------------------------------------- |
| MVC / MVVM            | 前端與 GUI 程式常用的分層顯示架構           |
| Microservices         | 將系統拆成多個獨立小服務，透過 API 通訊     |
| Plugin System         | 核心 + 插件架構，實現系統功能解耦與動態擴充 |
| Layered               | 傳統三層架構：UI / 服務層 / 資料層          |
| Event-driven          | 模組間透過事件發布/訂閱解耦合互動           |
| CQRS / Event Sourcing | 常用於大型資料同步與事件溯源系統            |

---

## 各架構模式介紹與適用場景

### 1. MVC（Model–View–Controller）

- **概念**：將應用拆成三個部分，分別負責資料、畫面與輸入控制，彼此解耦。

- **組成**：

  - Model：資料與狀態管理

  - View：畫面顯示

  - Controller：使用者輸入處理

- **適用場景**：大量表單與畫面交互的 Web 應用

- **實例**：Django、Ruby on Rails、Angular

---

### 2. MVVM（Model–View–ViewModel）

- **概念**：MVC 延伸，強調雙向資料綁定與 ViewModel 處理邏輯。

- **組成**：

  - Model：資料管理

  - View：UI 畫面

  - ViewModel：橋接 Model 與 View，管理狀態與邏輯

- **適用場景**：複雜前端應用與動態畫面更新

- **實例**：Vue.js、React（部分寫法）、Android Jetpack

---

### 3. Plugin System（插件系統）

- **概念**：核心系統定義擴充點，插件動態註冊並在生命週期內執行。

- **特色**：

  - 支援熱插拔，動態擴充功能

  - 清晰的插件生命週期與 hook 管理

- **適用場景**：需要第三方擴充、模組化強的系統

- **實例**：ESLint、Babel、Vite、VSCode、WordPress

---

### 4. Layered Architecture（三層架構）

- **概念**：系統分成 UI、服務與資料存取三層，各自負責不同職責。

- **層級職責**：

  - UI 層：資料顯示與用戶互動

  - 服務層：商業邏輯處理

  - 資料層：資料庫或 API 存取

- **適用場景**：大型企業系統、ERP、CRM

- **實例**：Java EE、Spring Boot、Flask

---

### 5. Event-driven Architecture（事件驅動架構）

- **概念**：各模組間透過事件發布/訂閱通訊，達成解耦合。

- **角色**：

  - 發布者（Publisher）：發送事件

  - 訂閱者（Subscriber）：接收事件並處理

- **適用場景**：異步流程、多模組、分布式系統

- **實例**：Node.js EventEmitter、Kafka、RabbitMQ

---

### 6. Microservices Architecture（微服務架構）

- **概念**：將系統拆成多個小型獨立服務，服務間透過 API 溝通。

- **特色**：

  - 單一責任，獨立資料庫與部署

  - 支援跨語言、跨平台

- **適用場景**：大型、跨部門系統，需彈性擴展

- **實例**：Amazon、Netflix、Uber

---

## 六、架構模式總整理對照表

| 架構模式      | 特色簡述                | 適用場景                    |
| ------------- | ----------------------- | --------------------------- |
| MVC           | 資料、畫面與輸入分離    | 傳統 Web / GUI App          |
| MVVM          | 強化資料與畫面雙向綁定  | 複雜前端與行動應用          |
| Plugin System | 核心 + 插件模組動態擴充 | 編譯工具、編輯器、Lint 工具 |
| Layered       | 明確分層、模組清晰分工  | 企業系統、ERP、API          |
| Event-driven  | 事件通訊、模組解耦      | 異步流程、多模組系統        |
| Microservices | 小服務獨立部署與擴展    | 大型分布式系統、雲端平台    |

---

## 七、如何選擇適合的架構？

| 問題                           | 推薦架構模式               |
| ------------------------------ | -------------------------- |
| 業務邏輯複雜，團隊多人分工     | Layered Architecture       |
| 需處理大量異步事件和流程       | Event-driven Architecture  |
| 業務規則變動頻繁，需可動態擴充 | Plugin System              |
| 系統需高度可擴展、分布式       | Microservices Architecture |

**建議：**
初期建議先使用 Layered 架構，隨著系統成長可視需求加入事件驅動或插件系統，避免一開始架構過度複雜。
