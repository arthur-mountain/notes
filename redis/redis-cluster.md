## Redis Cluster 設定與操作指南

### 介紹

Redis Cluster 是一個內建於 Redis 的分散式資料庫解決方案，支援大量資料並具備高可用性。

資料可以自動分布在多個節點，並且每個資料槽(slot)會有對應的主節點(master)和從節點(slave)進行資料複製。

---

### 1. 啟動 Redis 節點並啟用 Cluster 模式

首先，啟動多個 Redis 節點並啟用集群模式。

可以通過 Docker 快速部署來完成。確保他們之間可以順利通信，因此指定同樣 network

#### 範例：啟動 3 個 Redis 節點

- 有些參數看不懂沒關係，下方會解釋

- 參數記得要替換掉，或者寫進 env 裡

```bash
docker run -d --rm --name redis-node1 --network redis-cluster-network -p 6001:6379 $YOUR_REDIS_IMAGE \
  redis-server \
    --requirepass "$REDIS_PASSWORD" \ # 一般 reids 連線用的 password(例如: redis-cli 要連線就要使用 password)
    --masterauth "$REDIS_PASSWORD" \ # 主從節點溝通時用的 password
    --port "$REDIS_PORT" \
    --cluster-enabled yes \
    --cluster-announce-ip "$REDIS_HOST" \
    --cluster-announce-bus-ip "$REDIS_HOST" \
    --cluster-node-timeout 5000 \
    --appendonly yes

docker run -d --rm --name redis-node2 --network redis-cluster-network -p 6002:6379 $YOUR_REDIS_IMAGE \
  redis-server \
    --requirepass "$REDIS_PASSWORD" \
    --masterauth "$REDIS_PASSWORD" \
    --port "$REDIS_PORT" \
    --cluster-enabled yes \
    --cluster-announce-ip "$REDIS_HOST" \
    --cluster-announce-bus-ip "$REDIS_HOST" \
    --cluster-node-timeout 5000 \
    --appendonly yes

docker run -d --rm --name redis-node3 --network redis-cluster-network -p 6003:6379 $YOUR_REDIS_IMAGE \
  redis-server \
    --requirepass "$REDIS_PASSWORD" \
    --masterauth "$REDIS_PASSWORD" \
    --port "$REDIS_PORT" \
    --cluster-enabled yes \
    --cluster-announce-ip "$REDIS_HOST" \
    --cluster-announce-bus-ip "$REDIS_HOST" \
    --cluster-node-timeout 5000 \
    --appendonly yes
```

---

### 2. 創建 Redis Cluster

啟動 Redis 節點後，將這些節點加入到 Redis Cluster 中，使用 `redis-cli` 命令來創建集群。

將要建立成 cluster 的節點，以 $host:$ip 的方式條列出來，

#### 範例：創建集群

```bash
docker exec -it redis-node1 redis-cli \
-a "$YOUR_REDISS_PASSWROD" \ # 如果 redis-server 在啟動時有設定 password 的話記得要傳入 redis-cli，一般建議都設定
--cluster create \
redis-node1:6379 redis-node2:6379 redis-node3:6379
```

[!important]

記得如果有設定 password 的話，記得有設定就要帶入 redis-cli， 以下範例就不重複撰寫，

---

### 3. 配置 Master 和 Slave 節點

每個主節點負責一部分資料，並且可以有多個從節點進行資料複製。

使用 `--cluster-replicas` 參數來指定每個主節點的從節點數量。

當然如果一開始節點數量沒有開對，主節點沒辦法被分配到從節點的話就會報錯

#### 範例：創建集群並配置主從節點

```bash
docker exec -it redis-node1 redis-cli \
-a "$YOUR_REDISS_PASSWROD" \
--cluster-replicas 1 \
--cluster create \
redis-node1:6379 redis-node2:6379 redis-node3:6379 \
redis-node4:6379 redis-node5:6379 redis-node6:6379
```

---

### 4. 進行故障轉移(Failover)

當主節點故障時，可以手動進行故障轉移，將一個從節點升級為主節點。

#### 範例：手動故障轉移

```bash
docker exec -it redis-node5 redis-cli cluster failover
```

---

### 5. 將節點加入到 Cluster 中

若集群中的某節點重新啟動，需要將其重新加入集群，使用 `cluster meet` 命令。

#### 範例：將節點加回集群

```bash
docker exec -it redis-node1 redis-cli cluster meet 172.18.0.6 6379
```

若要將節點設為主節點的從節點，使用 `CLUSTER REPLICATE` 指令。

#### 範例：手動設定節點角色

