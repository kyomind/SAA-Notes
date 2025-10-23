# AWS SAA 考前衝刺精簡版

## IAM & 認證

### ★★ IAM User 基礎

- 建立後無預設權限 ❌
- 需要 Access Key（身份驗證）+ IAM Policy（授權）✅

### ★★ 跨帳號存取

- **STS AssumeRole**：跨帳號臨時存取
- **企業既有系統** (LDAP/AD) → **Custom Identity Broker**
- **SAML 2.0** → 直接用 IAM Identity Provider

### ★★ KMS 存取控制三層（缺一不可）

- KMS Key Policy（門鎖，**必要條件**）
- IAM Policy（鑰匙）
- Grants（臨時授權）

### ★ Secrets Manager vs Parameter Store

| 項目     | Parameter Store | Secrets Manager            |
| -------- | --------------- | -------------------------- |
| 密碼輪換 | 手動實作 Lambda | **原生自動輪換** ✅         |
| 成本     | 免費/便宜       | 費用較高                   |
| 適用     | 組態值          | RDS/Aurora/DocumentDB 密碼 |

---

## VPC & 網路

### ★★ VPC 核心特性

- **Public Subnet ≠ 自動 Public IP**：需「有資格配置 + 有 IGW 路由」
- **ELB 不能跨 Region**：只能跨 AZ（跨 Region 用 Route 53 + 多個 Regional ELB）
- **Multi-AZ = 高可用**（同一 Region），**非災難恢復**

### ★★ EC2 Auto Scaling 預設跨 region ⭐️

- 可設單一 AZ，但省不了多少（跨 AZ 數據傳輸 ~$0.01/GB）

### ★ VPC Endpoint

- **Gateway Endpoint** (S3/DynamoDB)：改 route table，**免費** ✅
- **Interface Endpoint**：加 ENI，要錢

### ★★ Security Group vs NACL

| 項目 | Security Group | NACL          |
| ---- | -------------- | ------------- |
| 層級 | Instance       | Subnet        |
| 狀態 | **Stateful**   | **Stateless** |
| 規則 | Allow only     | Allow/Deny    |

### ★ Lambda + VPC 的 ENI Scale 失敗原因

1. **ENI 配額達上限**
2. **Subnet IP 不足**（CIDR 太小如 /28 → 只有 16 個 IP）

---

## EC2 & 儲存

### ★★ On-Demand quota 限制 ⭐️

- 以 **vCPU 為單位計算**，非實例數量

### ★★ EBS 核心特性

- **綁定單一 AZ**，自動在 AZ 內複製（防單點故障）
- **只能掛載到同 AZ** 的 EC2

### ★ EBS Multi-Attach 限制

- 僅限 **io1/io2**（Provisioned IOPS SSD）
- 必須同 AZ + Nitro instance

### ★ EBS 加密

- **既有 volume 無法直接加密** ❌
- **唯一作法**：snapshot → 複製（加密）→ 新 volume ✅

### ★★ EBS Snapshot 儲存位置：S3（非 RDS） ✅

### ★ EBS 加密金鑰

- AWS-managed keys：自動管理
- Customer-managed keys (CMK)：可控輪換、權限、audit log

### ★ EBS Volume Type 選擇

| Type    | 用途         | 特性         |
| ------- | ------------ | ------------ |
| gp3/gp2 | 通用型       | CP 值最高    |
| io2/io1 | 資料庫       | 穩定高 IOPS  |
| st1     | Big Data/Log | 大量順序讀寫 |
| sc1     | 冷資料       | 最便宜       |

---

## RDS & 資料庫

### ★★ Multi-AZ 核心機制

- **同步複製**（每次寫入等 standby 確認）
- **DNS CNAME 切換**（非 IP，因為 IP 綁定 subnet，subnet 無法跨 AZ）
- **容錯轉移時間**：60-120 秒

### ★ Read Replicas

- **不會提升 Primary 效能** ❌
- **只分散讀取流量**（Primary IOPS 不變）

### ★★ IAM Database Authentication 三層必要

1. EC2 IAM Role → `rds-db:connect` 權限
2. RDS 啟用 IAM Auth
3. DB 層級建立 IAM 使用者

### ★ SSL/TLS 設定差異

| 引擎             | 設定方式                 | 額外步驟          |
| ---------------- | ------------------------ | ----------------- |
| MySQL/PostgreSQL | require_secure_transport | 重開 DB           |
| SQL Server       | rds.force_ssl = true     | 重開 DB + CA 憑證 |

### ★ RDS Backup

- 手動 snapshot：RDS 刪除時保留 ✅
- 自動備份：RDS 刪除時消失 ❌

### ★★ RDS vs DynamoDB vs Aurora

