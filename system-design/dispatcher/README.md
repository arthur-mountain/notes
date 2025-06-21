# Dispatcher 相關概念整理

## 1. Dispatcher 是什麼？

**Dispatcher** 是一個負責接收請求（Request）並依規則，將請求分派給適當處理器（Handler）或流程的元件。
它的工作是「轉送」或「分配」，**不負責具體業務邏輯**。

## 2. Dispatcher vs Mediator

| 角色     | Dispatcher                       | Mediator                         |
| -------- | -------------------------------- | -------------------------------- |
| 主要職責 | 根據請求條件，把請求分派給處理器 | 協調多個元件間的互動與溝通       |
| 處理邏輯 | 簡單，主要負責分派               | 較複雜，包含協調邏輯             |
| 元件關係 | 呼叫端與處理端間的中介           | 多個元件彼此間透過 Mediator 溝通 |
| 適用場景 | 請求路由、事件分派、任務分配     | UI 元件交互、多人協作系統        |

### 簡單範例

- **Dispatcher 範例**：
  用戶請求 `/login` → Dispatcher 根據 URL 把請求送給 `LoginController` 處理。

- **Mediator 範例**：
  聊天室中 Mediator 負責接收 A 用戶發的訊息，然後通知 B、C 用戶，協調多方訊息流通。

## 3. Dispatcher + Strategy Pattern

- **情境**：根據不同條件動態選擇不同「策略」（邏輯）來處理請求。
- **Dispatcher 角色**：根據環境決定使用哪個策略。
- **Strategy 角色**：封裝各種具體的處理邏輯。

### 範例：計算運費

```ts
interface ShippingStrategy {
  calculate(order): number;
}
class USShipping implements ShippingStrategy {
  /* ... */
}
class EUShipping implements ShippingStrategy {
  /* ... */
}

class Dispatcher {
  constructor(private strategies: Record<string, ShippingStrategy>) {}
  dispatch(order, region: string) {
    return this.strategies[region].calculate(order);
  }
}
```

## 4. Dispatcher + Command Pattern

關於 command 跟 strategy 的差別，請參考 [這篇](../../design-patterns/strategy-vs-command.md)
(因為有點類似，都是查表然後執行相同介面的 method)

- **情境**：系統有多種命令（Command），每個命令有自己的處理器。
- **Dispatcher 角色**：接收命令，查表找到對應 Handler 執行。
- **Command/Handler 角色**：封裝不同的具體行為。

### 範例

```ts
interface Command {
  execute(): void;
}
class CreateUserCommand implements Command {
  execute() {
    /*...*/
  }
}
class DeleteUserCommand implements Command {
  execute() {
    /*...*/
  }
}

const registry = {
  create: new CreateUserCommand(),
  delete: new DeleteUserCommand(),
};

class Dispatcher {
  dispatch(commandType: string) {
    registry[commandType].execute();
  }
}
```

## 5. Dispatcher + Middleware Pipeline

- **情境**：請求需通過一連串「中介軟體」（Middleware）逐步處理。
- **Dispatcher 角色**：控制 Middleware 依序執行，並管理流程。
- **Middleware 角色**：各自完成一項功能（驗證、記錄、授權等）。

### 範例

```ts
type Middleware = (req, res, next) => void;

class Dispatcher {
  private middlewares: Middleware[] = [];

  use(mw: Middleware) {
    this.middlewares.push(mw);
  }

  dispatch(req, res) {
    let i = 0;
    const next = () => {
      if (i < this.middlewares.length) {
        this.middlewares[i++](req, res, next);
      }
    };
    next();
  }
}
```

# 總結

| 搭配模式            | Dispatcher 作用        | 適用場景                           |
| ------------------- | ---------------------- | ---------------------------------- |
| Strategy            | 選擇處理邏輯（策略）   | 多種演算法、地區差異邏輯           |
| Command             | 查表呼叫對應命令處理器 | 多種明確指令、事件驅動系統         |
| Middleware Pipeline | 按順序執行多個中介軟體 | 請求流程處理（驗證、記錄、授權等） |
| vs Mediator         | 單向分派請求           | 多方協調互動                       |
