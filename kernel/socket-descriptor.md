# Socket descriptor(套接字描述符)

開發 endpoints 或者網頁瀏覽，都會到特定的 port，我們電腦底層是如何 listen 這些連線的？

Ans: 在電腦底層，對於連線的監聽，主要是由作業系統中的「網路堆疊」和「網路套接字(socket) 」機制處理的。

## ㄧ、什麼是網路堆疊、socket descriptor(套接字描述符)？

### 1. 網路堆疊

網路堆疊(Network Stack) 是作業系統中的一部分，負責處理所有的網路通訊需求。

網路堆疊會根據 OSI 模型的層次進行通訊協定的處理，如TCP/IP協定，其中TCP和UDP是兩種最常見的傳輸層協定，處理端口的監聽需求。

### 2. 網路套接字(Socket) 機制

當應用程式希望監聽特定端口上的連線時，它會透過 kernel 建立一個「套接字」(Socket)。

套接字是網路通訊中的一種抽象介面，它允許應用程式與網路堆疊溝通。

套接字的基本工作流程如下:

1. **建立套接字**:
   應用程式會呼叫`socket()`函數建立一個新的套接字。
   套接字包含了`IP協定(IPv4或IPv6)`、`傳輸協定(TCP或UDP)`、`本地端IP地址`和`端口號`等資訊。

2. **綁定端口**:
   一旦套接字建立後，
   應用程式會呼叫`bind()`函數，將該套接字綁定到特定的端口(如: 80 port or 443 port) 和IP地址上。
   這個過程中，作業系統會確認該端口未被其他應用佔用，並將該套接字設置為專門處理該端口上的數據。

3. **開始監聽**:
   如果使用TCP協定，應用程式會呼叫`listen()`函數，將套接字置於監聽模式。
   這樣一來，作業系統會將任何針對該端口的連線請求發送到該套接字，等待應用處理。

4. **接受連線**:
   當有連線請求到達時，應用程式會使用`accept()`函數接受這些連線。
   此時，應用程式會獲得一個新的套接字，用來與特定的客戶端連線，並維持該連線的通訊。
   這些連線的數據經由TCP或UDP封包在網路堆疊中處理，最後由套接字送達應用程式。

5. **處理數據**:
   在連線建立後，應用程式可以透過`recv()`和`send()`函數來接收和傳送數據。
   這些數據會被封裝成網路封包，並透過網路堆疊發送或接收。

### 3. 底層網路驅動

當應用程式透過套接字發送或接收數據時，作業系統的網路堆疊會通過網路驅動程式與實際的網路硬體(如網卡) 通訊。

網路驅動負責處理封包的實際發送和接收，並將封包傳遞至對應的套接字。

### 4. 中斷(Interrupts)

在網卡接收到新的網路封包後，會觸發一個「中斷」(Interrupt)，通知作業系統有新的資料需要處理。

作業系統會檢查封包的目的端口號，並找到對應的套接字，將數據傳遞給相應的應用程式。

## 整體流程總結

當應用程式監聽一個端口時，它會創建一個套接字並通過網路堆疊與網路硬體通訊。

當有新的請求到達該端口，作業系統會根據目的端口號將請求數據交給該套接字，從而使應用程式可以處理該請求。

## 二、作業系統接收到 request 後，是會把 request 丟進我們建立得 socket descriptor file 嗎？

作業系統在接收到請求之後，會將這個請求與應用程式所建立的「socket descriptor」關聯起來。

### 1. 套接字描述符(Socket Descriptor)

在 Unix 和 Linux 系統中，每個套接字(Socket) 都是透過一個「描述符」(Descriptor) 來表示，

這個描述符實際上是一個整數，就像文件描述符(File Descriptor) 一樣。

當應用程式建立套接字後，作業系統會分配一個唯一的描述符來表示這個套接字，稱為「套接字描述符」。

### 2. 將請求與套接字描述符關聯

當有`新的請求`到達時，作業系統會檢查`封包的IP地址`和`端口號`，確認這些資料與`特定的套接字匹配`。

找到相符的套接字之後，作業系統會`將請求傳遞給`該`套接字對應的描述符`。

### 3. 套接字的文件抽象

在 Unix 系統中，套接字的運作方式類似於文件，因此套接字描述符也被視為「文件描述符」的一部分。

這意味著應用程式可以通過讀寫文件的方式來處理網路數據。

例如，當應用程式使用`recv()`或`read()`從套接字描述符讀取數據時，實際上是在從該套接字的緩衝區中讀取接收到的請求數據。

相反，應用程式可以使用`send()`或`write()`將回應數據寫入套接字描述符，以回傳給客戶端。

### 4. 請求的緩衝區處理

當請求到達並被分配到特定的套接字描述符後，作業系統會將請求數據放入該套接字的緩衝區中。

