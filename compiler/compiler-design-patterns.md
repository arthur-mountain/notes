# 編譯器實作中的設計模式應用筆記

六種常見設計模式如何在編譯器實作中應用，並補充其實際情境、使用動機、優缺點與 Java 程式碼實例，幫助深入理解其在工程上的價值與取捨。

---

## 1. Visitor Pattern(訪問者模式)

### 💡 使用情境

需要在不修改 AST 結構的情況下，對 AST 執行多種不同操作，例如型別檢查、解譯執行、產生中介碼、輸出格式化等。

### 🎯 用途

將演算法邏輯與 AST 節點分離，讓新增操作變得容易且乾淨。

### 🧪 程式實例

```java
interface Visitor {
    void visit(BinaryExpr expr);
    void visit(LiteralExpr expr);
}

abstract class Expr {
    abstract void accept(Visitor visitor);
}

class BinaryExpr extends Expr {
    Expr left, right;
    void accept(Visitor visitor) { visitor.visit(this); }
}

class LiteralExpr extends Expr {
    void accept(Visitor visitor) { visitor.visit(this); }
}

class TypeChecker implements Visitor {
    public void visit(BinaryExpr expr) {
        // 對左右節點遞迴檢查型別
    }

    public void visit(LiteralExpr expr) {
        // 回傳整數、布林等基本型別
    }
}
```

### ⚖️ 優缺點

**優點：**

- 新增操作容易(只要實作新的 Visitor)

- 分離邏輯與資料結構，維護性佳

**缺點：**

- 若 AST 結構頻繁更動，需同步調整所有 Visitor，擴充節點不方便

---

## 2. Builder Pattern(建造者模式)

### 💡 使用情境

在 Parser 或 IR Builder 中，建構複雜資料結構(如 AST、IR)需拆分成多步驟、抽象流程，避免過度耦合。

### 🎯 用途

集中管理建構流程，讓每個步驟清楚定義，便於測試與組裝。

### 🧪 程式實例

```java
class ASTBuilder {
    public BinaryExpr buildBinaryExpr(Expr left, Expr right) {
        return new BinaryExpr(left, right);
    }

    public LiteralExpr buildLiteralExpr(int value) {
        return new LiteralExpr(value);
    }
}
```

### ⚖️ 優缺點

**優點：**

- 建構過程清晰，利於擴充與除錯

- 可重複使用、降低重複程式碼

**缺點：**

- 較簡單的資料結構可能顯得冗長

- 多一層抽象對簡單專案造成負擔

---

## 3. State Pattern(狀態模式)

### 💡 使用情境

在 Lexer 解析輸入時，根據目前狀態(預設、數字、字串、註解等)決定轉換邏輯。

### 🎯 用途

讓每種狀態的邏輯封裝在一個類別中，避免使用大量 if-else 或 switch。

### 🧪 程式實例

```java
interface LexerState {
    void handle(LexerContext context, char ch);
}

class DefaultState implements LexerState {
    public void handle(LexerContext context, char ch) {
        if (Character.isDigit(ch)) {
            context.setState(new NumberState());
        }
    }
}

class NumberState implements LexerState {
    public void handle(LexerContext context, char ch) {
        if (!Character.isDigit(ch)) {
            context.emitToken("NUMBER");
            context.setState(new DefaultState());
        }
    }
}

class LexerContext {
    private LexerState state = new DefaultState();

    void setState(LexerState state) { this.state = state; }
    void emitToken(String type) { System.out.println("Emit: " + type); }
    void read(char ch) { state.handle(this, ch); }
}
```

### ⚖️ 優缺點

**優點：**

- 每種狀態邏輯分離，結構清晰

- 可彈性新增狀態

**缺點：**

- 若狀態太多，會導致類別爆炸

- 需維護大量的狀態切換流程

---

## 4. Strategy Pattern(策略模式)

### 💡 使用情境