```bash
docker exec -it redis-node1 redis-cli cluster replicate <MASTER_NODE_ID>
```

---

### 6. 查看集群狀態

使用 `cluster nodes` 指令查看集群中所有節點的狀態。

#### 範例：查看集群節點

```bash
docker exec -it redis-node1 redis-cli cluster nodes
```

範例輸出：

```bash
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa 172.20.0.2:6001@16001 myself,master - 0 0 1 connected 0-5460
bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb 172.20.0.2:6006@16006 slave,fail? ccccccccccccccccccccccccccccccccccccccc 1746337952842 1746337951927 2 connected
cccccccccccccccccccccccccccccccccccccccc 172.20.0.3:6002@16002 master,fail? - 1746337954499 1746337951927 2 connected 5461-10922
```

按照空格，從左至右解釋 Redis Cluster Nodes 輸出格式：

```bash
[Node ID] [IP:Port@BusPort] [Flags] [Master ID (if slave)] [Ping Sent] [Pong Recv] [Config Epoch] [Link State] [Slot Range (if master)]
```

#### 各欄位說明

1. **Node ID**：節點的唯一識別碼(UUID 格式)

2. **IP\:Port\@BusPort**：節點的 IP 與 port，以及內部 cluster bus 使用的通訊 port

3. **Flags**：節點的角色與狀態(例如：`master`、`slave`、`myself`、`fail?`)

4. **Master ID**(若是 slave 才會出現)：該 slave 所屬的 master 的 Node ID；若是 master 節點則為 `-`

5. **Ping Sent**：上次對該節點送出 PING 的 Unix 時間戳(ms)

6. **Pong Recv**：上次從該節點收到 PONG 回應的 Unix 時間戳(ms)

7. **Config Epoch**：節點所屬的配置版本號(用於解決分區衝突)

8. **Link State**：與該節點的連線狀態(例如：`connected` 或 `disconnected`)

9. **Slot Range**(若為 master 才有)：該節點所負責的 hash slot 範圍(例如：`0-5460`)

---

### 7. 集群故障處理與自動化

為了保證集群的高可用性，可以使用 Redis Sentinel 或 Kubernetes 來處理節點故障與故障轉移。

#### 範例：使用 `cluster info` 查看集群狀態

```bash
docker exec -it redis-node1 redis-cli cluster info
```

範例輸出：

```bash
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:4
cluster_my_epoch:1
```

---

### 8. 使用 Docker Compose 部署 Redis Cluster

如果你想使用 Docker Compose 部署 Redis Cluster，下面是簡單的配置範例：

```yaml
version: "3"

services:
  redis-node1:
    image: redis:8.0-M02-alpine3.20
    ports:
      - "7001:6379"
    networks:
      - redis-cluster-network
    command: >
      redis-server \
        --requirepass "$REDIS_PASSWORD" \
        --masterauth "$REDIS_PASSWORD" \
        --port "$REDIS_PORT1" \
        --cluster-enabled yes \
        --cluster-announce-ip "$REDIS_HOST1" \
        --cluster-announce-bus-ip "$REDIS_HOST1" \
        --cluster-node-timeout 5000 \
        --appendonly yes

  redis-node2:
    image: redis:8.0-M02-alpine3.20
    ports:
      - "7002:6379"
    networks:
      - redis-cluster-network
    command: >
      redis-server \
        --requirepass "$REDIS_PASSWORD" \
        --masterauth "$REDIS_PASSWORD" \
        --port "$REDIS_PORT2" \
        --cluster-enabled yes \
        --cluster-announce-ip "$REDIS_HOST2" \
        --cluster-announce-bus-ip "$REDIS_HOST2" \
        --cluster-node-timeout 5000 \
        --appendonly yes

  redis-node3:
    image: redis:8.0-M02-alpine3.20
    ports:
      - "7003:6379"
    networks:
      - redis-cluster-network
    command: >
      redis-server \
        --requirepass "$REDIS_PASSWORD" \
        --masterauth "$REDIS_PASSWORD" \
        --port "$REDIS_PORT3" \
        --cluster-enabled yes \
        --cluster-announce-ip "$REDIS_HOST3" \
        --cluster-announce-bus-ip "$REDIS_HOST3" \
        --cluster-node-timeout 5000 \
        --appendonly yes

networks:
  redis-cluster-network:
    driver: bridge
```

---

### 9. 重要 Redis 啟動參數解釋

在啟動 Redis 節點時，會有一些非常重要的配置參數，特別是與集群模式相關的參數。

這些設定不僅能確保 Redis 節點的正常運作，還能增強集群的穩定性與安全性。

下面將詳細解釋每個參數的作用，並提供具體範例。

