# 10-Route 53、RDS Proxy、Elasticache 與 DNS

2025-10-10

---

### 🧩 RDS Proxy vs PgBouncer

**核心概念：**

* **RDS Proxy** 與 **PgBouncer** 都是為了優化資料庫連線管理而設計。
* 它們能減少資料庫的連線數量、提升效能，並改善應用程式的可擴展性與穩定性。

**主要差異：**

| 項目   | RDS Proxy              | PgBouncer                  |
| ---- | ---------------------- | -------------------------- |
| 管理方式 | AWS 全代管服務              | 自行安裝與維運                    |
| 安全性  | 整合 IAM、Secrets Manager | 需自行設定帳密管理                  |
| 整合性  | 原生支援 RDS、Aurora        | 一般 PostgreSQL/MariaDB 等都可用 |
| 高可用性 | 內建多 AZ                 | 需自行部署與監控                   |

> 💡 **考試重點提示：** 如果題目提到「減少 RDS 連線壓力」且要「整合 AWS 原生安全機制」，選 **RDS Proxy**。

---

### ⚡ Elasticache 核心概念

* **Elasticache** 是 AWS 提供的完全代管快取服務。
* 支援 **Redis** 與 **Memcached**。
* 主要用來：

  * 提升讀取效能。
  * 減輕資料庫負載。
  * 優化延遲與使用者體驗。

**考試小撇步：**

> 如果題目提到「減少資料庫查詢次數」或「需要毫秒級回應時間」，通常答案是 **Elasticache (Redis)**。

---

### 🌐 Route 53 Domain 與 DNS 概念

#### Public vs Private Domain

* **Public Domain**：對外可見（例如：`example.com`）。
* **Private Domain**：僅在特定 **VPC** 內部解析使用。

> ⚠️ 一般的域名註冊商（如 GoDaddy）**只提供 Public Domain**。
> **Private Domain** 是 AWS 特有功能，能與 VPC 整合以進行內部服務解析。

#### TTL（Time To Live）

* 定義 DNS 記錄的快取時間。
* **TTL 短** → 變更生效快。
* **TTL 長** → 減少查詢負載但變更較慢。

> 💡 **考試提醒：** 如果題目問「如何讓 DNS 改變更快生效」，答案通常是「縮短 TTL」。

---

### 🔗 Alias vs CNAME

| 項目       | Alias                   | CNAME             |
| -------- | ----------------------- | ----------------- |
| 功能       | Route 53 特有，可指向 AWS 資源  | 標準 DNS 記錄，指向另一個域名 |
| 是否能用在根域名 | ✅ 可以                    | ❌ 不行              |
| 常見用途     | 指向 ELB、CloudFront、S3 網站 | 指向子域名或其他 DNS 名稱   |
| 額外查詢費用   | 無                       | 可能有               |

> 💡 **記憶口訣：** 根域名要指向 AWS 資源 → 用 **Alias**。

#### 為何 Alias 不能指向 EC2？

* 因為 EC2 的 DNS 名稱是**動態變化**的（IP 會改變）。
* Alias 僅支援**穩定、受 AWS 管理**的服務（如 ELB、CloudFront、S3）。

---

### 📍 Route 53 Routing Policy (路由策略)

**Routing Policy 是決定 DNS 如何回應使用者請求的機制。**

| 類型                        | 說明                        | 使用場景                         |
| ------------------------- | ------------------------- | ---------------------------- |
| **Simple Routing**        | 單一固定目標                    | 最基礎設定                        |
| **Weighted Routing**      | 依比例分配流量                   | A/B 測試、漸進式流量切換               |
| **Latency-based Routing** | 根據延遲最小的區域返回 IP            | 全球應用最佳體驗                     |
| **Failover Routing**      | 主從切換（Primary / Secondary） | 高可用架構                        |
| **Geolocation Routing**   | 根據使用者地理位置導向               | 區域特化內容（例如不同語言站）              |
| **Multi-value Answer**    | 多個資源隨機返回                  | 類似簡單負載分散（非真正 Load Balancing） |

#### TTL 與 Routing Policy 的關聯

* TTL 影響使用者重新查詢新記錄的頻率。
* 若 TTL 太長，Routing Policy 的變更會延遲生效。

> 💡 **建議：** 對於需要快速變更流量分配的策略（如 Weighted Routing），可設定 **較短的 TTL（例如 60 秒）**。

#### Route 53 vs Load Balancer

* **Route 53**：在 DNS 層決定「回應哪個 IP」。
* **Load Balancer**：在應用層實際分配流量。

> ⚡ **快速記憶法：** Route 53 決定「去哪裡」，ELB 決定「怎麼分配」。

---

### 🧠 小結 — Route 53 與 DNS 思維導圖

```
Domain
 ├── Public → 對外可見
 └── Private → 僅限 VPC 內部

DNS 記錄
 ├── A / AAAA → IP 解析
 ├── CNAME → 指向其他域名
 └── Alias → 指向 AWS 資源 (ELB, CloudFront, S3)

Routing Policy
 ├── Simple / Weighted / Latency-based
 ├── Geolocation / Failover / Multi-value
 └── TTL 控制快取時間與更新頻率
```

---

### ✅ 考試常見陷阱

1. **CNAME 不能放在根域名上**。
2. **Alias 記錄是 Route 53 特有功能**。
3. **Private Domain 僅限 VPC 使用**。
4. **RDS Proxy 適用於連線優化與 IAM 整合**。
5. **Elasticache 提升讀取效能、減輕 DB 負擔**。
6. **TTL 影響 DNS 快取與策略變更速度**。

---

> 🧭 **重點心法：** Route 53 不做流量「轉送」，它只負責回答「哪個 IP」；真正分配請求的是 **ELB 或應用層的機制**。
