# 15-CloudFront、S3 Gateway 與 SQS

2025-10-12

---

### 📦 Amazon CloudFront

#### ✅ CloudFront 的 Origin 類型

* **S3 作為 Origin**：公開或私有的 S3 Bucket
* **HTTP Server**：如 EC2、ALB、自架 Web Server
* **其他 AWS 服務**：如 MediaPackage、Lambda@Edge

> 📌 **考試提醒**：判斷題目是否在問你如何安全地對外公開資源，例如：私有 S3 搭配 CloudFront 發佈，或是不讓 EC2 直接對外開放。

#### ✅ CloudFront 搭配私有資源

* 使用 **Origin Access Control (OAC)** 來讓 CloudFront 存取私有 S3
* 搭配 **Signed URL / Signed Cookie** 控制使用者存取權限

#### ✅ CloudFront 的加速與安全性優勢

* 使用 **Anycast IP** 提供最近的節點連線，提升全球用戶存取速度
* 可整合 **WAF、防 DDoS (Shield)** 保障後端資源
* 搭配 EC2 或 ALB 可避開直接曝露在公網，提高安全性

#### ✅ 快取控制：Cache Invalidation

* 讓 CloudFront 重新抓取最新內容
* 可使用 CLI、SDK 或 AWS Console 操作

> 📌 **考試提醒**：當你看到「如何讓用戶拿到最新的檔案版本」時，考慮用 cache invalidation 而不是修改 DNS 或 S3 URL。

---

### ☁️ AWS Storage Gateway - File Gateway 模式

#### ✅ 服務本質與應用場景

* **S3 File Gateway**：讓本地伺服器透過 NFS/SMB 協定存取 S3
* 適合需要混合雲存取的應用場景（例如備份、歷史檔案存取）

#### ✅ NFS vs SMB 協定

| 協定  | 常見平台       | 用途          |
| --- | ---------- | ----------- |
| NFS | Linux/Unix | 適合開源或內部系統整合 |
| SMB | Windows    | 適合企業文件共享環境  |

> 📌 **考試提醒**：如果題目出現「在本地部署系統中讀寫雲端資料」，File Gateway 幾乎是標準答案。

---

### 📨 Amazon SQS：標準與 FIFO 訊息佇列

#### ✅ 基本特性

* 標準佇列：**最多一次送達、可能重複、不保證順序**
* FIFO 佇列：**嚴格順序、Exactly-Once 保證、支援 Message Grouping**

#### ✅ 消費機制

* 訊息處理完成後需由 Consumer **明確刪除訊息**
* 使用 **Visibility Timeout** 避免同一訊息同時被多個 Consumer 處理

#### ✅ Long Polling

* 避免頻繁的空輪詢請求
* Consumer 可等待訊息到來（最長 20 秒）
* 節省 API 呼叫成本，提高處理效率

> 📌 **考試提醒**：考題問到怎麼減少空輪詢造成的成本時，選擇 Long Polling 是關鍵。

---

### 📦 AWS Snowball：大規模資料遷移服務

#### ✅ 使用場景

* 資料量太大（例如幾十 TB 或 PB）無法用網路有效上傳
* 使用實體設備（Snowball）搬運資料至 AWS
* 資料加密、安全配送、內建防篡改設計

> 📌 **考試提醒**：考題若提到「需要將大量資料從內部資料中心遷移到 AWS」時，Snowball 幾乎是唯一解。

---

### 🎯 總結記憶要點

* CloudFront：**快取、安全、全球加速，支援私有 S3 發佈**
* File Gateway：**讓本地透過 NFS/SMB 存取 S3，實現混合雲**
* SQS：**非同步訊息佇列，標準 vs FIFO、Visibility Timeout、Long Polling**
* Snowball：**大量資料搬運的實體方案，非網路傳輸**
