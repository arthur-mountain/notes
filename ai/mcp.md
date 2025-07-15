# 🧠 Model Context Protocol（MCP）與 MCP Server：結構化 LLM 系統的基礎協議

**Model Context Protocol（MCP）** 便是一個為此而設計的標準語境協議，

而 **MCP Server** 則是協助 LLM 執行工具呼叫的實體執行單元。

## 🧩 MCP 是什麼？

**Model Context Protocol (MCP)** 是一種為 LLM 設計的上下文結構標準，用來描述：

- 模型的角色（`system`）

- 使用者輸入（`user`）

- 任務記憶（`memory`）

- 任務相關的背景資料（`context`）

- 可用工具清單（`tools`）

- 執行限制與輸出格式（`constraints`, `output_format`）

## 🖥 MCP Server 是什麼？

**MCP Server** 是 MCP 架構下的工具執行代理系統(亦稱 tool host)，專責負責以下工作：

- ✅ 註冊與提供可供 LLM 調用的工具（如 APIs、資料查詢函式、腳本等）

- ✅ 接收來自 LLM 的 tool call（如 function calling, resources calling）

- ✅ 執行被調用的工具並回傳結果

- ❌ 不參與任務決策與上下文建構

> ✅ MCP Server 僅是工具提供者與執行者。

## 🔄 完整運作流程

```plaintext
1. Client 構建 MCP 格式的上下文（包含 system, user, context, tools 等）

2. 傳送給 LLM

3. LLM 根據上下文決定是否需要調用某個工具

4. 若需要 → LLM 向 MCP Server 發出 tool call，並提供所需參數

5. MCP Server 接收到 tool call，執行該 tool → 回傳結果

6. LLM 以該結果，根據現有的資線，重新整理回應內容
```

## 🧱 MCP 格式範例

以下是一個簡化的 MCP 輸入格式範例（JSON）：

```json
{
  "system": "You are a helpful financial assistant.",
  "user": "請幫我查詢今天台北的天氣。",
  "context": "User lives in Taipei and wants to plan a trip.",
  "tools": [
    {
      "name": "get_weather",
      "description": "查詢指定城市的今日天氣。",
      "parameters": {
        "type": "object",
        "properties": {
          "city": { "type": "string" }
        },
        "required": ["city"]
      }
    }
  ],
  "output_format": "請以繁體中文回應，並使用 Markdown 格式。"
}
```

## 🧠 常見問題釐清

| 問題                               | 解釋                                                            |
| ---------------------------------- | --------------------------------------------------------------- |
| MCP 是什麼？                       | 一種 LLM 語境封裝協議，定義如何結構化地提供 prompt              |
| MCP Server 是什麼？                | 執行工具調用的伺服器，不參與上下文設計                          |
| `system`, `user` 等是誰提供的？    | 由 Client 或 Orchestrator 準備，非 MCP Server                   |
| 誰決定是否執行工具？               | LLM 根據上下文自主決定是否發出 tool call                        |
| MCP Server 提供哪些內容？          | 只提供 tool schemas + tool 執行接口                             |
| MCP 是否與 LangChain/CrewAI 相容？ | 是，MCP 概念與 function calling 和 agent tool registry 高度相容 |

## ✅ 總結

| 元件                      | 功能                                   |
| ------------------------- | -------------------------------------- |
| **MCP (Protocol)**        | 定義上下文格式的結構與語義標準         |
| **Client / Orchestrator** | 負責建構符合 MCP 格式的完整語境        |
| **LLM**                   | 根據 MCP 格式推理，並決定是否調用工具  |
| **MCP Server**            | 提供工具定義與執行支援，回傳結果給 LLM |
