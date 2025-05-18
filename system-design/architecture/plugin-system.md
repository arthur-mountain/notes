# Plugin System

**1. Plugin System 是什麼？**

- 一種架構模式(Architectural Pattern)，用來實現系統的解耦與延遲綁定行為。

- 主程式提供核心接口，plugin 實作接口新增功能，並根據註冊狀態決定是否執行。

**2. Plugin System 與設計模式的結合**

- Plugin System 不是單一設計模式，而是多種設計模式的組合：

  - **Strategy / Interface**：定義 plugin 統一接口。

  - **Command / Strategy**：plugin 實作不同行為。

  - **Observer / EventBus**：plugin 註冊與事件呼叫。

  - **Factory + Reflection**：動態載入與建立 plugin。

  - **Composite / Chain of Responsibility**：plugin串接和分組管理。

  - **Configuration Pattern**：根據設定檔控制 plugin 啟用。

**3. 具體範例(Babel / Rollup / Vite)**

- 核心定義 Plugin 接口，plugin 自行註冊並在執行時按序調用，支持plugin獨立處理邏輯。

**4. 設計思路**

- 明確定義 plugin 的生命週期與 hook(如 onLoad、onTransform)。

- 注入 context 以共享資源，避免 plugin 與核心過度耦合。

**5. Plugin System 的強大解耦特性**

- 核心不需了解 plugin 具體實作，只需統一接口呼叫。

- plugin 不須了解核心運作，獨立實作 hook。

- 支持熱插拔、測試與模擬。

- 透過設定動態決定啟用哪些 plugin。

**6. 核心設計策略對照表**

| 核心特徵           | 使用設計模式                        | 範例                               |
| ------------------ | ----------------------------------- | ---------------------------------- |
| 定義共用行為介面   | Strategy                            | transform() 方法                   |
| 延遲註冊與解耦     | Observer / PubSub                   | plugin 註冊、等待呼叫              |
| 建構 plugin 實例   | Factory                             | 透過設定動態建立 plugin            |
| 組合 / 管理 plugin | Composite / Chain of Responsibility | 多 plugin 串聯處理                 |
| 配置自動掛載       | Configuration Pattern               | vite.config.ts / babel.config.json |

**7. Plugin System 在架構模式中的位置**

- Plugin System 是系統層級的架構模式，與設計模式不同，它解決「整體系統模組如何分工和擴充」的問題。

- 常用於開放擴展、模組化的系統，如 ESLint、Vite、VSCode 等。