| 特性   | RDS    | DynamoDB | Aurora |
| ------ | ------ | -------- | ------ |
| 架構   | 關連式 | NoSQL    | 混合型 |
| 擴展   | 垂直   | 水平     | 水平   |
| 一致性 | 強     | 最終     | 強     |

---

## S3 & 儲存類別

### ★★ 儲存類別選擇 ⭐️

| 類別                | 場景            | 檢索      | 記憶                   |
| ------------------- | --------------- | --------- | ---------------------- |
| Standard            | 高頻            | 毫秒      | -                      |
| One Zone-IA         | **可重建+快拿** | 毫秒      | **單 AZ，成本 ↓80%** ✅ |
| Intelligent-Tiering | 模式不可預測    | 毫秒-小時 | 自動但無法客製         |
| Glacier             | 備份/歸檔       | 分鐘-小時 | 低成本                 |

### ★★ S3 加密方案

| 方案                         | 金鑰管理         | AWS 可見性                |
| ---------------------------- | ---------------- | ------------------------- |
| SSE-S3                       | AWS              | 看不到明文                |
| SSE-KMS                      | KMS              | 看不到明文                |
| SSE-C                        | 客戶提供         | 看不到明文                |
| Client-Side (KMS)            | AWS KMS          | AWS 知道 key 但看不到明文 |
| **Client-Side (Master Key)** | **客戶完全管理** | **AWS 完全看不到** ✅      |

### ★★ S3 防誤刪

- **Versioning**：保留歷史版本
- **MFA Delete**：需 MFA 才能永久刪除

### ★ S3 Lifecycle Policy

自動化資料生命週期管理

### ★ S3 跨 KMS Key 複製 ⚠️

S3 Replication Service Role 需要：

- Source Key 的 `kms:Decrypt`
- Target Key 的 `kms:Encrypt`

### ★ S3 Event 通知多方

S3 → **SNS** → 多個 endpoint（Fanout）

---

## Lambda & 無伺服器

### ★★ Lambda 適合場景

- 間歇性、事件驅動、執行時間 **< 15 分鐘**
- 流量不可預測、想省錢

### ★★ Lambda 不適合場景

- 需要持續運行 → EC2/ECS
- 執行時間 > 15 分鐘 → Step Functions + Lambda

### ★★ Lambda 事件源判斷 ⭐️

- **Stream-based** (Kinesis/DynamoDB/SQS)：Lambda **poll**（主動拉取）
- **Event-based** (S3/SNS)：**推送**給 Lambda

### ★ Lambda@Edge ⭐️

- 在 **CloudFront edge location** 執行
- 4 個介入時間點：Viewer Request/Origin Request/Origin Response/Viewer Response

### ★ Lambda + VPC 的 ENI 管理

- Lambda 自動建立 ENI 連接 VPC
- 快速 scale 時需足夠 IP（subnet ≥ /24）

---

## DynamoDB & NoSQL

### ★★ DynamoDB 核心

- **Partition Key = 資料分散依據**
- **RCU/WCU 獨立**，互不影響
- **Item 最大 400KB**

### ★★ Hot Partition 問題 ⭐️

| 問題                  | 原因                   | 解決方案                              |
| --------------------- | ---------------------- | ------------------------------------- |
| 單一 partition 被打爆 | Partition key 設計不當 | 用**高基數欄位**（userId vs country） |
| 總容量看起來夠        | RCU 平均分配 partition | 增加 RCU **無法解決** ❌               |

### ★★ High-Cardinality Attributes

- 有很多不同值：userId、orderId、timestamp
- 反例：status、gender、country（基數低）

### ★★ DynamoDB Streams ⭐️

- Change Data Capture，變更日誌
- 觸發 Lambda 後續處理
- **敏感資料必須 before write 處理**（Streams = after write）

### ★ Global Table 前置條件

啟用 **Streams** ✅

### ★ GSI (Global Secondary Index)

- 獨立容量（RCU/WCU）
- 可單獨設定 Auto Scaling

### ★ DAX 快取

- 只支援 DynamoDB
- 毫秒 → 微秒
- 最終一致性（可能延遲）

---

## 應用層服務

### ★★ API Gateway 三種 Endpoint

- Regional：特定區域
- **Edge-Optimized**：CloudFront 加速（預設），**ACM 證書必須 us-east-1** ⚠️
- Private：VPC 內存取

### ★★ ALB vs NLB ⭐️

| 項目     | ALB                     | NLB             |
| -------- | ----------------------- | --------------- |
| Layer    | **7 (HTTP/HTTPS)**      | **4 (TCP/UDP)** |
| 路由能力 | Host/Path/Method/Header | -               |
| gRPC     | ✅ 支援                  | ❌ 不支援        |

### ★ ALB Layer 7 路由

