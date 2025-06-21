## ✅ Strategy vs Command — 表面相似 vs 本質差異

| 面向                   | **Strategy Pattern**                | **Command Pattern**                       |
| ---------------------- | ----------------------------------- | ----------------------------------------- |
| 🎯 設計意圖（Intent）  | **封裝不同的演算法邏輯**，可互換    | **封裝一個具體的操作或命令**，可排程/撤銷 |
| 🎭 扮演角色            | 可替換的「處理邏輯」                | 可執行的「行為物件」                      |
| 📦 Input               | 通常是相同的資料結構                | 通常每個 command 有自己的參數             |
| 🧠 抽象介面焦點        | 相同的目標（e.g. `.process(data)`） | 相同的行為（e.g. `.execute()`）           |
| 🧰 常見應用            | 演算法切換、策略分派、AI 模式選擇   | 遠端呼叫、排程、undo/redo、事件觸發       |
| 🔁 是否支援撤銷/序列化 | ❌ 通常不支援                       | ✅ 支援 undo、redo、序列化執行            |
| 📜 查表分派常見方式    | 根據「策略 key」選擇策略            | 根據「指令類型」選擇處理器                |

## 🧠 用同一個例子來區分

### 🚀 假設：一個線上購物系統

### ▶ Strategy Pattern 的情境

**「根據不同國家的稅率邏輯來計算總價」**

```ts
interface TaxStrategy {
  calculate(order): number;
}

class USTaxStrategy implements TaxStrategy {
  calculate(order) {
    return order.price * 1.1;
  }
}
class EUTaxStrategy implements TaxStrategy {
  calculate(order) {
    return order.price * 1.2;
  }
}

const strategyMap = {
  US: new USTaxStrategy(),
  EU: new EUTaxStrategy(),
};

strategyMap[region].calculate(order); // 🔄 根據策略選擇處理邏輯
```

➡ **重點是：所有策略都在處理「相同的問題」，只是邏輯不同。**

---

### ▶ Command Pattern 的情境

**「使用者可以發出不同類型的操作指令」**

```ts
interface Command {
  execute(): void;
}

class CreateOrderCommand implements Command {
  execute() {
    /* 建立訂單 */
  }
}

class CancelOrderCommand implements Command {
  execute() {
    /* 取消訂單 */
  }
}

const commandRegistry = {
  create: new CreateOrderCommand(),
  cancel: new CancelOrderCommand(),
};

commandRegistry[commandType].execute(); // 🔄 根據指令選擇要執行的行為
```

➡ **重點是：每個 command 都是完全不同的操作，不只是邏輯差異，而是目的不同。**

---

## 🔍 小技巧：一秒區分它們

| 問題                                           | 如果你回答... | 它可能是 |
| ---------------------------------------------- | ------------- | -------- |
| 「這些物件都是在做**同一件事**的不同方式嗎？」 | ✅ 是         | Strategy |
| 「這些物件都是在做**完全不同的事**嗎？」       | ✅ 是         | Command  |

## 🧠 更深的比喻（武俠風 😄）

- **Strategy** 像是你學會了不同流派的「劍法」：同樣都是「攻擊敵人」，但方法不同（快劍、重劍、氣劍）

- **Command** 像是你有一堆不同「武功招式」：有的攻擊、有的防禦、有的治療、有的瞬移，功能完全不同

## ✅ 總結

| Strategy             | Command                              |
| -------------------- | ------------------------------------ |
| 同一件事，不同做法   | 不同的事，各自獨立                   |
| 抽換的是「邏輯」     | 抽換的是「操作」                     |
| 通常不會序列化、撤銷 | 通常會搭配 queue、undo、macro 等應用 |
