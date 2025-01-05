# DNS 記錄類型

DNS 使用不同的記錄類型來管理和解析域名，讓域名指向 IP 地址或其他服務。

以下是常見的 DNS 記錄類型，其作用和使用場景：

## 1. **A 記錄 (Address Record)**

- **作用**:

  將域名解析為 [IPv4 地址](./ipv4-and-ipv6.md#1-ipv4)。

- **適用場景**:

  適用於任何需要解析成 IPv4 地址的域名，例如網站的主域名或子域名。

- **範例**:

  若 `example.com` 的伺服器 IP 地址為 `203.0.113.1`，可以設置 A 記錄如下：

  ```plaintext
  example.com.  IN  A  203.0.113.1
  ```

## 2. **AAAA 記錄 (IPv6 Address Record)**

- **作用**:

  將域名解析為 [IPv6 地址](./ipv4-and-ipv6.md#2-ipv6)。

- **適用場景**:

  IPv6 適用於現代網路環境，尤其是需要支持大量設備和更高效能的情況下，例如 IoT 設備和新興技術。

- **範例**:

  若 `example.com` 的伺服器 IPv6 地址為 `2001:0db8:85a3:0000:0000:8a2e:0370:7334`，可設置如下：

  ```plaintext
  example.com.  IN  AAAA  2001:0db8:85a3:0000:0000:8a2e:0370:7334
  ```

## 3. **CNAME 記錄 (Canonical Name Record)**

- **作用**:

  將一個域名作為別名 (Alias) 指向另一個域名。

- **適用場景**:

  - 用於讓多個子域名指向同一個目標 (便於管理)。

  - 適合情況：希望一個子域名隨主域名變更而自動更新 IP 地址。

- **範例**:

  若 `www.example.com` 是 `example.com` 的別名，CNAME 記錄可設置為：

  ```plaintext
  www.example.com.  IN  CNAME  example.com.
  ```

  當訪問 `www.example.com` 時，實際會解析到 `example.com` 的 IP 地址。

**限制**：

- CNAME 記錄無法與其他記錄 (如：A 記錄) 同時存在於同一個域名下。

## 4. **MX 記錄 (Mail Exchange Record)**

- **作用**:

  指定該域名的郵件伺服器，用於處理電子郵件。

- **適用場景**:

  當設置郵件服務時，MX 記錄用於指定郵件伺服器的域名，並設置優先級以確保郵件傳遞的穩定性。

- **範例**:

  若 `example.com` 的郵件伺服器為 `mail.example.com`，且優先級為 10，可設置如下：

  ```plaintext
  example.com.  IN  MX  10 mail.example.com.
  ```

  可以配置多條 MX 記錄，例如：

  ```plaintext
  example.com.  IN  MX  10 mail1.example.com.
  example.com.  IN  MX  20 mail2.example.com.
  ```

  表示優先使用優先級較低 (數字較小) 的伺服器。

## 5. **TXT 記錄 (Text Record)**

- **作用**:

  存放文字資訊，主要用於驗證和安全設置，例如 SPF、DKIM 和 DMARC 等郵件驗證技術。

- **適用場景**:

  - 配置 SPF 防止垃圾郵件。

  - 設置 DKIM 簽名保證郵件的完整性。

  - 配置域名的其他驗證需求 (如 Google Verify、AWS 域名驗證)。

- **範例**:

  設置 SPF 策略來限制哪些伺服器可以代表 `example.com` 發送郵件：

  ```plaintext
  example.com.  IN  TXT  "v=spf1 include:mail.example.com -all"
  ```

  **說明**：

  - `v=spf1` 表示 SPF 的版本。

  - `include:mail.example.com` 表示允許該伺服器發送郵件。

  - `-all` 表示不允許其他伺服器發送。

## 6. **NS 記錄 (Name Server Record)**

- **作用**:

  指定該域名所使用的 DNS 伺服器。

- **適用場景**:

  - 定義誰負責管理該域名的解析。

  - 通常提供主 DNS 和輔助 DNS 伺服器，以增加容錯能力。

- **範例**:

  若 `example.com` 使用 `ns1.example.com` 和 `ns2.example.com` 作為名稱伺服器，記錄可設置為：

  ```plaintext
  example.com.  IN  NS  ns1.example.com.
  example.com.  IN  NS  ns2.example.com.
  ```

  **注意**：

  - 名稱伺服器地址必須可用，否則域名解析會失敗。

## 總結

- **域名別名**:

  - CNAME 記錄用於別名，讓一個域名指向另一個域名。

- **郵件伺服器設置**:

  - MX 記錄負責指向郵件伺服器，並可設置優先級。

- **驗證與文字資訊**:

  - TXT 記錄用於存放 SPF、DKIM 等驗證資訊，增強郵件和域名安全。

- **名稱伺服器管理**:

  - NS 記錄用於指定域名的名稱伺服器。
