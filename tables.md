# Table Summary Notes

## AWS 服務的 Caching 機制

| 服務        | Caching 機制                                                 |
| ----------- | ------------------------------------------------------------ |
| API Gateway | 可啟用 stage-level caching，暫存 API 回應內容（TTL 可調整）⭐️ |
| DynamoDB    | 有 DAX (DynamoDB Accelerator)，提供 in-memory cache          |
| CloudFront  | 本質就是 CDN，核心功能就是 edge caching                      |
| RDS         | 可搭配 ElastiCache 作為資料庫查詢快取                        |

## RDS Read Replica 按位置分類

最常見的是**同 Region 不同 AZ**。
這是 RDS Read Replica 的**標準配置**，原因：

**成本效益**：同 Region 內 Read Replica 的資料傳輸是免費的
**低延遲**：同 Region 內 AZ 間延遲極低（通常 < 1ms）
**基本容錯**：至少有 AZ 級別的容錯能力

跨 Region Read Replica 雖然提供更強的災難恢復能力，但有額外的**跨 Region**資料傳輸費用**，通常只有全球化應用或有嚴格 DR 要求時才會用。

| 位置              | 用途                        | 說明                                     |
| ----------------- | --------------------------- | ---------------------------------------- |
| 同 Region 不同 AZ | ✅ 分散讀取 + 手動故障恢復   | 標準做法，AZ 掛了可**手動**提升為 Master |
| 同 AZ             | ⚠️ 分散讀取（不建議）        | 無 AZ 容錯，實務上不使用                 |
| 跨 Region         | ✅ 分散讀取 + 跨區域災難恢復 | 全球化應用、Region 級災難恢復            |

**重點**：Read Replica 不提供自動故障轉移，真正的自動容錯用 **Multi-AZ**

## Read Replica vs Multi-AZ

注意：Multi-AZ 解決 HA，Read Replica 解決讀取效能，它們是**互補關係**，不是替代關係。

| 功能     | Read Replica           | Multi-AZ                      |
| -------- | ---------------------- | ----------------------------- |
| 目的     | 讀取分流、擴展效能     | 高可用性、自動容錯            |
| 是否可讀 | ✅ 可以讀取             | ❌ Standby 不可讀（同步備援）⭐️ |
| 故障切換 | ❌ 需手動提升為主庫     | ✅ 自動切換，無需人工介入  ⭐️   |
| 延遲  ⭐️  | 非同步複製（秒級延遲） | 同步複製（零資料遺失）        |

## RDS 備份保留期限

| 備份類型 | 保留期限    | 用途                             |
| -------- | ----------- | -------------------------------- |
| 自動備份 | 最多 35 天⭐️ | 短期恢復、Point-in-Time Recovery |
| 手動快照 | 無限期保留  | 長期保存、合規需求、重大版本備份 |

自動備份設計為最多 35 天，是因為它的用途是「短期操作失誤恢復」（如誤刪資料），這類問題通常幾天內就會發現。若允許無限保留，儲存成本會失控，用戶也可能忘記清理。AWS 的設計哲學是：**自動備份顧短期、手動快照顧長期**，需要長期保留就該有意識地建立手動快照並自行管理。

## 儲存服務對比 (EBS、EFS、S3)

| 服務 | 連接方式    | 適用場景                       | 考試重點                                   |
| ---- | ----------- | ------------------------------ | ------------------------------------------ |
| EBS  | 單 EC2 獨佔 | 1 個 instance 的本地高效存儲   | Block storage、低延遲                      |
| EFS  | 多 EC2 共享 | 1000+ instances 共用同份數據   | POSIX filesystem⭐️、concurrently accessible |
| S3   | 應用層連接  | 物件存儲、web accessible、歸檔 | Eventually consistent、web-based           |

## EBS Multi-Attach 與標準 EBS 比較

| 特性       | 標準 EBS | EBS Multi-Attach           |
| ---------- | -------- | -------------------------- |
| 附加實例數 | 1 個     | 最多 16 個                 |
| 可用區域   | 單一 AZ  | 單一 AZ                    |
| 適用場景   | 普通應用 | 高可用叢集 (如 Oracle RAC) |
| 考試出現率 | 常見     | 罕見                       |

EBS Multi-Attach 只支援 **io1 和 io2 Block Express**（Provisioned IOPS SSD 系列），最多可附加 16 個實例。gp2、gp3、st1、sc1 都不支援。原因是只有高效能 IO 場景（如 clustered database）才需要多實例共享磁碟。

## EFS Performance Mode 選擇比較

| 特性         | General Purpose | Max I/O                   |
| ------------ | --------------- | ------------------------- |
| **適用場景** | 一般網站、CMS   | **大量併發存取**          |
| **延遲**     | 較低（ms）⭐️     | 較高（可接受）            |
| **併發連線** | 適中            | **無限制**                |
| **判斷關鍵** | 少量 tasks      | **數百個 tasks 同時存取** |

Max I/O 模式延遲較高是分散式系統的固有權衡：當併發量暴增，系統需要更多協調開銷（locking、synchronization），請求排隊等待時間也會拉長。Max I/O 的設計是**優先保證 throughput 不崩，代價是延遲變高**；General Purpose 則**優先保證低延遲，但併發量有上限**。

### 記憶口訣

- 題目出現「hundreds of tasks」→ Max I/O
- 題目強調「low latency」→ General Purpose

## 三種 ELB 健康檢查對比

| ELB 類型 | 支援的健康檢查   | 使用層級         |
| :------- | :--------------- | :--------------- |
| ALB      | HTTP、HTTPS      | Layer 7 (應用層) |
| NLB      | TCP、HTTP、HTTPS | Layer 4 (傳輸層) |
| CLB      | TCP、HTTP、HTTPS | Layer 4 + 7      |

記法：**健康檢查**跟**流量轉發**是兩回事，NLB 可以「L4 轉發 + L7 檢查」。

## WAF vs Shield 功能對比

| 關鍵字                    | 正確答案                |
| :------------------------ | :---------------------- |
| 限制請求頻率 / Rate limit | **WAF Rate-Based Rule** |
| 封鎖特定 IP/地區          | WAF Regular Rule        |
| Layer 4 DDoS 防護⭐️        | **AWS Shield**          |
| 整合 ALB                  | 用 WAF（可直接關聯）    |
| 整合 CloudFront           | 用 WAF（可直接關聯）    |

WAF 是 **Web** Application Firewall，專門檢查 HTTP/HTTPS 內容（URL、headers、body），這些都是 Layer 7 的東西。Layer 4（TCP/UDP）封包還沒解析成 HTTP，WAF 根本看不懂，所以 Layer 4 DDoS（如 SYN flood）要靠 **Shield** 在網路層處理。

## ALB vs NLB

| 特性        | ALB          | NLB             |
| ----------- | ------------ | --------------- |
| OSI Layer   | Layer 7      | Layer 4         |
| 適用協定    | HTTP/HTTPS   | TCP/UDP/TLS     |
| 路由能力    | **內容路由** | 無 (只看 port)⭐️ |
| 效能        | 高           | **極高**        |
| 靜態 IP     | ❌            | ✅               |
| WebSocket   | ✅            | ✅               |
| Lambda 支援 | ✅            | ❌               |
| 價格        | 中           | 低              |

**判斷標準**:

- 題目提到「**根據 URL path 路由**」→ ALB
- 題目提到「**需要靜態 IP**」或「**極致效能**」→ NLB
- 題目提到「**WebSocket**」→ ALB 或 NLB 都可以，但通常選 ALB

WebSocket 握手用 HTTP，但建立後就是普通的 **TCP 長連線**。NLB 處理 Layer 4，不在乎連線內容是什麼，只負責轉發 TCP 封包，所以天然支援 WebSocket——它根本不需要「理解」WebSocket，照轉就對了。

## WAF 支援的服務

| 服務            | 用途       | 典型場景                   |
| --------------- | ---------- | -------------------------- |
| **CloudFront**  | CDN        | 保護靜態網站、全球分發內容 |
| **ALB**         | 負載均衡器 | 保護後端 API/應用程式      |
| **API Gateway** | API 管理   | 保護 RESTful/WebSocket API |

WAF **只能掛 ALB** 是因為它需要檢查 HTTP/HTTPS 內容（Layer 7），而 NLB 工作在 Layer 4，根本看不到 HTTP 內容。

## WAF 規則類型與用途

| 規則類型                 | 範例                   | 實務用途     |
| :----------------------- | :--------------------- | :----------- |
| **IP 白/黑名單**         | 阻擋來自中國的 IP      | 地理封鎖 ⭐️   |
| **Query String**         | `?admin=true`          | 防止參數注入 |
| **HTTP Header**          | `User-Agent: BadBot`   | 阻擋爬蟲     |
| **Request Body**         | 包含 `<script>`        | 防 XSS 攻擊  |
| **Rate Limiting** ⭐️      | 單 IP 每分鐘 > 1000 次 | 防 DDoS      |
| **SQL Injection 規則** ⭐️ | 自動偵測 SQL 注入模式  | 防資料庫攻擊 |

## 安全服務防護層級對比

| 服務               | 防護層級           | 用途                   | 能否阻擋 HTTP 內容？       |
| :----------------- | :----------------- | :--------------------- | :------------------------- |
| **Security Group** | Layer 4 ⭐️          | 控制 EC2 的進出流量    | ❌ 只能看 IP + Port         |
| **NACL**           | Layer 4 (傳輸層) ⭐️ | 控制 Subnet 的進出流量 | ❌ 只能看 IP + Port         |
| **AWS WAF**        | Layer 7 (應用層)   | 檢查 HTTP/HTTPS 內容   | ✅ 可以看 URL、Header、Body |
| **AWS Shield**     | Layer 3/4 ⭐️        | 自動防 DDoS            | ❌ 不看內容，只擋大流量     |

幾個言外之意：

1. **Security Group 和 NACL 功能重疊但層級不同**：SG 在 instance 層、NACL 在 subnet 層，兩者都只看 IP + Port，所以防護能力相同，差別在於作用範圍和規則邏輯（SG 是 stateful、NACL 是 stateless）

2. **Layer 4 服務無法防禦應用層攻擊**：SQL Injection、XSS、惡意 request body 這些都需要 WAF，SG/NACL/Shield 完全擋不住

3. **Shield 和 WAF 是互補關係**：Shield 擋大流量（Layer 3/4 DDoS），WAF 擋惡意內容（Layer 7 攻擊），不是二選一

4. **考試陷阱題型**：題目說「防止惡意 SQL 查詢」，答案一定是 WAF，不管選項有沒有 Security Group 看起來很誘人

## WAF vs Security Group 選擇指南

| 需求                     | 能用 WAF? | 適合用 WAF? | 最佳方案                  | 原因                |
| ------------------------ | --------- | ----------- | ------------------------- | ------------------- |
| 只允許公司固定 IP        | ✅         | ❌           | Security Group            | 免費、簡單、直接    |
| 阻擋惡意 IP (動態黑名單) | ✅         | ✅           | WAF IP Set                | 可程式化更新大量 IP |
| 阻擋特定國家             | ✅         | ✅           | WAF Geo Match             | SG 做不到           |
| 限制單一 IP 請求頻率     | ✅         | ✅           | WAF Rate Limiting         | SG 做不到           |
| 阻擋已知攻擊來源         | ✅         | ✅           | WAF + Threat Intelligence | 整合威脅情報        |

## AWS Config 三大功能對比

| 功能              | 用途                           | 對應題目需求      |
| ----------------- | ------------------------------ | ----------------- |
| **Config Rules**  | 偵測合規（檢查 SG 是否開 SSH） | ✅ 偵測問題        |
| **Remediations**  | 自動修復為合規組態             | ✅ 自動重新組態 SG |
| **Notifications** | 發送通知                       | ❌ 只通知，不修復  |

**AWS Config 兩種都能做到**，取決於你怎麼設定：

- **僅通知**：Config Rules 偵測違規 → 觸發 SNS Notification
- **自動修復**：Config Rules 偵測違規 → 觸發 **Remediation Action**（透過 SSM Automation）

關鍵差異是你設定的「下游動作」是什麼，Config Rules **本身只負責偵測**。

所以題目看到：

- 「偵測 / 檢查 / 監控」→ Config Rules
- 「自動修復 / 自動重新設定」→ Config Rules + Remediations

## IAM 身份驗證方案常見混淆

| 方案                              | 使用時機                            |
| --------------------------------- | ----------------------------------- |
| **SAML Federation**               | 公司已有 AD/IdP，要 SSO             |
| **IAM Users**                     | 直接在 AWS 建立帳號（小型團隊）     |
| **AWS SSO (IAM Identity Center)** | 管理多個 AWS 帳號的 SSO（現代作法） |
| **Cognito**                       | 給「終端使用者」用的（app、網站）   |

## Internet Gateway vs NAT Gateway

|              | Internet Gateway          | NAT Gateway               |
| ------------ | ------------------------- | ------------------------- |
| **用途**     | Public subnet 雙向連外網  | Private subnet 單向連外網 |
| **流量方向** | 雙向（進+出）             | 只能出，不能進            |
| **使用場景** | Web server、Load Balancer | Database、後端服務        |
| **安全性**   | 直接暴露在網路上          | 隱藏 private IP           |
| **成本**     | 免費                      | 按小時+流量收費 ⭐️         |

**記憶點**：

- IGW = 大門（可進可出）
- NAT = 單向閥門（只能出去下載更新、呼叫 API ⭐️）

---

## Security Group vs NACL

|              | Security Group          | NACL                       |
| ------------ | ----------------------- | -------------------------- |
| **層級**     | Instance（EC2、RDS 等） | Subnet ⭐️                   |
| **Stateful** | 是（回程自動放行）      | 否（必須明確設定進出規則） |
| **規則**     | 只有 Allow              | Allow + Deny               |
| **預設**     | 全關閉                  | 全開放                     |
| **評估順序** | 全部規則都看 ⭐️          | 依編號順序，碰到就停 ⭐️     |

**記憶點**：

- SG = 保鑣（認得你，回來自動放行，stateful）
- NACL = 閘門（進出都要驗證，stateless）

**考試陷阱**：

- 看到「需要 deny 特定 IP」→ NACL（SG 沒有 deny）
- 看到「stateless」→ NACL
- 大部分情況用 SG 就夠了，NACL 是額外防護層

### Security Group 不會有衝突，因為它只有 Allow 規則

評估邏輯是：**所有規則取聯集⭐️**。只要任一條規則允許，流量就放行。

例如：

- 規則 A：允許 port 22 from 10.0.0.0/24
- 規則 B：允許 port 22 from 192.168.0.0/16

結果：兩個來源都能進 port 22，沒有衝突問題。

因為沒有 Deny，所以不存在「一條說允許、另一條說拒絕」的情況。**沒被任何規則允許 = 拒絕**（implicit deny）。

## RDS Multi-AZ 核心重點解析