- Host header (api.example.com)
- Path (/users/*, /orders/*)
- HTTP method / Query string / Source IP / Custom header

### ★ WAF ⭐️

- 防止 SQL Injection、XSS、Rate Limiting、IP Blocking
- 可部署：CloudFront、ALB、API Gateway
- ❌ 不能放 NLB、❌ 不能直接保護 EC2

### ★ CloudFront Origin 支援

- ✅ ALB、S3、自訂 HTTP server
- ❌ NLB（Layer 4）

### ★ S3 + CloudFront 靜態網站

- CloudFront → S3 REST endpoint + **OAI**（Origin Access Identity）

---

## 監控與合規

### CloudWatch & 觀測

#### ★ CloudWatch 預設 EC2 指標

- CPU Utilization、Disk Read、Network In/Out

#### ★ CloudWatch 自訂 metrics

- **需要** CloudWatch Agent + 自訂 script
- Memory、Disk swap、Disk space 無預設

#### ★★ CloudWatch Metric Stream ⭐️

- Near real-time 串流
- 必須經過 **Kinesis Firehose**
- 直接送 3rd party（Datadog、Splunk）

### CloudTrail & GuardDuty

#### ★★ CloudTrail vs GuardDuty ⭐️

| 服務       | 功能          | 用途                 |
| ---------- | ------------- | -------------------- |
| CloudTrail | 記錄 API 呼叫 | 稽核、合規、事後調查 |
| GuardDuty  | 威脅偵測      | **即時威脅監控**     |

#### ★ GuardDuty 掃描來源

- VPC Flow Logs、CloudTrail Logs、DNS Logs
- ❌ 不掃描 CloudWatch Logs

#### ★ Amazon Inspector

- 掃描 EC2 主機漏洞（OS vulnerabilities）

#### ★ Amazon Macie

- ML 自動發現 S3 敏感資料（PII、財務資料）

#### ★ AWS Shield Advanced

- 24/7 DDoS Response Team (DRT)
- 成本保護（帳單報銷）
- 費用：$3,000/月

---

## 資料與串流

### 資料流處理

#### ★★ Kinesis vs Athena ⭐️

| 服務        | 資料            | 用途               |
| ----------- | --------------- | ------------------ |
| **Kinesis** | **即時串流**    | **Real-time 應用** |
| **Athena**  | **S3 靜態資料** | **歷史分析**       |

#### ★ Kinesis Data Streams

- 高吞吐量（GB/秒）、24-365 天 Replay

#### ★ S3 Event → Lambda ⭐️

- 推送模式：S3 主動呼叫 Lambda
- 支援 prefix/suffix 過濾
- Event 物件含 bucket name、key、大小

### 訊息與佇列

#### ★★ SNS vs SQS

| 項目   | SNS      | SQS        |
| ------ | -------- | ---------- |
| 模式   | **Push** | **Pull**   |
| 訂閱者 | 多個     | 單一 Queue |

#### ★★ SNS Fan-out Pattern ⭐️

- 1 則訊息 → 複製給多個 endpoint
- 架構：SNS Topic + 多個 SQS Queue + Lambda

#### ★★ SNS Message Filtering ⭐️

- 訂閱者設定 filter policy
- 只接收符合條件的訊息（**訂閱層過濾**）
- ❌ 不要在 SQS queue 層過濾（太晚）

#### ★★ SQS Polling

| 模式  | ReceiveMessageWaitTimeSeconds | 行為     | 適用           |
| ----- | ----------------------------- | -------- | -------------- |
| Short | 0（預設）                     | 立即回應 | 高吞吐         |
| Long  | > 0（max 20s）                | 等待訊息 | **降低成本** ✅ |

#### ★★ SQS Visibility Timeout ⭐️

- **預設 30 秒**，最長 12 小時
- Consumer 收到 → 鎖定期間其他 consumer 看不到
- 時間到未刪除 → 訊息自動重新出現

#### ★ SQS DLQ 觸發

- **唯一原因**：超過 maxReceiveCount（通常 3-5 次）

#### ★ SQS 訊息重複接收常見原因

- 處理失敗拋例外
- Visibility timeout 太短
- 忘記呼叫 DeleteMessage

#### ★ SQS 訊息保留期

- **預設 4 天**、最長 14 天
- batch job 每週 → 需調整 7 天以上

### EventBridge & 事件

#### ★★ EventBridge 核心 ⭐️

- Event Bus（事件匯流排）
- 當某件事發生 → 觸發其他動作
- 應用：EC2 狀態變 → SNS、S3 上傳 → Lambda

#### ★★ EventBridge 權限配置 ⭐️

- Lambda 作為目標：**Resource-Based Policy**（附加在 Lambda）
- Kinesis 作為目標：**Identity-Based Policy**（附加在 EventBridge IAM Role）

---

## 進階主題

### 高可用與災難恢復

#### ★★ HA vs DR ⭐️

| 特性 | 高可用 (HA)     | 災難恢復 (DR)   |
| ---- | --------------- | --------------- |
| 範圍 | **同一 Region** | **跨 Region**   |
| 切換 | 自動            | 手動/自動       |
| 架構 | Multi-AZ        | Snapshot/Backup |

#### ★★ Multi-AZ Failover 為什麼用 DNS 切換 ⚠️

- **不能用 IP**：Standby 在不同 subnet，IP 無法互換
- **必須用 DNS CNAME**：primary 掛掉 → CNAME 指向 standby
- 容錯轉移時間：60-120 秒

### 跨帳號與多帳戶

#### ★★ AWS Organizations + SCP ⭐️

- **Organizations**：統一管理多個 AWS 帳戶
- **OUs**：邏輯分組（Prod OU、Dev OU）
- **SCP**：套用在 OU 層級的權限邊界
- **效果**：即使 IAM User 有 Admin 權限，SCP 禁止的服務也無法用

### 加密與金鑰管理

#### ★★ KMS Key Policy vs IAM Policy ⭐️

- **Key Policy**：直接控制誰能用 CMK（**必要條件**）
- **IAM Policy**：定義使用者能做什麼
- **完整判斷**：能用 CMK = (Key Policy 允許) **AND** (IAM Policy 允許)

#### ★★ KMS Multi-region Keys ⭐️

- 解決跨域加密（Aurora Global Database）
- 每個區域都有 key replica

#### ★★ S3 Client-Side Encryption + Multi-region ⭐️

- 應用：資料庫跨域 + 連管理員都無法看
- 組合：Client-side Encryption + Multi-region Keys

#### ★ EBS 加密的自動應用

- 新 volume：自動加密 ✅
- Snapshot 複製：新 snapshot 加密 ✅
- Launch instance：掛載 EBS 自動加密 ✅
- 既有 volume：無法直接加密 ❌

#### ★★ ACM 證書到期通知 ⭐️

- 架構：ACM → **EventBridge** → SNS → Email
- 支援每日檢查 + 提前 30 天

#### ★ Edge-Optimized API Gateway ACM ⚠️

- 證書**必須在 us-east-1**（與 API Gateway Region 無關）
- 原因：CloudFront 證書只能來自 us-east-1

---

## 關鍵數字速查表

| 服務             | 項目                        | 數值          | 備註         |
| ---------------- | --------------------------- | ------------- | ------------ |
| Lambda           | **最長執行時間**            | **15 分鐘**   | 無法延長     |
| DynamoDB         | **Item 最大大小**           | **400 KB**    | -            |
| SNS              | **訊息大小上限**            | **256 KB**    | -            |
| SQS              | **訊息大小上限**            | **256 KB**    | -            |
| **SQS**          | **Visibility Timeout 預設** | **30 秒**     | 最長 12 小時 |
| **SQS**          | **Long Polling 最高**       | **20 秒**     | -            |
| **SQS**          | **訊息保留期預設**          | **4 天**      | 最長 14 天   |
| **Auto Scaling** | **Cooldown Period 預設**    | **300 秒**    | 可配置       |
| EBS              | Snapshot 存放               | **S3**        | 非 RDS       |
| RDS              | **Multi-AZ Failover**       | **60-120 秒** | DNS 切換     |
| RDS              | 自動備份保留                | 35 天         | 最長 35 天   |
| KMS              | 自動輪換週期                | 365 天        | CMK 專用     |

---

## 常見考試陷阱 ⚠️

1. **Multi-AZ ≠ 跨 Region**：Multi-AZ 是 HA，不是 DR
2. **ELB 不能跨 Region**：跨 Region 用 Route 53 + 多個 Regional ELB
3. **DynamoDB Hot Partition**：增加 RCU 無法解決，必須改 Partition Key
4. **EBS Snapshot → S3**：不是 RDS
5. **Lambda 15 分鐘上限**：無法延長
6. **Visibility Timeout 預設 30 秒**：不是 20 秒（20 秒是 Long Polling 上限）
7. **One Zone-IA 是 IA 類別**：不是 Archive，毫秒級檢索
8. **CloudFront Origin 不支援 NLB**：只支援 ALB、S3、自訂 HTTP
9. **Edge-Optimized API Gateway ACM 必須 us-east-1** ⚠️
10. **KMS Key Policy 是必要條件**：沒有它，IAM Policy 單獨無效
11. **Lambda Poll vs Push**：Stream-based = poll，Event-based = push
12. **SNS Filter = 訂閱層過濾**：❌ 不要在 SQS 層過濾
13. **Multi-region Keys 才能跨域解密**：Single-region key 無法

---

最後更新：2025-10-23 | 篇幅目標：25-35% 精簡
