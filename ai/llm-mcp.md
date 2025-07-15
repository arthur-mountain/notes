# MCP 架構核心概念總結（針對 LLM + Tool 呼叫設計）

## 🧩 1. MCP 是什麼？

MCP（Model-Centric Programming）是一種系統設計哲學，核心思想是：

> 把 LLM 當作「決策大腦」(Orchestration)，其他工具（API、查資料、邏輯模組）當作「手腳去執行指令」。

👉 **Model Context Protocol（MCP）** 便是一個為此而設計的標準語境協議，
而 **MCP Server** 則是協助 LLM 執行工具呼叫的實體執行單元。

## 🧱 2. MCP 中的三大角色

| 角色     | 功能                               | 實例                          |
| -------- | ---------------------------------- | ----------------------------- |
| Client   | 收使用者輸入，顯示 LLM 回應        | Web UI、Line Bot、手機 App    |
| Host     | 中介與控制邏輯，傳遞訊息與調用工具 | 你寫的後端服務、middleware    |
| Resource | 真正做事的工具或模組               | API、資料庫、查檔案、運算模組 |

🔁 LLM 在這中間不直接操作 Resource，而是透過 Host 請它去執行，然後再根據回傳資料「重新思考」。

## 🔄 3. Thinking = Chaining

- LLM 執行一個複雜任務時，常需要多次呼叫不同工具（Resource）。
- 每一次呼叫都形成一個 step，整個過程就像 LLM 在逐步思考（step-by-step reasoning）。
- 這種「先決策 → 執行 → 得到資料 → 再決策」的連續過程，就是所謂的 **chaining**。
- LLM 每一次拿到資料後，都會「重新整理整體回應」，而不是在舊回答上「補一段資料」。

## ✅ 4. Prompt + Tool Descriptions 的重要性

- 為了讓 LLM 知道有哪些工具、怎麼用，你必須在 prompt 中或透過 API 提供「工具描述」（例如 JSON schema）。
- LLM 根據這些描述，才能在對話中判斷「什麼時候該呼叫哪個 tool」。

## 🧠 總結一句話

LLM 是決策大腦，會透過 chaining 的方式調用工具資源，
並根據每一次工具的回傳結果，重新生成最終回應。
而這些工具的能力，要靠你在 Host 裡先告訴它。

## 📌 範例：使用者問「我明天要帶傘嗎？」

### 🎯 使用者輸入

> 「我明天下午要見客戶，要不要帶傘？」

### 🔁 LLM + Host + Resource 的完整思考流程

#### Step 1

LLM：這需要知道天氣 → 發出 tool call：

```json
{
  "name": "get_weather",
  "arguments": {
    "city": "Taipei",
    "time": "tomorrow afternoon"
  }
}
```

---

#### Step 2

Host 執行 Resource → 回傳結果：

```json
{
  "forecast": "80% chance of rain"
}
```

---

#### Step 3

LLM 重新思考：「既然有 80% 下雨機率 → 應該建議使用者帶傘」
產出完整回答：

> 「根據天氣預報，明天下午台北有 80% 的降雨機率。建議您攜帶雨具以備不時之需。」

✅ 使用者最終只看到這個回答，而不是整個過程。

## 📌 延伸範例：多步 chaining

> 「明天下午我在台北要見客戶，要不要帶傘？交通會不會塞？」

LLM 的內部 chaining 可能變成：

1. 查天氣
2. 根據天氣決定是否查交通
3. 整合為完整建議（例如：建議提前出門、攜帶雨具）

## 🧩 視覺化流程

```
Client ➝ Host ➝ LLM ➝ Tool Call ➝ Host ➝ Resource ➝ 回傳 ➝ LLM 重新思考 ➝ 回答 ➝ Client
                  ↳ Tool Call 2（如有） ↘
```

## 📌 最終總結 — 單個 LLM + 多個 MCP 的核心架構概念

- LLM 作為系統的大腦，負責決策整個對話流程和執行策略。
- 多個 MCP（Host）分別管理各自的資源（Resource），執行具體功能或查詢。
- 在對話中，LLM 會根據當下需求決定呼叫哪個 MCP Host 及其底下的 Resource。
- LLM 與多個 MCP 之間會進行多次 chaining（多步驟推理和工具調用），逐步獲取所需資訊。
- 每次從 MCP 獲得新的結果後，LLM 會重新整理整體回應，而非單純附加資訊。
- 使用者只會看到最後經過完整思考和整合的單次回應，不會看到中間多次呼叫資源的過程。