| 特性              | 說明                                                  | 考試常見陷阱                         |
| ----------------- | ----------------------------------------------------- | ------------------------------------ |
| **同步複製**      | Primary → Standby 是 **synchronous** replication      | 不是 async，這保證**資料零遺失**     |
| **自動容錯轉移**⭐️ | Primary 掛了，RDS 自動切換到 Standby（通常 1-2 分鐘） | Standby 平常**不能讀不能寫**，純備援 |
| **跨 AZ 部署**    | Primary 和 Standby 在不同 AZ                          | 單一 AZ 掛掉不影響服務               |

## 實務理解

把 Multi-AZ 想成你部署後端服務時的 **Active-Passive⭐️ HA 架構**：

- Primary DB = Active node（處理所有請求）
- Standby DB = Passive node（隨時準備接手，但平常不工作）
- 當 Primary 掛了，DNS endpoint 自動指向 Standby

### 考試必考場景

**Q: 如何同時達成高可用性 + 讀取效能提升？**
**A: Multi-AZ (HA) + Read Replicas (效能)**

Multi-AZ 解決的是「服務不中斷」，想分散讀取流量要用 Read Replicas。

## Multi-AZ vs Read Replicas 複製機制

| 項目               | Multi-AZ          | Read Replicas    |
| ------------------ | ----------------- | ---------------- |
| **複製方式**       | Synchronous       | Asynchronous     |
| **用途**           | High Availability | Read scalability |
| **Failover**       | 自動容錯轉移      | 需手動 promote   |
| **Standby 可讀嗎** | ❌ 不能讀寫        | ✅ 可讀取         |

## SNS 訂閱端點類型(推送的目標)

非常多種

| 訂閱者類型           | 使用場景                       |
| -------------------- | ------------------------------ |
| **SQS Queue**        | 解耦非同步處理，確保訊息不遺失 |
| **Lambda**           | 觸發無伺服器函式，自動執行邏輯 |
| **HTTP/HTTPS**       | 推送到自訂 API endpoint        |
| **Email/Email-JSON** | 通知管理員或發送報表           |
| **SMS**              | 手機簡訊告警                   |
| **Mobile Push**      | App 推播通知                   |

## CloudFront Signed URLs vs Signed Cookies

| 特性     | **Signed URLs** | **Signed Cookies** |
| -------- | --------------- | ------------------ |
| 使用場景 | 單一檔案存取    | 多個檔案存取       |
| 典型案例 | 下載付費電子書  | 訂閱制影片平台     |
| 實作方式 | URL 帶參數⭐️     | Cookie 存權限      |
| 用戶體驗 | 每次要新 URL    | 一次驗證，多次使用 |

## CloudFront 內容存取控制機制

| 機制                            | 用途                                       | 考試重點                                |
| ------------------------------- | ------------------------------------------ | --------------------------------------- |
| **Signed URLs/Cookies**         | 控制內容存取權限                           | **付費內容、時效性限制首選**            |
| **OAC (Origin Access Control)** | 強制用戶只能透過 CloudFront 存取 **S3** ⭐️  | **防止繞過 CloudFront 直接打 S3 URL**   |
| **S3 Pre-signed URLs**          | 讓用戶不需要 AWS 憑證就能上傳/下載 S3 物件 | **注意：這是 S3 功能，不是 CloudFront** |

S3 Pre-Signed URL → 讓用戶「**上傳/下載**」但不必給 AWS 憑證

## VPC Endpoint 完整對比

| 特性          | Gateway Endpoint     | Interface Endpoint |
| ------------- | -------------------- | ------------------ |
| **支援服務**  | **僅 S3 + DynamoDB** | 幾乎所有 AWS 服務  |
| **費用**      | **免費 ⭐️**           | 按小時 + 流量收費  |
| **實作方式**  | **修改 Route Table** | 建立 ENI (私有 IP) |
| **配置層級**⭐️ | Route Table 層級     | Subnet 層級        |
| **安全組**⭐️   | **不需要**           | 需要               |
| **跨 VPC**    | **僅限同 VPC** ⭐️     | 可透過 PrivateLink |
| **考試判斷**  | **S3/DynamoDB 首選** | 其他服務才考慮     |

關鍵是**實作方式的本質差異**：

| Endpoint 類型 | 實作方式                              | 為何需要/不需要 SG                           |
| ------------- | ------------------------------------- | -------------------------------------------- |
| **Gateway**   | 修改 Route Table，流量走 AWS 內部網路 | 沒有 ENI，沒有網路介面，**SG 無從附加**      |
| **Interface** | 在 Subnet 建立 ENI（有私有 IP）       | ENI 就是網路介面，**必須有 SG 控制進出流量** |

### 有沒有需要 SG 記憶方式

- **Gateway = 路由層** → Route Table 管，不需要 SG
- **Interface = 網路介面層** → 有 ENI 就有 SG，跟 EC2 一樣的邏輯

## 考試記憶要點

看到 S3 或 DynamoDB 私有連線 → 直接選 Gateway Endpoint

理由：

1. 免費（成本最佳化）
2. 配置簡單（改 **route table** 就好）
3. AWS 官方推薦做法

其他服務需要私有連線 → Interface Endpoint

## VPC Endpoint 的考試秘訣

| 記憶方式           | 內容                                                                  |
| ------------------ | --------------------------------------------------------------------- |
| **Gateway 簡記**   | **「S3 & DynamoDB = 免費」** ← 考試看到 S3 或 DynamoDB 優先選 Gateway |
| **Interface 簡記** | **「其他服務 = 要錢但更靈活」** ← 需要其他服務時自動選 Interface      |
| **成本優化秘訣**   | 看到「cost-effective」+ 「S3/DynamoDB」→ **直接選 Gateway**           |

## SNS vs SQS

| 特性         | **SNS**                                           | **SQS**                               |
| ------------ | ------------------------------------------------- | ------------------------------------- |
| **模式**     | **Pub/Sub（1 對多）⭐️**                            | Queue（1 對 1）                       |
| **訊息儲存** | **不儲存**                                        | 儲存最多 14 天⭐️無法再更多，這是硬上限 |
| **用途**     | **廣播通知**                                      | 工作佇列                              |
| **即時性**   | **即時推送給所有訂閱者**（離線訂閱者會遺失訊息⭐️） | 非同步，訊息保留到被消費              |

**核心考點**：SNS 最重要的特性是 **即時性 + 不儲存訊息**，訂閱者離線會遺失訊息

## Shield Standard vs Shield Advanced

| 功能                 | **Shield Standard**     | **Shield Advanced**                 |
| -------------------- | ----------------------- | ----------------------------------- |
| 費用                 | 免費                    | **$3000/月 起**                     |
| 防護層級             | Layer 3/4（網路層）     | **Layer 3/4/7**（含應用層）         |
| 防護對象             | 所有 AWS 資源(預設就有) | **EC2、ELB、CloudFront、Route 53**⭐️ |
| **DDoS「成本」保護** | ❌                       | ✅ **防止流量攻擊產生的額外費用**    |
| **WAF 整合**         | ❌                       | ✅ **免費使用**                      |
| **24/7 DRT 支援**    | ❌                       | ✅                                   |
| **即時攻擊可視化**   | ❌                       | ✅                                   |

### 標準版可防護

Layer 3/4 常見 DDoS 攻擊類型：

| 攻擊類型                     | 層級  | 說明                         |
| ---------------------------- | ----- | ---------------------------- |
| **SYN Flood**                | L4    | 大量半開 TCP 連線耗盡資源    |
| **UDP Flood**                | L4    | 大量 UDP 封包塞爆頻寬        |
| **ICMP Flood (Ping Flood)**  | L3    | 大量 ping 請求               |
| **Reflection/Amplification** | L3/L4 | 利用 DNS、NTP 等放大攻擊流量 |

### Layer 7 攻擊：需 Advanced

- HTTP Flood、Slowloris 等應用層攻擊
- 這類攻擊需要 WAF 配合才能有效防禦

## 愈低層，處理的「抽象程度」愈低

| 層級 | 關心什麼                           | 不關心什麼             |
| ---- | ---------------------------------- | ---------------------- |
| L3   | 封包從 A 到 B（IP 位址）           | 內容是什麼、順序對不對 |
| L4   | 連線建立、傳輸可靠性（port、序號） | 資料的語意             |
| L7   | 應用邏輯（HTTP method、header）    | 底層怎麼傳的           |

所以 ICMP 只需要知道「你在不在」，不需要建立連線、不需要 port，自然比 TCP 簡單。

## IPv4 vs IPv6 出站連線與 Gateway 方案

| 特性         | IPv4             | IPv6                |
| ------------ | ---------------- | ------------------- |
| **對外連線** | **NAT Gateway**  | **Egress-only IGW** |
| **雙向連線** | Internet Gateway | Internet Gateway    |
| **私有 IP**  | ✅ 有             | ❌（都是公開 IP）    |

你的理解**正確**。

關鍵差異在於：

| Gateway 類型        | Route Table 設定                          | 原因                                                    |
| ------------------- | ----------------------------------------- | ------------------------------------------------------- |
| **NAT Gateway**     | 需指向 `nat-gw-xxx`                       | NAT GW 是獨立資源，放在 public subnet，流量要明確導過去 |
| **Egress-only IGW** | 指向 `eigw-xxx`（**它本身就是一種 IGW**） | 它是 IGW 的變體，直接附加到 VPC                         |

**本質差異**：

- NAT GW = 中介轉發器，需要兩段路由（private → NAT → IGW → 外網）
- Egress-only IGW = 單向 IGW，一段路由直達（private → EIGW → 外網）

所以你說「不需要指向 IGW」是對的 — 因為 **EIGW 本身就扮演了 IGW 的角色**，只是限制成只出不進。

## Blue-Green vs Canary 的關鍵差異

| 比較項目     | Blue-Green                           | Canary                        |
| ------------ | ------------------------------------ | ----------------------------- |
| **環境數量** | 需要兩套完整環境                     | 單一環境⭐️，部分流量導向新版本 |
| **成本**     | 高（雙倍資源）                       | 低（共用資源）                |
| **切換方式** | 一次性全切                           | 漸進式調整流量比例            |
| **風險控制** | 可快速回滾，但出問題影響 100% 使用者 | 出問題只影響小部分使用者      |

## KMS 金鑰存放方式比較

| 項目         | Default Key Store  | Custom Key Store          |
| ------------ | ------------------ | ------------------------- |
| 金鑰材料位置 | **AWS 託管的 HSM** | **你的 CloudHSM**         |
| 實體控制權   | **AWS**            | **你**                    |
| 複雜度       | **低**             | **高**（需管理 CloudHSM） |
| 成本         | **低**             | **高**（CloudHSM 費用）   |
| 適用場景     | 一般用途           | 合規要求需自控金鑰        |

## KMS 加密相關概念整理

這三個維度**互相獨立**，可自由組合：

| 維度             | 選項                                 | 決定因素                      |
| ---------------- | ------------------------------------ | ----------------------------- |
| **誰執行加密**   | Client-side / Server-side            | 你的應用加密 vs AWS 服務加密  |
| **金鑰存放位置** | Default Key Store / Custom Key Store | AWS 託管 HSM vs 你的 CloudHSM |
| **加密演算法**   | 對稱（AES）/ 非對稱（RSA）           | 使用場景需求                  |

### 考試關鍵字判斷

| 關鍵字                                         | 選擇                  |
| ---------------------------------------------- | --------------------- |
| encrypt before upload、application encrypts    | **Client-side**       |
| SSE、encryption at rest、automatic encryption  | **Server-side**       |
| regulatory、compliance、full control over keys | **Custom Key Store**  |
| 沒特別提金鑰控制                               | **Default Key Store** |

## CloudHSM = 硬體加密模組

**HSM (Hardware Security Module)**：專門用來保管加密金鑰的實體硬體設備

## 金鑰管理方案比較

| 特性         | KMS（Default Key Store） | KMS（Custom Key Store） | CloudHSM 直接使用 |
| ------------ | ------------------------ | ----------------------- | ----------------- |
| **金鑰存放** | AWS 託管 HSM             | 你的 CloudHSM           | 你的 CloudHSM     |
| **控制權**   | AWS 管理                 | 你完全控制              | 你完全控制        |
| **整合方式** | AWS 服務原生支援         | 透過 KMS API            | 需自行整合        |
| **適用場景** | 一般用途                 | 合規 + 想用 KMS API     | 合規 + 自訂整合   |
| **價格**     | 低                       | 高（CloudHSM 費用）     | 高                |

### 考試需求判斷

- 題目提到 **FIPS 140-2 Level 3**、**單租戶**、**完全控制金鑰** → CloudHSM
- 一般加密需求 → KMS

**白話說**：CloudHSM 是給銀行、政府這種「連 AWS 都不能信任」的客戶用的。99% 的情況用 KMS 就夠了。

## KMS Key 類型速查表

| 類型                        | 誰能管理 Key Policy | 能否設定 Rotation | 能否查看 CloudTrail | 金鑰材料存放位置  | 獨立審計 FIPS 140-2 Level 3 |
| --------------------------- | ------------------- | ----------------- | ------------------- | ----------------- | --------------------------- |
| **AWS Owned**               | AWS（你看不到）     | AWS 自動處理      | ✗ 看不到            | AWS HSM           | ✗                           |
| **AWS Managed**             | AWS（你只能看）     | ✓ 自動每年⭐️       | ✓                   | AWS HSM           | ✗                           |
| **Customer Managed (標準)** | **你**              | ✓ 可選自動/手動   | ✓                   | AWS HSM           | ✗                           |
| **Custom Key Store**        | **你**              | ✓ 可選自動/手動   | ✓                   | **你的 CloudHSM** | **✓**                       |

### 關鍵判斷點

- AWS Owned vs AWS Managed：前者你完全看不到、後者你**能看但不能改**
- AWS Managed vs Customer Managed：前者 AWS 幫你管、後者**你自己管（考試愛考這個）**
- Customer Managed vs Custom Key Store：後者是你要「完全掌控硬體 + **獨立審計需求**」時才用

### 考試出題邏輯

- 題目提到 "full control" → Customer Managed
- 題目提到 "compliance" + "dedicated HSM" → Custom Key Store
- 題目只說要 "encryption" 沒特別要求 → AWS Managed 最省事

## S3 加密選項對比

| 加密位置    | 金鑰來源 | AWS 術語 | 實務常見度 |
| ----------- | -------- | -------- | ---------- |
| Server-Side | S3 管理  | SSE-S3   | ⭐⭐⭐        |
| Server-Side | AWS KMS  | SSE-KMS  | ⭐⭐⭐        |
| Server-Side | 客戶提供 | SSE-C    | ⭐⭐         |
| Client-Side | AWS KMS  | CSE-KMS  | ⭐⭐         |
| Client-Side | 客戶管理 | CSE-C    | ⭐          |

## VPN 連接方案對比