#### 常用參數

1. **`--requirepass "$REDIS_PASSWORD"`**

   - 設定 Redis 的連接密碼，保證只有正確的客戶端能夠連接到 Redis 節點。

   範例：如果你的 Redis 集群需要密碼認證，這個參數是必要的。

   ```bash
   redis-server --requirepass "your_password"
   ```

2. **`--masterauth "$REDIS_PASSWORD"`**

   - 設定主節點和從節點之間的認證密碼。這個設定是確保主從節點之間安全通訊的必要條件。

   範例：如果 Redis 主從節點之間需要密碼驗證，請使用此參數。

   ```bash
   redis-server --masterauth "your_master_password"
   ```

3. **`--port "$REDIS_PORT"`**

   - 指定 Redis 節點的監聽端口。如果你希望 Redis 節點運行在非預設端口，這個參數可以幫助你設定。

   範例：將 Redis 節點運行在 6380 端口。

   ```bash
   redis-server --port 6380
   ```

4. **`--cluster-enabled yes`**

   - 啟用 Redis 集群模式，讓 Redis 節點能夠參與集群。

   範例：啟用集群模式以便讓 Redis 節點參與集群。

   ```bash
   redis-server --cluster-enabled yes
   ```

5. **`--cluster-announce-ip "$REDIS_HOST"`**

   - 這個參數用來告訴其他集群節點，當它們嘗試連接到這個 Redis 節點時，應該使用哪個 IP 地址。

     這對於容器化部署尤其重要，因為容器的內部 IP 地址與外部的網路地址可能不同。

     在這種情況下，容器內部的 Redis 節點可能無法直接與集群中的其他節點通訊，
     因此這個參數用來顯示一個外部可訪問的 IP 地址。

   **範例：**

   - 假設你有一個 Redis 節點在容器中運行，容器的內部 IP 地址是 `172.17.0.2`，但是你希望集群中的其他節點可以通過 `192.168.1.10` 來訪問這個節點，則可以使用 `--cluster-announce-ip` 指定外部可訪問的 IP 地址。

   ```bash
   redis-server --cluster-announce-ip "192.168.1.10"
   ```

   這樣，當其他節點與此節點通信時，就會使用 `192.168.1.10` 這個 IP 地址，而不是容器內部的 IP。

6. **`--cluster-announce-bus-ip "$REDIS_HOST"`**

   - 類似於 `--cluster-announce-ip`，這個參數用來指定集群節點之間的內部通信所用的 "bus" IP 地址。

     內部通信通常是指節點之間進行複製、故障轉移等操作時所使用的通道。

     當集群節點間需要在不同的網路環境下進行內部通信時，這個參數非常有用。

   **範例：**

   - 假設你的 Redis 節點需要在不同的內部網路中進行通信，比如一個節點使用 `192.168.1.20` 作為內部通信的地址，你可以設置這個參數來告訴其他節點該如何與它進行內部通信。

   ```bash
   redis-server --cluster-announce-bus-ip "192.168.1.20"
   ```

   `--cluster-announce-ip` 用於告訴其他節點這個節點的外部 IP

   `--cluster-announce-bus-ip` 則是用來指定節點之間的內部通信 IP。
   這些設置通常在容器化部署中非常重要，尤其是在不同的網絡和子網之間通信時。

   這樣的設定能保證即使在複雜的網絡環境下，集群的每個節點都能夠正確識別彼此的位置，保持穩定的通信與協同工作。

7. **`--cluster-node-timeout 5000`**

   - 設定節點的超時時間，當 Redis 節點無法在指定時間內與其他節點通信時，會認為該節點失聯。

   範例：設定 5 秒的超時時間來判定節點是否失聯。

   ```bash
   redis-server --cluster-node-timeout 5000
   ```

8. **`--appendonly yes`**

   - 啟用 AOF(Append Only File)持久化，保證資料不丟失。

     對於高可用集群，推薦使用此參數來提高資料持久化的可靠性。

   範例：啟用 AOF 持久化。

   ```bash
   redis-server --appendonly yes
   ```

#### 範例腳本

```bash
#!/usr/bin/env bash

redis-server \
 --requirepass "$REDIS_PASSWORD" \
 --masterauth "$REDIS_PASSWORD" \
 --port "$REDIS_PORT" \
 --cluster-enabled yes \
 --cluster-announce-ip "$REDIS_HOST" \
 --cluster-announce-bus-ip "$REDIS_HOST" \
 --cluster-node-timeout 5000 \
 --appendonly yes
```

---

### 結論

Redis Cluster 提供了高效能且高可用的分散式解決方案，支援大規模資料處理。