在 IR 優化、程式碼產生等階段，根據情境選擇不同演算法(如 Constant Folding、DCE、Inlining)。

### 🎯 用途

允許演算法在執行階段切換，並彼此獨立實作。

### 🧪 程式實例

```java
interface OptimizationStrategy {
    void optimize(IR ir);
}

class ConstantFolding implements OptimizationStrategy {
    public void optimize(IR ir) {
        // 3 + 4 → 7
    }
}

class DeadCodeElimination implements OptimizationStrategy {
    public void optimize(IR ir) {
        // 移除沒用到的區段
    }
}

class Optimizer {
    private OptimizationStrategy strategy;
    public Optimizer(OptimizationStrategy strategy) {
        this.strategy = strategy;
    }

    public void run(IR ir) {
        strategy.optimize(ir);
    }
}
```

### ⚖️ 優缺點

**優點：**

- 策略之間解耦，單一職責清楚

- 執行時可根據 flag 切換策略

**缺點：**

- 管理策略組合變得複雜

- 需額外設計策略選擇邏輯

---

## 5. Factory Pattern(工廠模式)

### 💡 使用情境

根據語法解析結果產生不同 AST 節點，或根據輸入產生 Token。

### 🎯 用途

將建立物件邏輯封裝起來，避免多個地方 hardcode new 操作。

### 🧪 程式實例

```java
abstract class ASTNode {}
class BinaryExpr extends ASTNode {}
class LiteralExpr extends ASTNode {}

class ASTNodeFactory {
    public static ASTNode create(String type) {
        return switch (type) {
            case "binary" -> new BinaryExpr();
            case "literal" -> new LiteralExpr();
            default -> throw new IllegalArgumentException("Unknown type: " + type);
        };
    }
}
```

### ⚖️ 優缺點

**優點：**

- 集中建立邏輯，減少重複程式碼

- 易於後續加上 caching、log 等擴充

**缺點：**

- 建立過程複雜時，容易變得臃腫

- 容易變成過度抽象的 anti-pattern(過多小工廠)

---

## 6. Singleton Pattern(單例模式)

### 💡 使用情境

在編譯流程中，全域只應該有一份資源(例如符號表、錯誤管理器)。

### 🎯 用途

確保某個物件只有一個實例，並提供全域訪問點。

### 🧪 程式實例

```java
public class SymbolTable {
    private static SymbolTable instance;

    private SymbolTable() {}

    public static SymbolTable getInstance() {
        if (instance == null) {
            instance = new SymbolTable();
        }
        return instance;
    }

    public void addSymbol(String name, Object symbol) {
        // 加入符號邏輯
    }
}
```

### ⚖️ 優缺點

**優點：**

- 管理共用資源方便(全域唯一)

- 避免重複初始化

**缺點：**

- 測試困難(不易 mock)

- 若過度使用，容易變成「全域變數」反模式

---

## 📘 總結表格

| 設計模式  | 使用情境                  | 優點                   | 缺點                             |
| --------- | ------------------------- | ---------------------- | -------------------------------- |
| Visitor   | 對 AST 執行多種操作       | 擴充操作簡單、結構清晰 | 新增 AST 節點需調整所有訪問者    |
| Builder   | 建構 AST/IR 過程分層      | 易維護、組裝彈性高     | 對簡單資料結構顯得過度工程化     |
| State     | Lexer 狀態切換            | 每個狀態獨立、結構清晰 | 狀態類別過多時管理負擔變大       |
| Strategy  | 優化策略切換、IR 產生邏輯 | 易於替換、擴充策略     | 策略組合複雜、選擇邏輯需額外處理 |
| Factory   | 動態產生 AST / Token      | 集中管理、可擴充       | 容易過度抽象，難以追蹤錯誤       |
| Singleton | 符號表、錯誤管理器        | 單一實例、管理簡單     | 測試難、全域依賴風險高           |