| 選項               | 為何不對                                            |
| ------------------ | --------------------------------------------------- |
| **VPC Peering**    | 只能連 VPC 對 VPC，**不能連 on-premises**⭐️          |
| **Direct Connect** | 專線連接，**不走公網**，且貴（**不適合當 backup**） |
| **PrivateLink**    | 用於**服務存取**（如連 S3、API），**不是連機房**    |

## S3 Object Lock 模式對比

| 項目     | **Governance Mode**    | **Compliance Mode**             |
| -------- | ---------------------- | ------------------------------- |
| 保護程度 | 軟性保護               | **硬性⭐️保護**                   |
| 誰能繞過 | 有特殊權限的人可以解除 | **誰都不能動⭐️，包括 root user** |
| 適用場景 | 一般資料保護           | **法規遵循、audit 要求**        |

## S3 Object Lock：Retention Period vs Legal Hold

| 特性     | **Retention Period** | **Legal Hold**    |
| -------- | -------------------- | ----------------- |
| 運作方式 | **設定時間範圍**     | **無限期⭐️鎖定**   |
| 解除條件 | **時間到就解除保護** | **必須手動⭐️解除** |

## AWS Resource Access Manager vs Organizations 比較

| 項目     | **RAM**                          | **Organizations**                            |
| -------- | -------------------------------- | -------------------------------------------- |
| 核心功能 | **共享資源**                     | **管理帳號**                                 |
| 解決問題 | **避免重複建資源**               | **統一管理多帳號**                           |
| 主要用途 | Transit Gateway、Subnet、License | 帳號群組化、套用政策                         |
| 考試陷阱 | 看到「共享資源」選 RAM           | 看到「管理帳號」「套用政策」選 Organizations |

## DR 策略對比

| 策略                   | RTO                  | 成本     | 平時狀態                                       |
| ---------------------- | -------------------- | -------- | ---------------------------------------------- |
| **Backup and Restore** | **數小時～天**       | **最低** | 只有備份存在 S3，沒有任何運行資源              |
| **Pilot Light**        | **數十分鐘～數小時** | **低**   | **核心組件維持最小運行** (如 DB、key services) |
| **Warm Standby**       | **數分鐘**           | **中高** | **完整系統以縮小規模運行**                     |
| **Multi-Site**         | **秒級**             | **最高** | **完整系統雙活運行**                           |

## RDS 讀取擴展策略

| 選項                    | 說明                       | 何時選                 |
| ----------------------- | -------------------------- | ---------------------- |
| **Aurora Auto Scaling** | 自動調整 read replica 數量 | 讀取流量波動、要省錢   |
| **Read Replica (手動)** | 固定數量的副本             | 流量穩定，不需動態調整 |

## RDS 其他效能與可用性選項

| 選項                 | 維度     | 說明              | 何時選                   |
| -------------------- | -------- | ----------------- | ------------------------ |
| **Multi-AZ**         | 可用性   | 同步備援到另一 AZ | 需要高可用、自動故障轉移 |
| **Provisioned IOPS** | 儲存效能 | 固定的高 I/O 性能 | 需穩定低延遲，如 OLTP    |

## EBS vs FSx for Windows

| 特性     | **EBS**                       | **FSx for Windows**    |
| -------- | ----------------------------- | ---------------------- |
| 儲存類型 | 區塊儲存 (Block Storage)      | 檔案系統 (File System) |
| 掛載方式 | 單一 EC2（Multi-Attach 例外） | 多台機器同時存取       |
| 協定支援 | 無 SMB                        | 原生支援 SMB           |
| 使用場景 | EC2 的硬碟                    | 共享檔案伺服器         |

### EBS Multi-Attach 限制

- 僅 io1/io2 磁碟類型
- 僅限同一 AZ 內的 EC2
- 應用層需自行處理並發寫入
- 仍是區塊儲存，不支援 SMB

### 考試選擇

看到 **SMB、Windows 共享、多台同時存取** → 選 FSx for Windows，不是 EBS Multi-Attach

常見一起比較的儲存服務：

| 服務                | 類型         | 特點                        | 適用場景               |
| ------------------- | ------------ | --------------------------- | ---------------------- |
| **EBS**             | 區塊儲存     | 單一 EC2、低延遲            | EC2 系統碟/資料碟      |
| **EFS**             | **檔案**系統 | 多 EC2 共享、**NFS**、Linux | Linux 共享儲存         |
| **FSx for Windows** | **檔案**系統 | 多機共享、**SMB**、AD 整合  | Windows 共享儲存       |
| **FSx for Lustre**  | **檔案**系統 | 高效能平行運算              | HPC、ML 訓練           |
| **S3**              | 物件儲存     | 無限擴展、HTTP 存取         | 靜態檔案、備份、資料湖 |

## 考試快速判斷

| 關鍵字                  | 選擇                |
| ----------------------- | ------------------- |
| Linux + 共享            | **EFS**             |
| Windows + SMB + AD      | **FSx for Windows** |
| HPC、Lustre、高吞吐運算 | **FSx for Lustre**  |
| 單一 EC2 硬碟           | **EBS**             |
| 靜態內容、無限儲存      | **S3**              |

## Amazon Data Lifecycle Manager vs AWS Backup

| 比較項目 | **Amazon DLM**            | **AWS Backup**                     |
| -------- | ------------------------- | ---------------------------------- |
| 定位     | **EBS snapshot 專用工具** | **跨服務集中式備份平台**           |
| 適用範圍 | **只管 EBS**              | **EBS、RDS、DynamoDB、EFS、S3...** |
| 複雜度   | 簡單直接                  | 功能完整但設定較複雜               |
| 成本     | 免費（只付儲存）          | 免費（只付儲存）                   |
| 考試答案 | **單純 EBS 問題選這個**   | **跨服務備份選這個**               |

Amazon Data Lifecycle Manager 是用來 自動化 EBS 快照及 EBS-backed AMI (Image) 的建立、保留及刪除的服務。
它主要用於資料備份策略（Backup Retention Policy），尤其常見於：

- 自動快照備份（EBS Volume）
- 建立 AMI 映像檔以支援跨區域或災難備援
- 清除過期快照，降低成本
- 配合 定期備份、保留 n 份 的策略

## 安全防護需求與解決方案對應表

| 需求                       | 選擇方案             |
| -------------------------- | -------------------- |
| **DDoS 防護 (Layer 3/4)**  | **AWS Shield + NLB** |
| **Web 攻擊防護 (Layer 7)** | **WAF + ALB**        |
| **SQL Injection 防護**     | **WAF + ALB**        |
| **Rate Limiting (IP)**     | **WAF + ALB**        |

**判斷標準**：題目提到「SQL Injection」、「XSS」、「bot protection」、「web application firewall」→ 必定是 **ALB + WAF** (因為 WAF **只能**掛 ALB)

## 常見 AWS Systems Manager（SSM）使用情境對應表

| 功能                           | DevOps 日常用途                        |
| ------------------------------ | -------------------------------------- |
| **Session Manager**            | 取代 SSH：無需跳板機、無需開防火牆     |
| **Parameter Store**            | 儲存 Secret，搭配 Lambda / ECS / CI/CD |
| **Run Command**                | 批次更新所有 EC2，例如 `apt upgrade`   |
| **Automation + Patch Manager** | 每週自動 Patch 所有 Linux/Windows 主機 |

## AWS 成本管理工具對比

| 工具                       | 用途                            | 記憶點         |
| -------------------------- | ------------------------------- | -------------- |
| **Cost Explorer**          | **分析歷史 + 預測未來(有限度)** | 看圖表、找趨勢 |
| **AWS Budgets**            | **設定預算 + 超標告警**         | 主動通知       |
| **Cost Anomaly Detection** | **AI 自動偵測異常花費**         | 機器學習找異常 |
| **Cost and Usage Report**  | **詳細原始數據匯出**            | 給 BI 工具用   |
| **Billing Dashboard**      | **當月帳單總覽**                | 只看總數       |

**Budgets 也可以「預測」**，但性質不同：

| 工具              | 預測方式                           | 用途       |
| ----------------- | ---------------------------------- | ---------- |
| **Cost Explorer** | 基於歷史趨勢，預測未來 12 個月花費 | 規劃、分析 |
| **Budgets**       | 預測「本月底會不會超過你設的預算」 | 告警觸發   |

**關鍵差異：**

- Cost Explorer：「下個月大概花多少？」→ **主動分析**
- Budgets：「照這個速度，月底會爆預算嗎？」→ **被動警示**

Budgets 的「Forecasted」告警就是這個概念——當預測值將超過預算時提前通知你，而不是等到真的超過才告警。

所以你的表格沒錯，只是「預測」的含義不同。考試通常問的是 Cost Explorer 的**趨勢預測**功能。

## AWS Glue vs Amazon EMR

| 特性     | AWS Glue                 | Amazon EMR                       |
| -------- | ------------------------ | -------------------------------- |
| **用途** | **Serverless ETL**       | **完整 big data framework**      |
| **框架** | **內建 Spark**           | **Hadoop/Spark/Hive/Pig 等多種** |
| **適用** | **簡單到中等複雜度 ETL** | **複雜資料處理、自訂演算法**     |
| **管理** | **全託管**               | **需管理 cluster**               |

## Route 53 路由策略核心差異

| 路由策略         | 核心邏輯                  | 決策依據                         | 考試陷阱                          |
| ---------------- | ------------------------- | -------------------------------- | --------------------------------- |
| **Latency**      | 自動選最快的 AWS Region   | AWS 測量的網路延遲               | 同地區用戶可能連到不同 Region     |
| **Geoproximity** | 地理位置 + 可調整流量比例 | 用戶與資源的地理距離 + bias 參數 | 可以用 bias「強迫」流量偏向某資源 |
| **Geolocation**  | 依用戶地理位置強制分配    | DNS 查詢來源的國家/地區          | 精準控制「這國用戶一定連造裡」    |
| **Weighted**     | 按比例分配流量            | 你設定的權重值                   | 與地理位置無關，純粹流量分配      |

## S3 Server-Side Encryption (SSE) 三種模式

| 加密方式    | 金鑰管理者     | 使用時機                                         | 考試關鍵字 |
| ----------- | -------------- | ------------------------------------------------ | ---------- |
| **SSE-S3**  | AWS 完全管理   | **最簡單，預設選項**                             | AES-256    |
| **SSE-KMS** | AWS KMS 管理   | **需要金鑰審計日誌、CloudTrail⭐️、金鑰輪替⭐️**     | CloudTrail |
| **SSE-C**   | 你自己管理金鑰 | **完全控制金鑰，但你要保管、每次請求都要帶金鑰** | 客戶自管   |

## VPC Peering vs Transit Gateway vs VPN 比較

| 特性           | **VPC Peering**           | **Transit Gateway** | **VPN**                |
| -------------- | ------------------------- | ------------------- | ---------------------- |
| **轉送能力**   | ❌ 不支援 (non-transitive) | ✅ 支援多 VPC 互通   | ✅ 可與 On-Premise 連線 |
| **CIDR 限制**  | 不能重疊                  | 不能重疊            | 可透過 NAT 解決        |
| **跨區域支援** | ✅ 支援，會收費            | ✅ 支援，集中管理    | ✅ 支援，但效能較低     |
| **適合場景**   | 少量 VPC 互聯             | 大規模多 VPC 架構   | 與 On-Premise 或其他雲 |

**CIDR 限制筆記：**

VPC Peering 和 Transit Gateway 都要求連接的網路 CIDR 不能重疊，因為路由無法判斷封包該送往哪個目的地。VPN 可透過 NAT 轉換 IP 範圍來繞過此限制。考試看到「CIDR 重疊」關鍵字，直接排除 Peering 和 TGW，考慮 PrivateLink 或 VPN + NAT。

考試記法：

- **VPC Peering** → **一對一、無轉送、CIDR 不重疊**。
- **Transit Gateway** → **Hub-and-Spoke、大規模、多 VPC 可轉送**。
- **VPN** → **跨雲或 On-Premise，經公網加密，效能較差**。

## AWS 安全與威脅偵測服務

| 服務        | 用途                                          |
| ----------- | --------------------------------------------- |
| Rekognition | **圖像/影片內容分析**（臉部、物件、不當內容） |
| Macie       | **S3 敏感資料偵測⭐️**（PII、機密資料）         |
| GuardDuty   | **威脅偵測**（安全異常行為）                  |

## VPC 網路連通性問題的排查順序

| 類型     | Security Group                              | NACL                    |
| -------- | ------------------------------------------- | ----------------------- |
| 作用層級 | Instance                                    | Subnet                  |
| 狀態     | Stateful（回傳自動允許）                    | Stateless（需雙向規則） |
| 預設行為 | Deny all inbound(指沒有任何 allow 項目設定) | Allow all               |
| 優先檢查 | ✓ 先查這個                                  | 再查這個                |

題目提「兩個 subnet 之間通訊」→ 兩者都要檢查。

## AWS CI/CD 三劍客

| 服務             | 一句話                 | 關鍵字                          |
| ---------------- | ---------------------- | ------------------------------- |
| **CodeBuild**    | 編譯、測試、打包       | `buildspec.yml`、build、compile |
| **CodeDeploy**   | 部署到 EC2/Lambda/ECS  | `appspec.yml`、Blue/Green       |
| **CodePipeline** | 串接以上，編排整個流程 | orchestrate、workflow           |

記憶口訣：Build 建、Deploy 部、Pipeline 串

## SQS vs Amazon MQ (RabbitMQ)

| 特性 | **SQS**                | **Amazon MQ (RabbitMQ、ActiveMQ 相容)**           |
| ---- | ---------------------- | ------------------------------------------------- |
| 管理 | **全託管，零維護**     | **需選機器規格**                                  |
| 費用 | **按請求計費（便宜）** | **按執行時間計費**                                |
| 協定 | **AWS SDK/HTTP**       | **AMQP 0-9-1**                                    |
| 適用 | **雲原生應用**         | **遷移既有系統**⭐️只有「需要與既有 MQ 相容」才有用 |

**考試判斷標準**：

- 題目提到「existing application」或「migrate from on-premises RabbitMQ」→ **Amazon MQ**
- 其他情況 → **SQS**

## AWS WAF vs Network Firewall

| 項目       | AWS WAF                       | Network Firewall                             |
| ---------- | ----------------------------- | -------------------------------------------- |
| **層級**   | Layer 7                       | Layer 3-7                                    |
| **位置**   | ALB/CloudFront/API Gateway 前 | VPC 邊界                                     |
| **關鍵字** | SQL injection、XSS、Bot       | VPC perimeter、FQDN filtering、all protocols |

### 速判口訣

WAF 擋 Web 攻擊，Network Firewall 顧整個 VPC

- **Web 攻擊** → WAF
- **VPC 邊界 + FQDN** → Network Firewall

唯一補充：如果題目出現「**outbound traffic filtering**」或「**egress filtering**」，也是指向 Network Firewall，因為它能在 VPC 出口過濾流量，WAF 做不到這件事。

