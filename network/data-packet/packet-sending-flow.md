# 封包發送範例

Before we start, please make sure you have read the

1. [packet encapsulation](./packet-encapsulation.md)

## 摘要

假設從 application 開始發送 https request，到最後對方收到的流程會是:

```plaintext
application request -> SSL/TLS -> tcp socket descriptor(即 kernel 接到數據)
-> 封裝成 TCP segment -> 封裝成 IP packet -> 封裝成 Ethernet frame -> 送到網路上
-> 對方收到 -> 進行解封裝 -> 解密 -> 對 application 送達
```

## 流程圖總結

底下為應用層數據在 OSI 模型中逐層封裝和解封裝的完整流程:

1. **Application Request** (應用層)

   應用程式 (例如 Web 客戶端或 API 客戶端) 發出請求，如一個 HTTP POST 請求。

2. **SSL/TLS 加密 (應用層/表示層)**

   如果數據需要通過 SSL/TLS 保護，應用程式會在傳輸層封裝之前，使用 SSL/TLS 庫對數據進行加密，

   並將加密後的數據傳遞給 kernel socket descriptor。

3. **Socket Descriptor**

   應用層數據通過系統調用 (如 send()) 傳遞到 socket 描述符 (socket descriptor)，這是一個進入內核網路棧的接口。

4. **Kernel 接收加密數據**

   內核接收來自應用層的加密數據，並進行協議封裝，例如封裝成 TCP 段、IP 包，以及以太網幀，最終通過物理網路進行傳輸。

5. **封裝成 TCP Segment (傳輸層)**

   加密後的應用層數據會被封裝成 TCP 段，添加 TCP header。

   TCP header 包括源端口、目標端口、序列號、確認號等信息，並提供可靠的數據傳輸。

6. **封裝成 IP Packet (網路層)**

   TCP 段被進一步封裝成 IP 包，添加 IP header。

   IP header 包含源和目標 IP 位址、TTL、協議類型等，用於路由選擇和網路尋址。

7. **封裝成 Ethernet Frame (數據鏈路層)**

   IP 包被進一步封裝成 Ethernet 幀，

   添加 Ethernet header 和尾部 (包括 MAC 地址和 FCS)，用於本地網路傳輸。

8. **送到網路上 (物理層)**

   完整的 Ethernet frame 被轉換為比特流，

   通過物理層 (如網卡和物理介質) 以電信號或無線信號形式發送到網路上。

9. **對方接收 Ethernet Frame (物理層)**

   接收方的網卡通過物理層接收到比特流，將其還原為 Ethernet frame 並傳遞到數據鏈路層。

10. **解封裝以太網幀 (數據鏈路層)**

    - **驗證 FCS**: 檢查幀校驗序列，確保數據未被損壞。

    - **拆除以太網頭部和尾部**: 獲取幀中的 IP 數據包。

11. **解封裝 IP 數據包 (網路層)**

    - **校驗 IP 頭部**: 驗證 IP 頭部的校驗和。

    - **檢查目標 IP 地址**: 確保數據包是發送給本機的。

    - **拆除 IP 頭部**: 獲取其中的 TCP 段。

12. **解封裝 TCP 段 (傳輸層)**

    - **校驗 TCP 校驗和**: 確保數據完整。

    - **重組數據流**: 根據序列號將數據段重組，處理丟包、重傳等。

    - **拆除 TCP 頭部**: 獲取其中的加密應用層數據。

13. **SSL/TLS 解密 (表示層/會話層)**

    - **解密數據**: 使用協商的密鑰和算法，對加密的應用層數據進行解密。

    - **驗證完整性**: 確保數據未被篡改。

14. **數據傳遞到應用層**

    應用程式接收，解密後的應用層數據 (如 HTTP 請求) 被傳遞給接收方的應用程式 (如 Web 伺服器、API 伺服器)。

15. **應用程式處理請求**

    應用程式根據接收到的請求進行處理，並可能生成響應數據，重複上述過程將響應數據發送回請求方。

## 總結

這樣的逐層封裝和解封裝流程保證了數據的完整性、保密性和傳輸可靠性。

每一層都添加並使用自己的 header，以實現分層協議所需的功能，最終成功將數據從發送方應用層傳送到接收方應用層。