這個緩衝區是一個內存區域，用來暫存即將被應用程式讀取的數據。

應用程式隨後會透過描述符來存取這些數據，並從中讀取請求的內容。

### 5. 非阻塞 I/O 和異步 I/O

有時，應用程式會使用非阻塞或異步 I/O模式來處理大量連線請求。

在這些情況下，作業系統不會讓應用程式因為等待數據而阻塞，
而是會使用事件通知機制(如`select`、`poll`或`epoll`) 來通知應用程式該描述符上有可用的數據。

這樣，應用程式可以更有效地處理多個連線。

### 總結

作業系統確實會將接收到的請求與應用程式的「套接字描述符」關聯起來，並將數據放入該描述符的緩衝區，供應用程式處理。

這樣的機制允許套接字像文件一樣被讀取或寫入，這就是Unix網路通訊的核心設計之一。

## 三、事件通知機制(如select、poll或epoll) 是什麼?

事件通知機制(如`select`、`poll`、`epoll`) 是作業系統提供的一組函數，用於高效處理多個連線或I/O操作，
特別是在需要同時處理多個網路連線或文件描述符時非常有用。

這些機制可以幫助應用程式避免阻塞在單一的I/O操作上，而是有效地監控多個套接字或描述符，以便在它們有活動時進行處理。

以下是每種機制的介紹和比較:

### 1. `select`

`select` 是最早期的I/O多路複用(multiplexing) 機制。它的基本原理是:

- `select`允許應用程式提供三個描述符集合，分別用來監聽「讀」、「寫」和「異常」事件。

- 應用程式設定好想要監聽的文件描述符後，`select`會進入等待狀態，直到有描述符上的事件發生為止(例如某個套接字上有數據可讀) 。

- `select`會返回一個已準備好進行I/O操作的描述符集合，應用程式可以依次處理這些描述符上的事件。

**優點**: 兼容性好，幾乎所有作業系統都支持`select`。

**缺點**:

- `select`有描述符數量上限(一般是1024個) ，處理大量連線時會遇到瓶頸。

- 每次調用`select`時都需要重置描述符集合，隨著描述符數量增加，效率會下降。

### 2. `poll`

`poll`是一種改進版的`select`，它移除了描述符上限，並以結構化數據的形式提供描述符列表。基本原理如下:

- `poll`允許應用程式提供一組文件描述符和對應的事件掩碼(用於指定要監聽的事件類型，如可讀、可寫等) 。

- `poll`會阻塞直到至少一個描述符上的事件發生，然後返回一組有活動的描述符。

**優點**: 無描述符數量上限，描述符數量可以是任意的，因此比`select`更靈活。

**缺點**:

- 仍然需要遍歷整個描述符列表來找出有活動的描述符，處理大量連線時效能不佳。

- 每次呼叫都要重複傳入描述符列表，這在描述符數量很多的情況下會增加系統開銷。

### 3. `epoll`

`epoll`是Linux特有的機制，專為高效處理大量文件描述符而設計。

相較於`select`和`poll`，`epoll`更具備良好的擴展性，適合於高併發連線。其基本特點如下:

- `epoll`分為「邊沿觸發」(Edge-triggered, ET) 和「水平觸發」(Level-triggered, LT) 兩種模式，ET模式在高效處理上更有優勢。

- 應用程式首先創建一個`epoll`實例，並註冊所有需要監控的描述符。
  與`select`和`poll`不同的是，這些描述符在創建之後不需要在每次呼叫`epoll_wait`時重複傳入。

- 當有事件發生時，`epoll_wait`僅返回那些有活動的描述符，因此無需遍歷整個描述符列表。

**優點**:

- **高效**: `epoll`只返回活動的描述符，減少了不必要的遍歷和系統開銷。

- **事件持久性**: 註冊的描述符持續有效，不需要每次重複傳入。

- 適合大量的連線需求，特別是高併發的網路伺服器應用。

**缺點**: 僅支援Linux，跨平台兼容性較差。

### 總結比較

| 機制     | 支援平台     | 文件描述符限制 | 適合的應用情境             | 性能 |
| -------- | ------------ | -------------- | -------------------------- | ---- |
| `select` | 幾乎所有系統 | 有限制(1024)   | 少量連線、跨平台兼容需求   | 較差 |
| `poll`   | 幾乎所有系統 | 無限制         | 中等數量連線、無跨平台需求 | 中等 |
| `epoll`  | 僅限於Linux  | 無限制         | 大量連線、需要高效事件處理 | 最佳 |

### 實際應用情境

在高併發的應用程式(例如網頁伺服器或即時通訊伺服器) 中，`epoll`能夠更高效地處理大量連線。

對於少量連線，`select`和`poll`也能夠勝任，但如果連線量過多，這些機制的效能會開始下降。

在需要支援不同作業系統時，則通常會選擇`select`或`poll`來確保兼容性。