## IAM User vs STS 比較

| 項目     | IAM User       | STS              |
| -------- | -------------- | ---------------- |
| 憑證效期 | 永久           | 15 分鐘～12 小時 |
| 安全性   | 低（長期暴露） | 高（用完即過期） |
| 用途     | 固定身份       | 臨時授權         |

## AWS 全球加速服務對比

| 服務                         | 主要用途           | 流量方向             | 快取機制    | 適用場景                                     | 成本特性          |
| ---------------------------- | ------------------ | -------------------- | ----------- | -------------------------------------------- | ----------------- |
| **S3 Transfer Acceleration** | 加速**上傳**或下載 | 客戶端 → S3          | ❌ 無        | • 跨國上傳大檔案 / 備份到 S3 / 用戶分散全球  | 每次傳輸收費      |
| **CloudFront**               | 加速下載           | S3 → 客戶端          | ✅ Edge 快取 | • 全球分發靜態內容 / 軟體更新下載 / 串流媒體 | 快取後省流量費    |
| **Global Accelerator**       | 加速**應用**       | 客戶端 ↔ ALB/NLB/EC2 | ❌ 無        | • 遊戲伺服器 / WebSocket / 即時應用程式      | 固定費用 + 流量費 |

### 關鍵差異：協定層級

- Transfer Acceleration & CloudFront：HTTP/HTTPS（應用層）
- Global Accelerator：TCP/UDP（傳輸層）

## S3 加密方式比較

| 加密類型    | Header                                    | 金鑰管理者 | 使用場景               |
| ----------- | ----------------------------------------- | ---------- | ---------------------- |
| **SSE-S3**  | `x-amz-server-side-encryption`            | AWS 管理   | 一般用途，最簡單       |
| **SSE-KMS** | `x-amz-server-side-encryption: aws:kms`⭐️  | AWS KMS    | 需要金鑰審計、權限控管 |
| **SSE-C**   | `x-amz-server-side-encryption-customer-*` | 你自己管理 | 自帶金鑰、合規要求     |

### SSE-S3 特徵

- 只需一個 header：`x-amz-server-side-encryption`
- AWS 自動輪替 master key
- AES-256 加密
- **最常考**：用 Bucket Policy 強制加密

### SSE-C 特徵

- 需要**三個** header（algorithm、key、MD5）
- 你負責金鑰管理
- 每次請求都要帶金鑰

**白話文**：沒帶加密 header 就不准上傳

### 記憶技巧

- SSE-S3 = 一個 header 搞定
- SSE-C = 三個 customer header（customer 自帶金鑰當然麻煩）

## Snowball vs Snowball Edge 比較

**Snowball**：純資料傳輸設備

**Snowball Edge**：資料傳輸 + 邊緣運算能力

| 特性         | Snowball           | Snowball Edge                  |
| ------------ | ------------------ | ------------------------------ |
| **主要用途** | 大量資料遷移至 AWS | 資料遷移 + 本地運算            |
| **儲存容量** | 50TB / 80TB        | 80TB - 210TB                   |
| **運算能力** | ❌ 無               | ✅ 有（可執行 EC2、Lambda）     |
| **使用場景** | 單純搬資料上雲     | 需要本地處理資料、斷線環境作業 |
| **價格**     | 較便宜             | 較貴（因為有運算）             |

## 考試重點判斷

### 選 Snowball 的情境

- 題目只提「大量資料遷移」
- 單純從 on-premises 搬到 S3
- 強調成本最優

### 選 Snowball Edge 的情境

- 題目出現「**邊緣運算**」、「**本地處理**」
- 需要在「**離線/斷網**」環境運作
- 要在設備上「**執行應用程式**」
- 資料需要「**預處理後再上傳**」

## DataSync vs Storage Gateway 比較

| 面向         | DataSync               | Storage Gateway            |
| ------------ | ---------------------- | -------------------------- |
| **設計目的** | 資料搬遷工具           | 混合雲儲存閘道             |
| **本質**     | 把資料從 A 點搬到 B 點 | 讓地端系統無縫存取雲端儲存 |
| **使用時機** | 一次性/定期大量遷移    | 持續雙向存取               |
| **地端角色** | 任務完成後可移除 agent | 需長期運行 gateway         |
| **典型場景** | 資料中心關閉、歸檔     | 地端應用仍需讀寫雲端資料   |

## EventBridge vs SNS 比較表

| 項目            | **EventBridge**                        | **SNS**                         |
| --------------- | -------------------------------------- | ------------------------------- |
| **設計目的**    | 事件驅動架構的核心路由器               | 訊息廣播與通知 (Pub/Sub)        |
| **資料流方式**  | 事件發生 → 根據規則路由給多個 target   | 主題發佈 → 廣播給所有訂閱者     |
| **路由邏輯**    | 支援複雜過濾條件，可針對 JSON 屬性比對 | 僅 topic-based 訂閱，無內容過濾 |
| **整合對象**    | 原生整合 100+ AWS 服務與 SaaS          | SQS、Lambda、HTTP、SMS、Email   |
| **傳遞模型**    | Event Bus → Rule → Target              | Topic → Subscriber              |
| **典型用途**    | 跨服務事件通知、SaaS 整合、微服務解耦  | 警示通知、Lambda 觸發、系統廣播 |
| **與 SQS 整合** | 可將事件送至 SQS 作為 target           | 常搭配 SQS 成 fan-out 架構      |
| **延遲/吞吐**   | 延遲略高但功能更強                     | 延遲極低，適合高頻簡單通知      |
| **考試關鍵字**  | 事件路由、過濾條件、多目標整合、SaaS   | 即時通知、簡單廣播、Email/SMS   |

## Aurora Endpoint 背後的運作機制

| Endpoint 類型        | DNS 解析行為                         | 實際效果           |
| -------------------- | ------------------------------------ | ------------------ |
| **Cluster Endpoint** | 永遠指向 **Primary (Writer)**        | 寫入流量固定打主機 |
| **Reader Endpoint**  | Round-robin 到**所有** read replicas | 讀取流量自動分散   |
| **Custom Endpoint**  | Round-robin 到**你指定的** instances | 流量只打你選的機器 |

後二者都有**自動負載平衡**效果，但 Reader Endpoint 是打所有 read replica，Custom Endpoint 是只打你指定的那幾台。

## Systems Manager Parameter Store vs Secrets Manager

| 功能          | Parameter Store | Secrets Manager |
| ------------- | --------------- | --------------- |
| 加密儲存      | ✅ SecureString  | ✅               |
| **自動輪換**⭐️ | ❌ 需自建 Lambda | ✅ 原生支援      |
| RDS 整合⭐️     | ❌               | ✅               |
| 成本          | 免費（標準層）  | 付費            |

### 為什麼 Parameter Store 是錯的

題目明確要求：

- ✅ 加密 → Parameter Store 可以
- ✅ **定期自動輪換** → Parameter Store **做不到**
- ✅ 應用程式用最新版 → Parameter Store 需額外開發

## S3 Object Lock 保護模式比較

```bash
Object Lock（功能開關）
├── Retention Mode（保留模式，可選 - 兩種選項選一）
│   ├── Compliance Mode（最嚴格）
│   └── Governance Mode（可特殊權限覆寫）
└── Legal Hold（獨立開關，可搭配使用）
```

| 配置                    | 說明                                  |
| ----------------------- | ------------------------------------- |
| Compliance Mode only    | 法規遵循，到期前完全不可刪            |
| Governance Mode only    | 一般保護，緊急時可覆寫                |
| Legal Hold only         | 訴訟保護，無期限                      |
| Compliance + Legal Hold | **最強保護**：到期前不可刪 + 訴訟保護 |
| Governance + Legal Hold | 一般保護 + 訴訟保護                   |

「誰能解除」那行有問題——Governance Mode 是可以被特殊權限解除的，只有 Compliance Mode 才是無人能解除。

修正版：

## Legal Hold vs Retention Period

| 比較項目     | Retention Period                                   | Legal Hold                        |
| ------------ | -------------------------------------------------- | --------------------------------- |
| **期限設定** | 固定時間（如 1 年）                                | 無期限                            |
| **自動解除** | 到期自動解除                                       | 必須手動移除                      |
| **誰能解除** | Compliance: 無人可解除；Governance: 特殊權限可繞過 | 有 `s3:PutObjectLegalHold` 權限者 |
| **適用場景** | 合規要求有明確期限                                 | 法律訴訟/調查（期限未定）         |
| **可否共存** | 可與 Legal Hold 同時存在                           | 可與 Retention Period 同時存在    |
| **保護機制** | 期限內 WORM 保護                                   | 手動移除前持續 WORM 保護          |

## 災難復原 (DR) 策略比較

| 設計策略                                 | RPO（資料損失）    | RTO（停機時間）    | 成本     | 複雜度   |
| ---------------------------------------- | ------------------ | ------------------ | -------- | -------- |
| 手動備份 + Restore                       | 大                 | 大                 | 低       | 低       |
| 非同步複製（Read Replica）               | 小 to 中（看延遲） | 中（需手動切換）   | 中       | 中       |
| **同步複製 + 自動 Failover（Multi-AZ）** | **極小（接近 0）** | **小（1 分鐘內）** | 高       | 高       |
| **跨區同步 + 全球複寫（企業級 DR）**     | **極小**           | **極小**           | **最高** | **最高** |

## Aurora Serverless v2 vs 標準 Aurora (Provisioned)

| 特性         | Aurora Provisioned            | Aurora Serverless v2   |
| ------------ | ----------------------------- | ---------------------- |
| **容量管理** | 手動選擇 instance type        | 自動擴縮 (ACU)         |
| **擴縮速度** | 需手動調整 (停機或加 replica) | 秒級自動調整           |
| **計費方式** | 按 instance 小時計費          | 按實際 ACU 使用量計費  |
| **成本**     | 固定成本，可預測              | 浮動成本，流量低時較省 |
| **最低成本** | 持續運行的最小 instance       | 可縮到 0.5 ACU         |
| **適用場景** | 穩定、可預測流量              | 波動大、難預測流量     |

## EC2 Placement Groups 比較

| 類型          | 用途               | 數量限制                   | 網路特性       |
| ------------- | ------------------ | -------------------------- | -------------- |
| **Spread**    | 高可用，分散風險   | 每 AZ 最多 7 個            | 一般           |
| **Cluster**   | 高效能運算，低延遲 | 無硬性限制                 | 10 Gbps 高頻寬 |
| **Partition** | 大規模分散式系統   | 每 AZ 最多 7 個 partitions | 一般           |

## DynamoDB On-Demand vs Provisioned 模式比較

| 比較項目     | On-Demand        | Provisioned                  |
| ------------ | ---------------- | ---------------------------- |
| 容量管理     | 自動調整         | 需手動設定 RCU/WCU           |
| Auto Scaling | 不需要（已內建） | **預設關閉**，需**手動啟用** |
| 適用場景     | 流量不可預測     | 流量穩定可預測               |

## EC2 計費狀態整理

### 會計費的狀態

| 實例類型  | 狀態                        | 原因                                  |
| --------- | --------------------------- | ------------------------------------- |
| On-Demand | `stopping` (準備 hibernate) | **休眠保留記憶體內容，持續佔用資源**  |
| Reserved  | `terminated`                | **RI 是預付費合約，用不用都要付到期** |
| 所有類型  | `running`                   | 正常使用中                            |

### 不計費的狀態

| 狀態                   | 說明                       |
| ---------------------- | -------------------------- |
| `pending`              | 啟動中，還沒進入 running   |
| `stopping` (準備 stop) | 正常關機準備，非 hibernate |
| `stopped`              | 已關機                     |
| `shutting-down`        | 準備終止                   |

### Spot Instance 不會在 `stopping` 計費

因為 Spot 是「用多少付多少」，準備關機時 AWS 已經不提供資源了

## 自動備份：RDS vs EBS 快照機制比較

| 項目                         | RDS                              | EBS                    |
| ---------------------------- | -------------------------------- | ---------------------- |
| 自動備份                     | 內建功能，需設定保留期才啟用     | 需配置 DLM/AWS Backup  |
| 保留期⭐️                      | 1-35 天（設 0 = 關閉）           | 自訂，無上限           |
| Point-in-time restore (PITR) | ✅ 支援（5 分鐘粒度）             | ❌ 只能還原到快照時間點 |
| 備份範圍                     | 整個 DB instance                 | 單一 EBS volume        |
| 費用                         | 備份儲存收費（超過 DB 大小部分） | 快照儲存收費           |

RDS 自動備份是內建功能，但**預設保留期是 0 天（等於關閉）**。
要啟用自動備份，你需要將保留期設為 1-35 天。這不是「額外付費功能」，而是：

- 備份儲存本身要收費（超過 DB instance 大小的部分）
- 功能本身不收錢，但儲存空間要錢

## 備份考試關鍵

- 題目問「自動備份」→ RDS 內建此功能（但**預設關閉**）
- EBS 也能自動化，但需額外配置（**DLM**/EventBridge/AWS Backup）
- 需要 point-in-time recovery → 只能選 RDS
- RDS 需要**超過 35 天**的備份 → 用 AWS Backup 搭配 RDS

## Security Group vs Network ACL 比較

| 項目             | Security Group (SG)                    | Network ACL (NACL)                            |
| ---------------- | -------------------------------------- | --------------------------------------------- |
| **規則評估方式** | **同時評估所有規則，沒有順序性**       | **依照 rule number 從小到大依序檢查**         |
| **匹配邏輯**     | **OR 條件**：只要任一條符合就允許      | **第一個符合的 rule 就決定結果 (Allow/Deny)** |
| **規則方向**     | Inbound / Outbound 各有一組 Allow 規則 | Inbound / Outbound 各有 Allow 與 Deny 規則    |
| **預設行為**     | 預設 Deny all (沒有 match 就拒絕)      | 有明確的 Default rule (rule #32767 Deny all)  |

### ✅ 小結論

- SG **沒有順序概念**（考試常見陷阱）
- SG 的每條規則都是 **獨立且平行評估**
- 任何一條規則匹配 → **流量允許**
- 沒有任何規則匹配 → **流量拒絕**

### 🧠 記憶技巧

- **NACL = 防火牆 (Firewall, 有順序，有 Deny)**
- **SG = 白名單 (Whitelist, 無順序，全是 Allow)**

## ECS on Fargate vs EKS on Fargate 比較

| 項目     | ECS on Fargate  | EKS on Fargate        |
| -------- | --------------- | --------------------- |
| 編排工具 | ECS (AWS 專屬)  | Kubernetes (業界標準) |
| 配置方式 | Task Definition | K8s YAML/Helm         |
| 適用場景 | AWS-native 應用 | 需要 K8s 生態系       |
| 學習曲線 | 簡單            | 較陡峭，需要懂 K8s    |

## Storage Gateway 三兄弟對照

| 類型             | 協定      | 後端儲存                           | 使用場景                           |
| ---------------- | --------- | ---------------------------------- | ---------------------------------- |
| **File Gateway** | NFS/SMB   | S3                                 | 檔案備份、檔案共享                 |
| Volume Gateway   | iSCSI     | S3（資料以 EBS snapshot 格式儲存） | Block storage、可還原為 EBS volume |
| **Tape Gateway** | iSCSI VTL | S3/Glacier                         | 磁帶備份系統替代                   |

## EBS Volume Types 重點整理

| Volume Type                        | 特點                 | 適用場景                              |
| ---------------------------------- | -------------------- | ------------------------------------- |
| **General Purpose SSD (gp2/gp3)**  | 預設選擇，平衡性價比 | 中小型資料庫、開發測試環境、開機磁碟  |
| **Provisioned IOPS SSD (io1/io2)** | 穩定低延遲、高 IOPS  | I/O 密集應用，大型關聯式/NoSQL 資料庫 |
| **Magnetic (舊世代)**              | 最低成本             | 低頻存取、成本敏感場景                |

## AWS Directory Service 基本概念

**目錄 (Directory)** = 儲存使用者身份資訊的資料庫

- 使用者名稱、密碼
- 群組成員資格
- 存取權限
- 組織結構

類比：就像公司的員工通訊錄系統，記錄每個人的職位、部門、權限

## AWS Directory Service 的角色

提供三種目錄服務：

| 服務                     | 用途                                    |
| ------------------------ | --------------------------------------- |
| AWS Managed Microsoft AD | 完整的 Active Directory，可建立信任關係 |
| AD Connector             | 代理服務，連接到地端的 AD               |
| Simple AD                | 輕量版 AD，適合小型需求                 |

**重點**：只負責「儲存和管理使用者資訊」，**不負責** 跨 AWS 帳戶的身份驗證

## ENA vs EFA 比較

| 特性           | ENA                | EFA           |
| -------------- | ------------------ | ------------- |
| 基本 VPC 網路  | ✅                  | ✅             |
| OS-bypass      | ❌                  | ✅             |
| HPC 加速       | ❌                  | ✅             |
| 支援 MPI、NCCL | ❌                  | ✅             |
| 適用場景       | 一般高網路效能需求 | HPC、機器學習 |

## 記憶口訣：審計追蹤 = 一定是 KMS

| 加密方式 | Audit Trail            | 自動輪替   | 考試判斷       |
| -------- | ---------------------- | ---------- | -------------- |
| SSE-KMS  | ✅ CloudTrail 完整記錄⭐️ | ✅ 自動     | 有合規需求必選 |
| SSE-S3   | ❌ 只有 S3 操作紀錄     | ✅ 但看不見 | 最簡單/最便宜  |
| SSE-C    | ❌ 無                   | ❌ 自己做   | 自己管金鑰     |

題目說 "audit trail that shows **who used the encryption key**" → 直接鎖定 KMS。

## RDS 自帶的(CloudWatch 基本指標)

從 hypervisor 層收集，不需額外啟用:

| 指標類別             | 說明                         |
| -------------------- | ---------------------------- |
| CPU Utilization      | Hypervisor 層面的 CPU 使用率 |
| Database Connections | DB 連線數                    |
| Freeable Memory      | 可用記憶體                   |
| Read/Write IOPS      | 讀寫操作數                   |
| Network Throughput   | 網路吞吐量                   |

## 二、Enhanced Monitoring 才有的

從 **DB instance OS 層的 agent** 收集，需**手動**啟用:

| 指標類別             | 說明                                    |
| -------------------- | --------------------------------------- |
| **Process List**     | 顯示各 process/thread 的資源使用        |
| RDS child processes⭐️ | DB 主程序(如 `mysqld`, `aurora`)        |
| RDS processes        | RDS 管理 agent、診斷程序                |
| OS processes⭐️        | Kernel、系統程序                        |
| **Per-process CPU**  | 個別 process 的 CPU 使用率              |
| **Memory breakdown** | 詳細記憶體分配(active/inactive/buffers) |

顯然，所有關於 **process** 的指標，都是 Enhanced Monitoring 獨有的。

## IAM Policy 解讀

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "VisualEditor0",
      "Effect": "Allow",
      "Action": ["ds:*"],
{
  "Version": "2012-10-17",  // Policy 語法版本(固定值)
  "Statement": [             // 權限規則陣列
    {
      "Sid": "VisualEditor0", // Statement ID(可選,用於識別)
      "Effect": "Allow",       // 允許或拒絕
      "Action": ["ds:*"],     // 允許的操作
      "Resource": "arn:aws:ds:us-east-1:123456789012:directory/d-1234567890"
    },
    {
      "Effect": "Allow",
      "Action": ["ec2:*"],
      "Resource": "*"         // 所有資源
    }
  ]
}
```

### **第一段 Policy（ds:*）**

允許此 IAM 身分 **對指定的 AWS Directory Service 目錄做所有操作**（ds:*），但**只限特定 ARN 的那個 directory**。

### **第二段 Policy（ec2:*）**

允許此 IAM 身分 **對所有 EC2 資源執行所有操作**（ec2:\*），不限制資源範圍（Resource =*）。

這是兩個獨立的 Statement，兩者**是累加的**，不會互相覆蓋。

## AMI 兩種類型

| 類型                      | Root Device    | 資料持久性                   |
| ------------------------- | -------------- | ---------------------------- |
| **EBS-backed**            | EBS Volume     | 終止後**保留**(除非設定刪除) |
| **Instance Store-backed** | Instance Store | **終止後自動刪除**           |

## S3 Presigned URLs vs CloudFront Signed URLs

| 比較項目 | S3 Presigned URLs | CloudFront Signed URLs       |
| -------- | ----------------- | ---------------------------- |
| 效能     | 直連 S3，無快取   | 全球 CDN 加速                |
| 成本     | 直接從 S3 傳輸    | 透過 CloudFront 節省頻寬成本 |
| 使用場景 | 簡單的檔案下載    | 大規模、全球分發             |

## Elastic IP 是綁在哪？

| AWS 元件                        | 能不能綁 EIP？ | 說明                                                                            |
| ------------------------------- | -------------- | ------------------------------------------------------------------------------- |
| **NAT Gateway**                 | ✅ 可以         | NAT Gateway 建立時就必須指定一個 EIP，這就是外部看到的 NAT 公網 IP。            |
| **Internet Gateway (IGW)**      | ❌ 不行         | IGW 是一個區域性的 VPC 出口，只負責「路由」，不是一個實體端點，沒有 IP 可以綁。 |
| **EC2**（透過 ENI）             | ✅ 可以         | EIP 綁在 EC2 NIC（ENI）上。                                                     |
| **Network Load Balancer (NLB)** | ✅ 可以         | 每個 AZ 可指定一個 EIP，適合需要固定 IP 白名單的場景。                          |

## NAT Gateway vs Internet Gateway vs Bastion Host

| 服務                 | 用途                        | 位置          |
| -------------------- | --------------------------- | ------------- |
| **NAT Gateway**      | Private subnet 主動對外連線 | Public subnet |
| **Internet Gateway** | 讓 Public subnet 雙向通訊   | VPC level⭐️    |
| **Bastion Host**     | 讓你 SSH 進 Private subnet  | Public subnet |

## ✅ Security Group（SG） vs Network ACL（NACL）

| 項目         | **Security Group (SG)**                                                                       | **Network ACL (NACL)**                                                                            |
| ------------ | --------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **層級**     | Instance level（綁 EC2、ENI）                                                                 | Subnet level（作用於整個 Subnet）                                                                 |
| **規則性質** | **Allow-only（白名單）**                                                                      | Allow + Deny                                                                                      |
| **狀態性**   | **Stateful**（回程自動允許）                                                                  | **Stateless**（需 inbound + outbound 兩邊都設）                                                   |
| **用途定位** | **精準控制**：限制某 IP / 某 Port                                                             | **粗粒度過濾**：阻擋 CIDR、整段流量策略                                                           |
| **常見考題** | - "Only allow XYZ IP" / - "Restrict SSH/FTP/RDP from specific IPs" / - "Tight access control" | - "**Block a malicious IP range**" / - "Subnet-wide allow/deny" / - "Additional layer of defense" |
| **預設行為** | Deny all inbound, allow all outbound                                                          | Allow all inbound/outbound（Default VPC）                                                         |
| **何時用？** | **要允許某人進來** → SG                                                                       | **要阻擋整段流量** → NACL                                                                         |

提醒：NACL 完全不是**擋單一 IP** 的最佳工具。(可以做到但很麻煩且不直觀)

## S3 Bucket Policy

兩者都用相同的 JSON 語法（AWS Policy Language），但：

| 比較         | IAM Policy                    | S3 Bucket Policy            |
| ------------ | ----------------------------- | --------------------------- |
| **附加對象** | IAM User / Role / Group       | S3 Bucket 本身              |
| **控制角度** | 「誰」能做什麼                | 「這個 Bucket」允許誰做什麼 |
| **跨帳號**   | 只能管自己帳號內的 principal  | 可授權其他 AWS 帳號存取     |
| **Resource** | 必須明確指定（如 bucket ARN） | 可省略，預設就是該 bucket   |

## 強制 S3 上傳加密的 Bucket Policy 範例

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*",
      "Condition": {
        "StringNotEquals": {
          "s3:x-amz-server-side-encryption": "AES256"
        }
      }
    }
  ]
}
```

**考試判斷技巧**：

- 看到 `Principal` 欄位 → Bucket Policy（或 Resource-based Policy）
- 沒有 `Principal` → IAM Policy（identity-based）

## NAT Gateway vs Egress-only Internet Gateway 比較

**Egress-only Internet Gateway 直接附加在 VPC level**，不是掛在特定 subnet 上。

| 項目     | NAT Gateway           | Egress-only IGW    |
| -------- | --------------------- | ------------------ |
| 掛載位置 | Public Subnet         | VPC level          |
| 協定     | IPv4                  | **IPv6 only**      |
| 路由設定 | Private Subnet 指向它 | IPv6 subnet 指向它 |
| 費用     | 按小時 + 流量計費     | **完全免費**       |

### 運作方式

Egress-only IGW:

- 允許 VPC 內的資源「主動發起」IPv6 連線到外部
- 阻止外部「主動發起」IPv6 連線進來
- 就像 IPv6 版的 NAT，但不做位址轉換（IPv6 夠用，不需要 NAT）

## Aurora Auto Scaling vs Global Database

| 特性         | Aurora Auto Scaling        | Aurora Global Database                  |
| ------------ | -------------------------- | --------------------------------------- |
| **解決問題** | 單一區域的讀取擴展         | 跨區域**災難復原** + 全球**低延遲讀取** |
| **擴展方式** | 自動增減 read replica 數量 | 在多個 AWS 區域建立只讀副本             |
| **延遲**     | 毫秒級 (同區域)            | 次秒級 (跨區域複製延遲 < 1秒)           |
| **成本**     | 只付使用的 replica         | 跨區域資料傳輸 + 多區域儲存成本         |
| **使用場景** | 流量波動大的應用           | 全球化業務、DR 需求                     |

## Aurora Auto Scaling 概覽

| 項目         | 說明                                                      |
| ------------ | --------------------------------------------------------- |
| **預設狀態** | 關閉，需手動設定 Auto Scaling policy                      |
| **適用版本** | Aurora **Provisioned only**（Serverless v2 自帶容量調整） |
| **功能費用** | 免費                                                      |
| **實際成本** | 依產生的 Read Replica instance 規格與時間計費             |
| **調整標的** | Read Replica **數量**（非單一 instance 大小）             |
| **常用指標** | CPU utilization、連線數                                   |

## AWS 儲存服務考試重點對照表

| 服務                     | 類型          | 協定            | 考試關鍵字                       | 適用場景                              |
| ------------------------ | ------------- | --------------- | -------------------------------- | ------------------------------------- |
| **EBS**                  | Block         | iSCSI (隱藏)    | EC2 root volume、snapshot        | 單一 EC2 專用，高效能                 |
| **Instance Store**       | Block         | 直接附加        | 暫時性、ephemeral、highest IOPS  | 臨時資料、快取                        |
| **FSx for NetApp ONTAP** | Block⭐️ + File | iSCSI + SMB/NFS | 交易系統、Multi-AZ block storage | 需要 block 的 Windows⭐️/Linux 混合環境 |
| **EFS**                  | File          | NFS             | Linux、shared access、serverless | 多 EC2 共享，自動擴展                 |
| **FSx for Windows**      | File          | SMB             | Active Directory、user shares    | Windows 檔案共享                      |
| **FSx for Lustre**       | File          | Lustre          | HPC、ML training、S3 integration | 高效能運算                            |
| **S3**                   | Object        | HTTP/HTTPS      | 靜態網站、備份、lifecycle        | 幾乎所有非即時存取需求                |

## 考試判斷樹

```bash
需要共享存取？
├─ NO → EBS (單一 EC2)
└─ YES
    ├─ Block storage？
    │   └─ YES → FSx for NetApp ONTAP (iSCSI)
    └─ File storage？
        ├─ Linux → EFS (NFS)
        ├─ Windows → FSx for Windows (SMB)
        ├─ HPC/ML → FSx for Lustre
        └─ 備份/靜態 → S3
```

## File vs Block vs Object 儲存差異

| 特性         | Block Storage       | File Storage     | Object Storage          |
| ------------ | ------------------- | ---------------- | ----------------------- |
| **存取單位** | 固定大小區塊 (4KB)  | 檔案 + 目錄結構⭐️ | 物件 (檔案 + metadata⭐️) |
| **存取方式** | 直接讀寫磁碟區塊    | 檔案路徑⭐️        | HTTP API / URL ⭐️        |
| **效能**     | 最快 (sub-ms)       | 快 (ms)          | 較慢 (100ms+)           |
| **使用情境** | OS、資料庫          | 共享檔案         | 備份、靜態內容          |
| **AWS 服務** | EBS、Instance Store | EFS、FSx         | S3、Glacier             |

## 技術差異

### Block Storage (區塊儲存)

```bash
原始磁碟 → 作業系統格式化 → 應用程式直接讀寫
特點: 像本地硬碟，需要格式化 (ext4/NTFS)
```

### File Storage (檔案儲存)

```bash
已有檔案系統 → 透過 NFS/SMB 掛載 → 多台共享
特點: 不用格式化，直接使用
```

### Object Storage (物件儲存)

```bash
無階層結構 → 透過 API 存取 → key-value 模式
特點: 平面命名空間，URL 存取
```

### 考試判斷關鍵字

| 需求描述                        | 選擇類型   | AWS 服務     |
| ------------------------------- | ---------- | ------------ |
| Database、OS boot、low latency  | **Block**  | EBS          |
| Shared files、multi-EC2 access  | **File**   | EFS/FSx      |
| Backup、archive、static website | **Object** | S3           |
| "iSCSI protocol"                | **Block**  | NetApp ONTAP |
| "mount point"、"directory"      | **File**   | EFS/FSx      |
| "REST API"、"lifecycle policy"  | **Object** | S3           |

### 實際類比

- **Block** = 你插一顆新硬碟到電腦
- **File** = 你掛載一個網路磁碟 (NAS)
- **Object** = 你用網址下載檔案 (Google Drive)

## FSx 名稱由來解析

這個「x」其實就是一個**變數/萬用字元**的概念，代表「**多種檔案系統**」。

就像數學裡的 x 可以代入不同的值，FSx 的 x 代表 Amazon 支援的各種第三方檔案系統：

| FSx 變體                        | x = ?        | 用途場景                               |
| ------------------------------- | ------------ | -------------------------------------- |
| FSx for **Windows** File Server | Windows      | Windows 環境的共享檔案系統（SMB 協定） |
| FSx for **Lustre**              | Lustre       | 高效能運算（HPC）、機器學習            |
| FSx for **NetApp ONTAP**        | NetApp ONTAP | 企業級 NAS，多協定支援                 |
| FSx for **OpenZFS**             | OpenZFS      | Linux 工作負載，ZFS 快照功能           |

簡單說，AWS 的命名邏輯是：

- **EFS** = Elastic **File System**（AWS 原生的 Linux NFS）
- **FSx** = **File System x**（第三方檔案系統的託管版）

所以當你看到 FSx，就知道這是 AWS 幫你託管「**別人家的檔案系統**」，讓你不用自己架設和維護。

## IAM Database Authentication 與傳統密碼認證比較

```python
# 傳統方式
conn = psycopg2.connect(
    host="xxx.rds.amazonaws.com",
    user="admin",
    password="hardcoded_password"  # 💀 危險
)

# IAM Authentication 方式
import boto3
token = boto3.client('rds').generate_db_auth_token(
    DBHostname="xxx.rds.amazonaws.com",
    Port=5432,
    DBUsername="iam_user"
)
conn = psycopg2.connect(
    host="xxx.rds.amazonaws.com",
    user="iam_user",
    password=token,  # ✅ 15 分鐘後自動失效
    sslmode='require'
)
```

## 標準 KMS 與 Custom Key Store 比較

| 特性     | 標準 KMS    | Custom Key Store            |
| -------- | ----------- | --------------------------- |
| HSM      | AWS 多租戶  | 你的專屬 CloudHSM           |
| 金鑰刪除 | 7-30 天等待 | 立即移除                    |
| 稽核     | CloudTrail  | CloudTrail + 你自己的監控   |
| 成本     | 低          | 高(需維護 CloudHSM cluster) |
| 適用     | 一般場景    | 嚴格合規需求                |

**成本提醒**: CloudHSM 很貴，一個 cluster 至少需要 2 個 HSM instance，每個約 $1.45/hr。考試不會考價格，但實務上要知道這是「必要時才用」的方案。

## KMS 金鑰的三種類型

這段在強調 **KMS 金鑰的三種所有權類型**，考試常考混淆點。

| 類型                 | 管理者 | 你能控制什麼  | 能否稽核            |
| -------------------- | ------ | ------------- | ------------------- |
| **AWS owned**        | AWS    | 無            | ❌ 不可見            |
| **AWS managed**      | AWS    | 無法刪除/輪替 | ✅ CloudTrail only ⭐️ |
| **Customer managed** | 你     | 完全控制      | ✅ CloudTrail + 自訂 |

## RDS Enhanced Monitoring 重點

**核心功能**：監控 RDS 底層 OS 的即時效能指標

**與 CloudWatch Metrics 的差異**：

| 項目     | CloudWatch Metrics    | Enhanced Monitoring           |
| -------- | --------------------- | ----------------------------- |
| 監控層級 | Hypervisor 層(虛擬化) | OS 層(實際資源)               |
| 粒度     | 1 分鐘                | 最快 1 秒                     |
| 指標範例 | CPU utilization       | CPU per process, memory usage |
| 儲存位置 | CloudWatch            | CloudWatch Logs⭐️              |

**關鍵考點**:

- 預設保留 **30 天**,可調整 `RDSOSMetrics` log group 設定
- 需要細粒度 OS 層級監控時使用(如查看哪個 process 吃 CPU)
- 存在 **CloudWatch Logs**(不是 CloudWatch Metrics)

**考試判斷**:

- 題目要「process-level metrics」→ Enhanced Monitoring
- 題目要「1 秒監控間隔」→ Enhanced Monitoring
- 題目只要基本 CPU/memory → CloudWatch Metrics 就夠

## Aurora Global Database 考試重點整理

**跨區域災難恢復 + 全球讀取加速**的終極解決方案

## 關鍵數字(必背)

| 指標           | 數值                        | 意義                           |
| -------------- | --------------------------- | ------------------------------ |
| **RPO**        | < 1 秒                      | 資料複製延遲，幾乎不會遺失資料 |
| **RTO**        | < 1 分鐘                    | 故障時切換到備用區域的時間     |
| **複製延遲**   | < 1 秒                      | Storage-based 自動複製         |
| **支援區域數** | 最多 5 個 secondary regions | 1 primary + 5 secondary        |

### 與其他方案比較

| 需求             | Aurora Global DB | Multi-AZ     | Read Replica   |
| ---------------- | ---------------- | ------------ | -------------- |
| 區域級災難恢復   | ✅                | ❌            | ❌              |
| 全球低延遲讀取   | ✅                | ❌            | 部分(需手動建) |
| 自動故障切換     | ✅ (跨區域)       | ✅ (單區域內) | ❌              |
| RTO/RPO 要求極致 | ✅                | 一般         | 不適用         |

## RDP vs SSH 考試比較表

| 比較項目           | RDP                                        | SSH                                |
| ------------------ | ------------------------------------------ | ---------------------------------- |
| **Port 號碼**      | 3389                                       | 22                                 |
| **作業系統**       | Windows Server                             | Linux/Unix                         |
| **關鍵字提示**     | Active Directory / SMB / Windows app / GUI | Linux instance / CLI / 自動化腳本  |
| **Security Group** | Inbound 3389                               | Inbound 22                         |
| **常見場景**       | AD Domain / Windows FSx / .NET 部署        | EC2 管理 / 腳本執行 / 伺服器維護   |
| **身份驗證**       | Username + Password                        | Key Pair (推薦) / Password         |
| **安全性考點**     | 限制來源 IP / Bastion Host / 啟用 NLA      | Key Pair / 禁用密碼 / Bastion Host |

## S3 存儲等級考試重點對照表

| 存儲等級                          | 適用場景(關鍵字)         | 成本特性                | 可用性 | 最小存儲時間 | 檢索時間       | 考試陷阱/記憶點                                               |
| --------------------------------- | ------------------------ | ----------------------- | ------ | ------------ | -------------- | ------------------------------------------------------------- |
| **S3 Standard**                   | 頻繁存取、關鍵數據       | 最貴                    | 99.99% | 無           | 毫秒級         | **預設選項**,看到「frequently accessed」就選它                |
| **S3 Intelligent-Tiering**        | 存取模式不確定、自動優化 | 有監控費用              | 99.9%  | 無           | 毫秒級         | 關鍵字「**unpredictable access**」、「**changing patterns**」 |
| **S3 Standard-IA**                | 不常存取但需快速取回     | 便宜但有**檢索費** ⭐️    | 99.9%  | **30天**     | 毫秒級         | 看到「**infrequent**」+「**immediate access**」               |
| **S3 One Zone-IA**                | **可重建**的非關鍵數據   | 比 Standard-IA 便宜 20% | 99.5%  | **30天**     | 毫秒級         | 關鍵字「**single AZ**」、「**can be recreated**」             |
| **S3 Glacier Instant Retrieval**  | 季度存取的歸檔數據       | 更便宜                  | 99.9%  | **90天**     | **毫秒級**     | 「**quarterly access**」+「需要**立即取回**」                 |
| **S3 Glacier Flexible Retrieval** | 歸檔數據、可接受等待     | 很便宜                  | 99.99% | **90天**     | **分鐘到小時** | 「**archive**」+「**不急著拿**」,有加速選項                   |
| **S3 Glacier Deep Archive**       | 長期保存、合規需求       | 最便宜                  | 99.99% | **180天**    | **12小時**     | 「**7-10 years**」、「**compliance**」、「**cheapest**」      |

## 考試判斷邏輯(從上到下檢查)

### Step 1: 看關鍵字直接秒殺

- **unpredictable/changing** → Intelligent-Tiering
- **recreatable/non-critical** + cost → One Zone-IA
- **compliance/7+ years** → Glacier Deep Archive
- **quarterly** → Glacier Instant Retrieval

### Step 2: 看存取頻率 + 檢索需求

```bash
頻繁存取 → Standard
不常存取 + 立即需要 → Standard-IA
不常存取 + 可等幾分鐘⭐️ → Glacier Flexible
不常存取 + 可等12小時 → Glacier Deep Archive
```

### Step 3: 看成本要求

- 題目強調「**most cost-effective**」→ 往下選(Glacier 方向)
- 題目強調「**performance**」→ 往上選(Standard 方向)

## 實戰記憶技巧

### 1️⃣ 最小存儲時間陷阱

```bash
IA 系列 = 30天
Glacier Instant = 90天
Glacier Flexible = 90天
Glacier Deep Archive = 180天
```

**陷阱**：如果你只存 15 天就刪除，照樣收 30/90/180 天的費用!

### 2️⃣ 檢索時間記憶法

```bash
毫秒級 = Standard 到 Glacier Instant(名字有 Instant)
分鐘到小時 = Glacier Flexible(有彈性,可以選)
12 小時 = Deep Archive(埋很深,挖很久)
```

### 3️⃣ One Zone-IA 的判斷公式

```bash
One Zone-IA = 能接受資料遺失風險 + 要省錢
```

**常見場景**：縮圖、可重新生成的數據、非關鍵備份

## DMS 常見組合

| 遷移場景                       | 工具選擇                     |
| ------------------------------ | ---------------------------- |
| 同質資料庫 (MySQL→MySQL)       | DMS 或 native tools          |
| 異質資料庫 (Oracle→PostgreSQL) | DMS + Schema Conversion Tool |
| 需要加密傳輸                   | DMS + SSL⭐️                   |
| 大量資料                       | DMS + Snowball Edge          |

## AWS 憑證/金鑰管理服務比較表

| 服務                                | 主要用途                         | 能否儲存憑證        | 自動輪換       | 考試判斷關鍵字                                         |
| ----------------------------------- | -------------------------------- | ------------------- | -------------- | ------------------------------------------------------ |
| **Secrets Manager**                 | **儲存、管理、自動輪換機密資料** | ✅ 核心功能          | ✅ 原生支援     | "rotate credentials", "database passwords", "API keys" |
| **Systems Manager Parameter Store** | 儲存配置參數與機密               | ✅ 可用 SecureString | ❌ 需手動實作   | "configuration data", "cheaper alternative"            |
| **AWS Certificate Manager (ACM)**   | 管理 SSL/TLS 憑證                | ❌ 只管理憑證        | ✅ 憑證自動續期 | "SSL/TLS", "HTTPS", "certificate"                      |
| **AWS KMS**                         | 加密金鑰管理                     | ❌ 不儲存憑證        | N/A            | "encryption keys", "encrypt data", "CMK"               |

### 考試必記判斷邏輯

看到這些關鍵字 → 選 Secrets Manager:

- "automatically rotate" + "database credentials/API keys"
- "securely store secrets"
- 題目強調「自動輪換」

選 Parameter Store vs Secrets Manager:

- Parameter Store = 便宜、簡單配置、**不需自動輪換**⭐️
- Secrets Manager = 貴一點、**需要自動輪換功能**⭐️

### ACM 陷阱

- 只要題目不是在講 HTTPS/SSL 憑證，**ACM 100% 是錯誤選項**
- ACM 管理的是「網站憑證」,不是「應用程式密碼」

### KMS 陷阱

- KMS 是「加密工具」，不是「保險箱」
- KMS 提供加密金鑰，但**不負責儲存你的應用程式憑證**
- Secrets Manager 底層會用 KMS 加密，但你不直接用 KMS 存密碼

### 考試記憶口訣

**"Secrets Manager = 自動輪換專家"**
需要 rotate → Secrets Manager，不需要 → Parameter Store

**"ACM = 網站鎖頭，KMS = 加密工廠"**
都不是存密碼的地方

## ELB vs Route 53 Weighted 流量分配比較

| 方案                  | 運作層級      | 健康檢查 | 自動故障轉移  | 適用場景                              |
| --------------------- | ------------- | -------- | ------------- | ------------------------------------- |
| **ELB**               | Layer 4/7     | ✅ 自動   | ✅ 即時        | **首選方案** - 單一 Region 內分散流量 |
| **Route 53 Weighted** | DNS (Layer 3) | ✅ 可設定 | ⚠️ 有 TTL 延遲 | 跨 Region 分配、A/B testing           |

## 考試判斷重點

題目只說「add elasticity」→ 兩者都能做到，但 ELB 是標準解法。

- ELB = 標準解法
- Route 53 Weighted = 也能做到

## S3 Lifecycle Policy vs DLM 比較表

| 特性         | S3 Lifecycle Policy                | DLM (Data Lifecycle Manager)   |
| ------------ | ---------------------------------- | ------------------------------ |
| **管理對象** | S3 objects                         | EBS snapshots, EBS-backed AMIs |
| **主要用途** | 自動轉換 storage class、刪除舊物件 | 自動建立、保留、刪除 snapshots |
| **典型場景** | 日誌歸檔、冷資料轉 Glacier         | EC2 備份自動化                 |
| **設定位置** | S3 bucket level                    | EC2 Console / Tag-based        |
| **觸發條件** | 天數、版本數                       | 排程(每 X 小時/天)             |

## 想到 Blacklist FQDN 就想到這兩個服務

| 服務                      | 用途                      | 能否封鎖 FQDN        |
| ------------------------- | ------------------------- | -------------------- |
| **Route 53 DNS Firewall** | 封鎖惡意網域              | ✅ 核心功能           |
| **Network Firewall**      | Layer 3-7 網路流量過濾    | ✅ 可以，但非主要用途 |
| WAF                       | HTTP/HTTPS 應用層攻擊防護 | ❌ 不處理 DNS         |
| Security Group            | IP/Port 控制              | ❌ 不認識 domain      |

dojo 中出現的就是 AWS Network Firewall。

## Geolocation vs Geoproximity Routing 比較表

| 特性           | Geolocation Routing                                    | Geoproximity Routing                             |
| -------------- | ------------------------------------------------------ | ------------------------------------------------ |
| **判斷依據**   | 用戶的地理位置(國家/州/大陸)                           | 用戶與資源的地理距離                             |
| **路由邏輯**   | 固定規則:歐洲 → EU server                              | 動態計算:選最近的 server                         |
| **Bias 調整**  | ❌ 不支援                                               | ✅ 可用 bias 擴大/縮小涵蓋範圍                    |
| **典型場景**   | 內容在地化、法規遵循                                   | 延遲優化、負載分散                               |
| **考試關鍵字** | "based on user location", "restrict content by region" | "route to nearest", "**bias**", "distance-based" |

## Site-to-Site VPN 連線要求

| 端點                              | 需要固定 IP? | 原因                                         |
| --------------------------------- | ------------ | -------------------------------------------- |
| **Customer Gateway (CGW)**        | ✅ 必須       | 你的 on-premises 設備需要公開 IP 讓 AWS 連線 |
| **Virtual Private Gateway (VGW)** | ❌ 不需要     | AWS 自動處理，你不用管                       |

## CloudTrail vs Config 比較

| 特性         | CloudTrail     | Config       |
| ------------ | -------------- | ------------ |
| **記錄內容** | API 呼叫動作   | 資源配置狀態 |
| **關注點**   | 誰做了什麼     | 資源長什麼樣 |
| **時間維度** | 事件發生時間點 | 配置變更歷史 |
| **合規性**   | 操作審計       | 配置合規檢查 |

### 實際例子

假設你刪除了一個 S3 bucket:

**CloudTrail 記錄**:

```bash
User: john@company.com
Action: DeleteBucket
Time: 2025-01-15 10:30:00
Bucket: my-important-data
Result: Success
```

**Config 記錄**:

```bash
Before: Bucket existed with versioning enabled
After: Bucket does not exist
Compliance: Non-compliant (violated retention policy)
```

## Storage Gateway 完整分類

| 類型               | 模式   | 協定      | 用途                    |
| ------------------ | ------ | --------- | ----------------------- |
| **File Gateway**   | -      | NFS/SMB   | 檔案共享，**直接存 S3** |
| **Volume Gateway** | Cached | iSCSI     | 區塊儲存，快取模式      |
| **Volume Gateway** | Stored | iSCSI     | 區塊儲存，完整本地      |
| **Tape Gateway**   | -      | iSCSI-VTL | 虛擬磁帶備份            |

## File Gateway vs Volume Gateway

| File Gateway                        | Volume Gateway                         |
| ----------------------------------- | -------------------------------------- |
| 協議：NFS/SMB                       | 協議：iSCSI（block）                   |
| 資料落點：S3 object                 | 資料落點：S3 + EBS Snapshots           |
| 用途：檔案上雲                      | 用途：Block Storage + 本地 Volume 備援 |
| 常見用途：檔案伺服器替代、NAS、備份 | 常見用途：VM backup、DB volumes、災備  |

### File Gateway

- 檔案介面 (NFS/SMB)
- **直接映射到 S3 objects**
- 每個檔案 = 一個 S3 object

### Volume Gateway

- 區塊介面 (iSCSI)
- 像是虛擬硬碟
- 資料以 EBS snapshots 形式存 S3⭐️

**考試判斷**: 題目提到 "file share" 或 "NFS/SMB" → File Gateway

## Route 53 的 A Record Alias

這是一個 Route 53 特有的功能，讓你可以在 Zone Apex 使用類似 CNAME 的功能。(但本質是 A Record)

| 記錄類型             | Zone Apex (`tutorialsdojo.com`) | 子網域 (`www.tutorialsdojo.com`) |
| -------------------- | ------------------------------- | -------------------------------- |
| **CNAME**            | ❌ DNS 規範禁止                  | ✅ 可以                           |
| **A Record (Alias)** | ✅ Route 53 特有功能             | ✅ 可以                           |

原則上，CNAME 只能用在子網域，不能用在 Zone Apex。但 Route 53 的 A Record Alias 可以解決這個限制。

## AWS Cost Explorer vs. Budgets 比較表

| 面向           | **Cost Explorer**                                               | **AWS Budgets**                                      |
| -------------- | --------------------------------------------------------------- | ---------------------------------------------------- |
| **核心功能**   | 分析與視覺化**過去**的成本數據                                  | 設定預算並**預警未來**支出                           |
| **時間導向**   | 回顧型（看歷史帳單）                                            | 前瞻型（設定門檻、觸發警報）                         |
| **主要用途**   | 找出花費在哪、趨勢分析、成本歸因                                | 控制預算、超支通知、自動化動作                       |
| **警報功能**   | ❌ 無原生警報                                                    | ✅ 支援 Email、SNS、自動化動作                        |
| **預測功能**   | ✅ 可預測未來成本（基於歷史）                                    | ✅ 可基於預測值設定警報                               |
| **自動化動作** | ❌                                                               | ✅ 可觸發 Lambda、停止 EC2 等                         |
| **報表/圖表**  | ✅ 強大的視覺化與篩選                                            | 基本圖表，重點在預算追蹤                             |
| **考試關鍵字** | "analyze costs"、"identify spending patterns"、"cost breakdown" | "alert"、"notification"、"threshold"、"budget limit" |

### 考試判斷口訣

- **看過去 → Cost Explorer**（分析、報表、哪裡花最多錢）
- **管未來 → Budgets**（設預算、超支警報、自動停機）

題目出現「**notify when cost exceeds**」或「**alert before overspending**」→ 選 **Budgets**

## AWS 網路安全防護層級比較表

重點是這些元件具體是在**哪個層級**運作

| 服務             | 層級             | 用途                     |
| ---------------- | ---------------- | ------------------------ |
| Security Group   | Instance         | 基本的 **L4** allow/deny |
| NACL             | Subnet           | Stateless L4 過濾        |
| WAF              | Application (L7) | HTTP/HTTPS 請求過濾      |
| Network Firewall | VPC 邊界         | 完整流量檢查 + IPS       |

## S3 Glacier Retrieval 完整比較

### Glacier（標準）

| 選項          | 時間      | 成本   | 適用場景           |
| ------------- | --------- | ------ | ------------------ |
| **Expedited** | 1-5 分鐘  | 最貴   | 緊急需求、小量資料 |
| **Standard**  | 3-5 小時  | 中等   | 一般取回，預設選項 |
| **Bulk**      | 5-12 小時 | 最便宜 | 大量資料、不急     |

### Glacier Deep Archive

| 選項         | 時間      | 成本   |
| ------------ | --------- | ------ |
| **Standard** | 12 小時內 | 中等   |
| **Bulk**     | 48 小時內 | 最便宜 |

Deep Archive **沒有 Expedited**——這是考試陷阱。

## Expedited 的額外細節

Expedited 有容量限制，高需求時可能失敗。如果需要**保證 Expedited 可用**，要購買 **Provisioned Capacity**（預留容量）。

**記憶口訣：**
> Glacier 三種速度：**快（分鐘）、標（小時）、慢（半天）**
> Deep Archive 只有兩種：**標（半天）、慢（兩天）**

## S3 Storage Classes Retrieval 費用

**S3 Standard 不用，但 S3 Standard-IA 和 S3 One Zone-IA 要。**

| Storage Class               | Retrieval 費用        | 備註                     |
| --------------------------- | --------------------- | ------------------------ |
| **S3 Standard**             | ❌ 無                  | 你付的就是儲存 + 請求    |
| **S3 Intelligent-Tiering**  | ❌ 無                  | 自動分層，不收 retrieval |
| **S3 Standard-IA**          | ✅ 有（per GB）        | 存取便宜的代價           |
| **S3 One Zone-IA**          | ✅ 有（per GB）        | 同上                     |
| **S3 Glacier Instant**      | ✅ 有（per GB）        | 更貴                     |
| **S3 Glacier Flexible**     | ✅ 有（per GB + 速度） | 越快越貴                 |
| **S3 Glacier Deep Archive** | ✅ 有（per GB + 速度） | 最便宜儲存，取回最貴     |

### 記憶方式

**有 "IA" 或 "Glacier" 字眼的，取回都要錢。**

這也是為什麼 **S3 Intelligent-Tiering** 很香——它會自動把資料搬到便宜的層級，但 **不收 retrieval 費用**。代價是每月有一筆 monitoring fee（per object），所以適合大檔案、不適合海量小檔案。

### 考試陷阱

> 「資料存取模式不可預測，需要最低成本且不希望有額外 retrieval 費用」

答案：**S3 Intelligent-Tiering**（不是 Standard-IA）

## Glacier Flexible Retrieval vs Glacier Deep Archive 比較表

| 比較項目                 | Glacier Flexible Retrieval | Glacier Deep Archive |
| ------------------------ | -------------------------- | -------------------- |
| **Expedited**            | 1-5 分鐘                   | ❌ 沒有這個選項       |
| **Standard**             | 3-5 小時                   | 12 小時              |
| **Bulk**                 | 5-12 小時                  | 48 小時              |
| **Provisioned Capacity** | ✅ 可搭配 Expedited         | ❌ 不適用             |

所以當題目出現「15 分鐘內取回」或「Expedited + Provisioned Capacity」，答案一定是 **Glacier Flexible Retrieval**，不可能是 Deep Archive。

## Aurora 核心優勢

| 特性          | 數據                                     |
| ------------- | ---------------------------------------- |
| **儲存上限**  | 128 TiB（RDS 只有 64 TiB）               |
| **效能**      | MySQL 的 5 倍、PostgreSQL 的 3 倍        |
| **自動擴展**⭐️ | 儲存以 10 GB 增量自動成長                |
| **高可用**    | 資料自動同步複製至多個 AZ，自動 failover |

**考試關鍵字**：超大規模、自動擴展儲存、多 AZ 同步、高效能 + MySQL/PostgreSQL 相容 → **Aurora**

## EC2 Placement Groups 比較表

注意：所有類型的 Placement Group 都只能在**單一 Region** 內使用。

| 類型          | 目的           | 部署方式           | 跨 AZ | 適用場景                                 |
| ------------- | -------------- | ------------------ | ----- | ---------------------------------------- |
| **Cluster**   | 低延遲、高吞吐 | 同一 AZ、同一機架  | ❌     | HPC、緊密耦合運算                        |
| **Spread**    | 隔離硬體故障   | 分散到不同硬體     | ✅     | 少量關鍵 instances（上限 7/AZ）          |
| **Partition** | 隔離機架故障   | 分散到不同機架分區 | ✅     | 大型分散式系統（HDFS、Cassandra、Kafka） |

**快速判斷**：

- 題目提 **HPC / low-latency / tightly-coupled** → **Cluster**
- 題目提 **故障隔離 / 關鍵應用 / 少量 instances** → **Spread**
- 題目提 **大數據 / 分散式資料庫 / partition-aware** → **Partition**

## Kinesis Data Streams vs Data Firehose 比較表

**關鍵**：Lambda 不能作為 Firehose 的**目的地（destination）**。

| 特性        | Kinesis Data Streams                | Data Firehose                                    |
| ----------- | ----------------------------------- | ------------------------------------------------ |
| 消費者模式  | 自己寫 consumer（Lambda、EC2、KCL） | **內建目的地**（S3、Redshift、OpenSearch、HTTP） |
| Lambda 角色 | ✅ 直接消費資料                      | ⚠️ 只能做**中間處理/轉換**，不是最終消費          |
| 即時性      | 真正即時                            | Near real-time（有 buffer）                      |
| 管理複雜度  | 高（要管 shard、consumer）          | 低（全託管）                                     |

**記憶方式**：

- **Data Streams** = 水管，你要自己接水龍頭（consumer）
- **Firehose** = 消防水管，直接噴向固定目標（S3、Redshift 等）

題目如果要 Lambda **處理並消費** Kinesis 資料 → 用 **Data Streams**，不是 Firehose。

### Data Firehose 內建目的地

| 類別     | 目的地                                                                  |
| -------- | ----------------------------------------------------------------------- |
| AWS 服務 | S3、Redshift、OpenSearch Service                                        |
| 第三方   | Splunk、Datadog、Dynatrace、LogicMonitor、MongoDB、New Relic、Snowflake |
| 通用     | 自訂 HTTP Endpoint                                                      |

S3 是最常見的，很多時候 Redshift/OpenSearch 也是先經過 S3 再載入。

### Data Streams + Lambda

```bash
資料 → Kinesis → Lambda（消費、處理、結束）
```

### Firehose + Lambda

```bash
資料 → Firehose → Lambda（轉換）→ Firehose → S3/Redshift
```

## EC2 無法連外的網路排錯步驟

| 層級 | 檢查項目             | 控制方向                          |
| ---- | -------------------- | --------------------------------- |
| 1    | **Public IP / EIP**  | 有沒有對外地址                    |
| 2    | **Route Table**      | Subnet 有沒有路由到 IGW           |
| 3    | **Internet Gateway** | VPC 有沒有掛 IGW                  |
| 4    | **Security Group**   | Inbound/Outbound 規則（Stateful） |
| 5    | **Network ACL**      | Subnet 層級規則（Stateless）      |

## 兩種 SQS Queue 比較

| 特性     | Standard Queue            | FIFO Queue                       |
| -------- | ------------------------- | -------------------------------- |
| 吞吐量   | 幾乎無限                  | 300 TPS（batching 可達 3000）    |
| 順序     | Best-effort               | 嚴格保證                         |
| 重複⭐️    | At-least-once（可能重複） | Exactly-once（不重複）           |
| 適用場景 | 高吞吐、**可容忍重複**    | 訂單、金融交易等**不可重複**場景 |

## CloudWatch Logs vs VPC Flow Logs 比較表

| Log 類型            | 記錄什麼                                       | 適用場景                |
| ------------------- | ---------------------------------------------- | ----------------------- |
| **CloudWatch Logs** | Application logs、系統日誌、任何你推送的 log   | 應用程式錯誤監控、debug |
| **VPC Flow Logs**   | 網路流量的 metadata（IP、port、accept/reject） | 網路安全分析、流量監控  |

## 為什麼 Flow Logs 不對

**Flow Logs 記錄的是這種東西**：

```bash
2 123456789012 eni-abc123 10.0.1.5 52.94.133.10 443 49152 6 25 5000 1620000000 1620000060 ACCEPT OK
```

這只是「誰連到誰、用什麼 port、有沒有被 accept」，完全不包含：

- HTTP 狀態碼
- Application error message
- Exception stack trace

**你的 application 丟出 `NullPointerException` 或 `500 Internal Server Error`，Flow Logs 根本看不到。**

## 考試判斷技巧

| 題目關鍵字                                                                         | 答案方向          |
| ---------------------------------------------------------------------------------- | ----------------- |
| 「**application error**」「**application logs**」                                  | → CloudWatch Logs |
| 「**network traffic**」「**security group**」「**accepted/rejected connections**」 | → VPC Flow Logs   |
| 「**who connected to whom**」「**traffic analysis**」                              | → VPC Flow Logs   |

## AWS 遷移工具完整比較

### 核心工具對照

| 工具                 | 搬什麼⭐️                      | 目標位置⭐️                   | 類比概念                 | 產出結果                  |
| -------------------- | ---------------------------- | --------------------------- | ------------------------ | ------------------------- |
| **AWS MGN**          | 整台 VM（OS + 應用 + 資料）⭐️ | EC2                         | VMware vMotion           | 可直接啟動的 EC2 instance |
| **AWS DMS**          | 資料庫內容                   | RDS / Aurora / EC2 上的 DB⭐️ | `pg_dump` + `pg_restore` | 遷移完成的資料庫          |
| **AWS DataSync**     | 檔案、資料夾⭐️                | S3 / EFS / FSx              | `rsync` / `robocopy`     | 同步完成的檔案            |
| **AWS Snow Family**  | 大量資料（離線）             | S3                          | 寄硬碟給 AWS             | 資料匯入 S3               |
| **VM Import/Export** | VM image（手動）             | EC2 AMI                     | 匯出 OVA 再匯入          | AMI（一次性）             |

### 依情境選擇

| 情境關鍵字                                              | 選擇                      |
| ------------------------------------------------------- | ------------------------- |
| **lift-and-shift**⭐️、migrate servers、minimize downtime | **AWS MGN**               |
| database migration、schema conversion、異質資料庫       | **AWS DMS**（可搭配 SCT） |
| transfer files、sync data to S3/EFS/FSx                 | **AWS DataSync**          |
| 網路頻寬不足、PB 級資料、離線傳輸                       | **Snow Family**           |
| 一次性 VM 匯入、可接受較長停機                          | **VM Import/Export**      |

### 持續複製能力比較

| 工具             | 持續複製？     | 停機時間           |
| ---------------- | -------------- | ------------------ |
| AWS MGN          | ✅ 持續同步差異 | 極短（分鐘級）     |
| AWS DMS          | ✅ CDC 持續複製 | 極短               |
| AWS DataSync     | ✅ 可排程同步   | 視資料量           |
| Snow Family      | ❌ 一次性       | 較長（等設備寄送） |
| VM Import/Export | ❌ 一次性       | 較長               |

## EFS vs S3 存取方式比較表

| 特性         | EFS (File Storage)                         | S3 (Object Storage)                   |
| ------------ | ------------------------------------------ | ------------------------------------- |
| **存取方式** | 像本機硬碟，mount 後直接讀寫               | 透過 HTTP API (PUT/GET)               |
| **路徑概念** | 真正的目錄階層 `/home/user/docs/file.txt`  | 扁平結構，「目錄」只是 key 的前綴假象 |
| **修改方式** | 可以改檔案的**某一部分**（partial update） | **整個物件替換**，無法只改一小段      |
| **鎖定機制** | 支援 file locking                          | 不支援（靠版本控制）                  |
| **掛載**     | 可 mount 到 EC2，像 `/mnt/efs`             | 不能 mount，只能透過 SDK/CLI          |

## 三種 Scaling Policy 比較

| Policy 類型         | 特性                                        | 適用場景             |
| ------------------- | ------------------------------------------- | -------------------- |
| **Simple Scaling**  | 觸發後執行一次，等 cooldown 結束才能再觸發  | 最基本，較少用       |
| **Step Scaling**    | 根據 alarm 超標程度，執行**不同幅度**的調整 | 需要分級反應         |
| **Target Tracking** | 自動維持某指標在目標值（如 CPU 50%）        | 最簡單，推薦優先使用 |

## Step Scaling 的兩個關鍵特性

### 1. Step Adjustments（分級調整）

根據超標程度決定擴展幅度：

```bash
CPU 60-70% → 加 1 台
CPU 70-80% → 加 2 台
CPU 80%+   → 加 4 台
```

### 2. Instance Warm-up Time

新啟動的 instance 在 warm-up 期間**不計入 scaling 指標計算**，避免還沒準備好就又觸發擴展。

## 兩者本質差異

| 特性                      | Target Tracking           | Step Scaling                               |
| ------------------------- | ------------------------- | ------------------------------------------ |
| **你設定什麼**            | 目標值（如 CPU 維持 50%） | 閾值 + 對應動作（CPU > 70% 加 2 台）       |
| **誰建 CloudWatch Alarm** | AWS 自動建立並管理        | **你自己建、自己設定**                     |
| **彈性程度**              | 低（AWS 黑盒處理）        | 高（完全自訂階梯式反應）                   |
| **類比**                  | 恆溫冷氣（設 25 度就好）  | 手動冷氣（超過 28 度開弱、超過 32 度開強） |

## 題目為何是 Step Scaling？

題目這句話是關鍵：
> **「specifying the scaling metrics and threshold values for the CloudWatch alarms」**

這明確說「你」要指定 metrics 和 threshold —— 這就是 Step Scaling 的定義。

**Target Tracking 的話**，你只會說：「我要 CPU 維持在 50%」，剩下的 AWS 全包，你根本不會去碰 CloudWatch alarm 的 threshold。

### 記法

- **Target Tracking** = 設「目標」，AWS 自己想辦法達成
- **Step Scaling** = 設「階梯規則」，你定義每個門檻該怎麼反應

看到題目強調「你要設定 threshold / alarm」→ **Step Scaling**
看到題目只提「維持某個 metric 在某個值」→ **Target Tracking**

## 對比 Cooldown vs Warm-up

| 概念         | 作用                                         |
| ------------ | -------------------------------------------- |
| **Cooldown** | 擴展動作後**暫停一段時間**，避免連續觸發     |
| **Warm-up**  | 新 instance 啟動後**不計入指標**，等它準備好 |

兩者目的都是防止 Auto Scaling 過度反應，但切入點不同。

### 時間線視角

```bash
[指標超標] → [觸發 scaling] → [新 instance 啟動] → [instance 開始處理流量]
              ↑                  ↑
              Cooldown 在這裡    Warm-up 在這裡
```

## EBS Encryption 完整涵蓋範圍

| 加密範圍        | 說明                                          |
| --------------- | --------------------------------------------- |
| Data at rest    | Volume 內的靜態資料                           |
| Data in transit | Volume ↔ EC2 Instance 之間的傳輸資料          |
| Snapshots       | 從加密 Volume 建立的快照，**自動加密**        |
| 衍生 Volumes    | 從加密 Snapshot 建立的新 Volume，**自動加密** |
| Boot Volume     | 支援加密（早期不支援，現在可以）              |

### 關鍵原則

**加密具有「傳染性」**— 一旦源頭加密，後續產物全部繼承加密狀態，無法中斷。

```bash
Encrypted Volume
    → Encrypted Snapshot
        → Encrypted Volume
```

反過來也成立：**未加密的 Volume 產生的 Snapshot 也是未加密的**，除非你在複製時明確指定加密。

## Cluster Autoscaler vs Karpenter 比較表

| 面向          | Cluster Autoscaler      | Karpenter                       |
| ------------- | ----------------------- | ------------------------------- |
| 運作方式      | 透過 Auto Scaling Group | 直接呼叫 EC2 API                |
| 回應速度      | 較慢（需等 ASG 反應）   | 較快（即時 provision）          |
| Instance 選擇 | 固定（ASG 預先定義）    | 動態（根據 pod 需求選最適合的） |
| 設定複雜度    | 需要手動調校 ASG        | 宣告式設定，較簡單              |
| **運維負擔**  | 較高                    | **較低**                        |

## Aurora Failover 機制解析

| 情境                              | Failover 行為                                         | 時間                |
| --------------------------------- | ----------------------------------------------------- | ------------------- |
| **有 Aurora Replica**             | 翻轉 CNAME 指向健康的 Replica，Replica 升級為 Primary | 約 30 秒內          |
| **沒有 Replica（單一 instance）** | 在同一 AZ 嘗試重建新的 DB instance                    | 較慢（best-effort） |

## IAM Policy 必要組成元素

IAM Policy 是 JSON 格式，**必要的 key 只有兩個**：

| Key         | 必要性   | 說明                                  |
| ----------- | -------- | ------------------------------------- |
| `Version`   | **必要** | 固定寫 `"2012-10-17"`（目前唯一版本） |
| `Statement` | **必要** | 陣列，包含一或多個權限聲明            |

---

## Statement 內的必要元素

每個 Statement 物件中，**必要的 key**：

| Key        | 必要性     | 說明                                             |
| ---------- | ---------- | ------------------------------------------------ |
| `Effect`   | **必要**   | `"Allow"` 或 `"Deny"`                            |
| `Action`   | **必要**   | 允許/拒絕的操作，如 `"s3:GetObject"`             |
| `Resource` | **視情況** | 操作的目標資源 ARN（identity-based policy 必要） |

## 最小可行 Policy 範例

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

## *V-SEA-R 口訣（依重要性排序）

- **V**ersion — 永遠是 `2012-10-17`
- **S**tatement — 權限聲明陣列
- **E**ffect — Allow / Deny
- **A**ction — 做什麼操作
- **R**esource — 對什麼資源

其他常見但**非必要**的 key：`Principal`（resource-based policy 才需要）、`Condition`（條件式授權）。

## 各元件運作層級比較表

| 層級     | 控制機制                       | 作用範圍                     |
| -------- | ------------------------------ | ---------------------------- |
| Subnet   | Network ACL                    | 控制進出該 subnet 的所有流量 |
| Instance | Security Group                 | 控制進出該 instance 的流量   |
| VPC      | Route Table + Internet Gateway | 控制路由方向，非直接過濾流量 |

## Nginx Virtual Host vs SNI 比較表

| 機制               | 作用層級          | 解決的問題                            |
| ------------------ | ----------------- | ------------------------------------- |
| Nginx Virtual Host | HTTP 層 (Layer 7) | 根據 `Host` header 路由到不同 backend |
| SNI                | TLS 層 (Layer 6)  | 根據 hostname 選擇正確的 SSL 憑證     |

### 時序對比

```bash
傳統 HTTPS（無 SNI）：
Client → TCP connect → TLS handshake（server 不知道要用哪張憑證）→ HTTP request（才看到 Host header）

有 SNI：
Client → TCP connect → TLS handshake + hostname → server 選對憑證 → HTTP request
```

## Security Group vs NACL 來源/目標表示法比較表

| 來源/目標表示法              | Security Group               | NACL            |
| ---------------------------- | ---------------------------- | --------------- |
| **CIDR block**               | ✅ `10.0.1.0/24`              | ✅ `10.0.1.0/24` |
| **單一 IP**                  | ✅ `10.0.1.5/32`              | ✅ `10.0.1.5/32` |
| **另一個 Security Group ID** | ✅ `sg-0abc1234`              | ❌ 不支援        |
| **Prefix List**              | ✅ `pl-xxx`（如 S3 endpoint） | ❌ 不支援        |

你的筆記完全正確，我來補充一個關鍵觀念：

## DynamoDB Provisioned + Auto Scaling vs On-Demand

PS：On-Demand 大約貴 2.5 到 3 倍

| 特性           | Provisioned + Auto Scaling | On-Demand                |
| -------------- | -------------------------- | ------------------------ |
| **擴展速度**   | 分鐘級（需要時間調整）     | 即時（毫秒級回應）       |
| **擴展上限**   | 受限於設定的 max RCU/WCU   | 幾乎無上限（帳號配額內） |
| **Spike 處理** | ❌ 來不及擴展會被 throttle  | ✅ 瞬間流量也能吸收       |
| **成本**       | 較低（可預測基準線）       | 較高（但省心）           |
| **適用場景**   | 流量可預測、有規律波動     | 流量不可預測、突發性高   |

**考試關鍵判斷**：

- 題目說「unpredictable traffic」「sudden spikes」→ **On-Demand**
- 題目說「predictable workload」「cost optimization」→ **Provisioned + Auto Scaling**

Provisioned 的 Auto Scaling 本質上還是「**事後反應**」，它看到 CloudWatch metrics 超標才開始調整；On-Demand 則是「**隨到隨服務**」，根本不存在調整延遲的問題。

## Direct Connect 本身 vs. VIF 的差異

**Direct Connect (DX)** 確實是私有專線——它是你的 data center 到 AWS 的**實體連線**，不走公網。

但這條私有專線上，你要**存取什麼東西**，由 VIF 類型決定：

| VIF 類型        | 存取目標                                     | 流量路徑                  |
| --------------- | -------------------------------------------- | ------------------------- |
| **Private VIF** | VPC 內的私有資源（EC2、RDS 等）              | DX → VPC（透過 VGW/DXGW） |
| **Public VIF**  | AWS 公開服務端點（S3、DynamoDB、EC2 API 等） | DX → AWS 公開服務區域     |
| **Transit VIF** | 透過 Transit Gateway 連多個 VPC              | DX → TGW → 多個 VPC       |

## 關鍵理解

**Public VIF 的「Public」不是指走公網**，而是指存取的目標是 AWS 的 public endpoints。⭐️

舉例：S3 的 endpoint `s3.amazonaws.com` 是公開的，但透過 **DX + Public VIF** 存取時：

- 流量走的是你的**私有專線**⭐️
- 不經過 Internet
- 延遲更低、更穩定

這跟直接從 Internet 存取 S3 完全不同。

## 考試重點

題目說要從 on-premises「私有地」存取 S3，有兩種常見解法：

1. **DX + Public VIF** — 走專線到 S3 public endpoint
2. **DX + Private VIF + VPC Endpoint (Gateway)** — 走專線進 VPC，再透過 VPC endpoint 到 S3

## AWS 網路元件關聯層級比較表

| 元件                  | 關聯層級                     | 功能              |
| --------------------- | ---------------------------- | ----------------- |
| **IGW / NAT Gateway** | VPC（但需 Route Table 指向） | 出入口            |
| **Route Table**       | Subnet                       | 路由方向          |
| **NACL**              | Subnet                       | 過濾（Stateless） |
| **Security Group**    | Instance (ENI)               | 過濾（Stateful）  |

## AWS VPC CIDR 限制

| 項目     | 限制                |
| -------- | ------------------- |
| 最大     | /16（65,536 個 IP） |
| 最小     | /28（16 個 IP）     |
| 必須使用 | 私有 IP 範圍        |

> **考試重點**：VPC CIDR 必須在 `/16` 到 `/28` 之間

### 為什麼最小是 /28？

- /28 = 16 個 IP
- AWS 保留 5 個 IP
- **實際可用 = 11 個 IP**
- 這是 AWS 認為「能正常運作的最小網路」

## S3 進階功能對應表

| 考題需求                 | 選用服務                                   |
| ------------------------ | ------------------------------------------ |
| 分析所有 S3 儲存趨勢     | S3 Storage Lens                            |
| 單一 bucket 存取模式分析 | S3 Analytics                               |
| 臨時授權下載檔案         | Pre-Signed URL                             |
| 記錄誰存取了 bucket      | S3 Access Logs                             |
| 上傳後自動執行程式       | S3 + Lambda                                |
| 多部門存取共用資料       | S3 Access Point                            |
| 防止誤刪除物件           | Object Lock (Governance Mode / Legal Hold) |
