# Table Summary Notes

## AWS 服務的 Caching 機制

| 服務        | Caching 機制                                                 |
| ----------- | ------------------------------------------------------------ |
| API Gateway | 可啟用 stage-level caching，暫存 API 回應內容（TTL 可調整）⭐️ |
| DynamoDB    | 有 DAX (DynamoDB Accelerator)，提供 in-memory cache          |
| CloudFront  | 本質就是 CDN，核心功能就是 edge caching                      |

## RDS Read Replica 按位置分類

| 位置              | 用途                        | 說明                                 |
| ----------------- | --------------------------- | ------------------------------------ |
| 同 Region 不同 AZ | ✅ 分散讀取 + 手動故障恢復   | 標準做法，AZ 掛了可手動提升為 Master |
| 同 AZ             | ⚠️ 分散讀取（不建議）        | 無 AZ 容錯，實務上不使用             |
| 跨 Region         | ✅ 分散讀取 + 跨區域災難恢復 | 全球化應用、Region 級災難恢復        |

**重點**：Read Replica 不提供自動故障轉移，真正的自動容錯用 **Multi-AZ**

## Read Replica vs Multi-AZ

| 功能     | Read Replica           | Multi-AZ                     |
| -------- | ---------------------- | ---------------------------- |
| 目的     | 讀取分流、擴展效能     | 高可用性、自動容錯           |
| 是否可讀 | ✅ 可以讀取             | ❌ Standby 不可讀（同步備援） |
| 故障切換 | ❌ 需手動提升為主庫     | ✅ 自動切換，無需人工介入     |
| 延遲     | 非同步複製（秒級延遲） | 同步複製（零資料遺失）       |

## RDS 備份保留期限

| 備份類型 | 保留期限   | 用途                             |
| -------- | ---------- | -------------------------------- |
| 自動備份 | 最多 35 天 | 短期恢復、Point-in-Time Recovery |
| 手動快照 | 無限期保留 | 長期保存、合規需求、重大版本備份 |

## 儲存服務對比 (EBS、EFS、S3)

| 服務 | 連接方式    | 適用場景                       | 考試重點                                  |
| ---- | ----------- | ------------------------------ | ----------------------------------------- |
| EBS  | 單 EC2 獨佔 | 1 個 instance 的本地高效存儲   | Block storage、低延遲                     |
| EFS  | 多 EC2 共享 | 1000+ instances 共用同份數據   | POSIX filesystem、concurrently accessible |
| S3   | 應用層連接  | 物件存儲、web accessible、歸檔 | Eventually consistent、web-based          |

## EBS Multi-Attach 與標準 EBS 比較

| 特性       | 標準 EBS | EBS Multi-Attach           |
| ---------- | -------- | -------------------------- |
| 附加實例數 | 1 個     | 最多 16 個                 |
| 可用區域   | 單一 AZ  | 單一 AZ                    |
| 適用場景   | 普通應用 | 高可用叢集 (如 Oracle RAC) |
| 考試出現率 | 常見     | 罕見                       |

## EFS Performance Mode 選擇比較

| 特性         | General Purpose | Max I/O                   |
| ------------ | --------------- | ------------------------- |
| **適用場景** | 一般網站、CMS   | **大量併發存取**          |
| **延遲**     | 較低（ms）⭐️     | 較高（可接受）            |
| **併發連線** | 適中            | **無限制**                |
| **判斷關鍵** | 少量 tasks      | **數百個 tasks 同時存取** |

**記憶口訣**：

- 題目出現「hundreds of tasks」→ Max I/O
- 題目強調「low latency」→ General Purpose

## 三種 ELB 健康檢查對比

| ELB 類型 | 支援的健康檢查   | 使用層級         |
| :------- | :--------------- | :--------------- |
| ALB      | HTTP、HTTPS      | Layer 7 (應用層) |
| NLB      | TCP、HTTP、HTTPS | Layer 4 (傳輸層) |
| CLB      | TCP、HTTP、HTTPS | Layer 4 + 7      |

記法：**健康檢查**跟**流量轉發**是兩回事，NLB 可以「L4 轉發 + L7 檢查」。

## 考試判斷公式：題目關鍵字對應解答

| 關鍵字                    | 正確答案                |
| :------------------------ | :---------------------- |
| 限制請求頻率 / Rate limit | **WAF Rate-Based Rule** |
| 封鎖特定 IP/地區          | WAF Regular Rule        |
| Layer 4 DDoS 防護         | **AWS Shield**          |
| 整合 ALB ⭐️                | 用 WAF（可直接關聯）    |
| 整合 CloudFront ⭐️         | 用 WAF（可直接關聯）    |

## ALB vs NLB

| 特性        | ALB          | NLB            |
| ----------- | ------------ | -------------- |
| OSI Layer   | Layer 7      | Layer 4        |
| 適用協定    | HTTP/HTTPS   | TCP/UDP/TLS    |
| 路由能力    | **內容路由** | 無 (只看 port) |
| 效能        | 高           | **極高**       |
| 靜態 IP     | ❌            | ✅              |
| WebSocket   | ✅            | ✅              |
| Lambda 支援 | ✅            | ❌              |
| 價格        | 中           | 低             |

**判斷標準**:

- 題目提到「**根據 URL path 路由**」→ ALB
- 題目提到「**需要靜態 IP**」或「**極致效能**」→ NLB
- 題目提到「**WebSocket**」→ ALB 或 NLB 都可以，但通常選 ALB

## WAF 支援的服務

| 服務            | 用途       | 典型場景                   |
| --------------- | ---------- | -------------------------- |
| **CloudFront**  | CDN        | 保護靜態網站、全球分發內容 |
| **ALB**         | 負載均衡器 | 保護後端 API/應用程式      |
| **API Gateway** | API 管理   | 保護 RESTful/WebSocket API |

WAF 只能掛 ALB 是因為它需要檢查 HTTP/HTTPS 內容（Layer 7），而 NLB 工作在 Layer 4，根本看不到 HTTP 內容。

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

| 服務               | 防護層級         | 用途                   | 能否阻擋 HTTP 內容？       |
| :----------------- | :--------------- | :--------------------- | :------------------------- |
| **Security Group** | Layer 4 (傳輸層) | 控制 EC2 的進出流量    | ❌ 只能看 IP + Port         |
| **NACL**           | Layer 4 (傳輸層) | 控制 Subnet 的進出流量 | ❌ 只能看 IP + Port         |
| **AWS WAF**        | Layer 7 (應用層) | 檢查 HTTP/HTTPS 內容   | ✅ 可以看 URL、Header、Body |
| **AWS Shield**     | Layer 3/4        | 自動防 DDoS            | ❌ 不看內容，只擋大流量     |

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
| **Config Rules**  | 偵測達規（檢查 SG 是否開 SSH） | ✅ 偵測問題        |
| **Remediations**  | 自動修復達規組態               | ✅ 自動重新組態 SG |
| **Notifications** | 發送通知                       | ❌ 只通知，不修復  |

**題目關鍵字**：「automatically re-configure」= 自動修復 = **Remediations**

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
| **成本**     | 免費                      | 按小時+流量收費           |

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
| **評估順序** | 全部規則都看            | 依編號順序，碰到就停       |

**記憶點**：

- SG = 保鑣（認得你，回來自動放行，stateful）
- NACL = 閘門（進出都要驗證，stateless）

**考試陷阱**：

- 看到「需要 deny 特定 IP」→ NACL（SG 沒有 deny）
- 看到「stateless」→ NACL
- 大部分情況用 SG 就夠了，NACL 是額外防護層

## RDS Multi-AZ 核心重點解析

| 特性             | 說明                                                  | 考試常見陷阱                         |
| ---------------- | ----------------------------------------------------- | ------------------------------------ |
| **同步複製**     | Primary → Standby 是 synchronous replication          | 不是 async，這保證資料零遺失         |
| **自動容錯轉移** | Primary 掛了，RDS 自動切換到 Standby（通常 1-2 分鐘） | Standby 平常**不能讀不能寫**，純備援 |
| **跨 AZ 部署**   | Primary 和 Standby 在不同 AZ                          | 單一 AZ 掛掉不影響服務               |

## 實務理解

把 Multi-AZ 想成你部署後端服務時的 **Active-Passive HA 架構**：

- Primary DB = Active node（處理所有請求）
- Standby DB = Passive node（隨時準備接手，但平常不工作）
- 當 Primary 掛了，DNS endpoint 自動指向 Standby

## 考試必考場景

**Q: 如何同時達成高可用性 + 讀取效能提升？**
**A: Multi-AZ (HA) + Read Replicas (效能)**

Multi-AZ 解決的是「服務不中斷」，想分散讀取流量要用 Read Replicas。

![圖 0](https://img.kyomind.tw/img-20251020-114730.png)

## Multi-AZ vs Read Replicas 複製機制

| 項目               | Multi-AZ          | Read Replicas    |
| ------------------ | ----------------- | ---------------- |
| **複製方式**       | Synchronous       | Asynchronous     |
| **用途**           | High Availability | Read scalability |
| **Failover**       | 自動容錯轉移      | 需手動 promote   |
| **Standby 可讀嗎** | ❌ 不能讀寫        | ✅ 可讀取         |

## 考試記憶點

**Synchronous = Multi-AZ = HA**
**Asynchronous = Read Replicas = 效能**

Read Replicas 有 replication lag（**複製延遲**），不適合做 HA，適合分散讀取流量。

## SNS 訂閱端點類型(推送的目標)

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
| 實作方式 | URL 帶參數      | Cookie 存權限      |
| 用戶體驗 | 每次要新 URL    | 一次驗證，多次使用 |

## CloudFront 內容存取控制機制

| 機制                            | 用途                                      | 考試重點                                |
| ------------------------------- | ----------------------------------------- | --------------------------------------- |
| **Signed URLs/Cookies**         | 控制內容存取權限                          | **付費內容、時效性限制首選**            |
| **OAC (Origin Access Control)** | 強制用戶只能透過 CloudFront 存取 **S3** ⭐️ | **防止繞過 CloudFront 直接打 S3 URL**   |
| **S3 Pre-signed URLs**          | 讓用戶上傳檔案到 S3，但不需要 AWS 憑證    | **注意：這是 S3 功能，不是 CloudFront** |

S3 Pre-Signed URL → 讓用戶「**上傳/下載**」但不給 AWS 憑證

## VPC Endpoint 完整對比

| 特性         | Gateway Endpoint     | Interface Endpoint |
| ------------ | -------------------- | ------------------ |
| **支援服務** | **僅 S3 + DynamoDB** | 幾乎所有 AWS 服務  |
| **費用**     | **免費 ⭐️**           | 按小時 + 流量收費  |
| **實作方式** | **修改 Route Table** | 建立 ENI (私有 IP) |
| **配置層級** | Route Table 層級     | Subnet 層級        |
| **安全組**   | **不需要**           | 需要               |
| **跨 VPC**   | **僅限同 VPC** ⭐️     | 可透過 PrivateLink |
| **考試判斷** | **S3/DynamoDB 首選** | 其他服務才考慮     |

## 考試記憶要點

看到 S3 或 DynamoDB 私有連線 → 直接選 Gateway Endpoint

理由：

1. 免費（成本最佳化）
2. 配置簡單（改 route table 就好）
3. AWS 官方推薦做法

其他服務需要私有連線 → Interface Endpoint

## 記憶技巧：VPC Endpoint 的考試秘訣

| 記憶方式           | 內容                                                                  |
| ------------------ | --------------------------------------------------------------------- |
| **Gateway 簡記**   | **「S3 & DynamoDB = 免費」** ← 考試看到 S3 或 DynamoDB 優先選 Gateway |
| **Interface 簡記** | **「其他服務 = 要錢但更靈活」** ← 需要其他服務時自動選 Interface      |
| **成本優化秘訣**   | 看到「cost-effective」+ 「S3/DynamoDB」→ **直接選 Gateway**           |

## SNS vs SQS

| 特性         | **SNS**                                          | **SQS**                  |
| ------------ | ------------------------------------------------ | ------------------------ |
| **模式**     | **Pub/Sub（1 對多）⭐️**                           | Queue（1 對 1）          |
| **訊息儲存** | **不儲存**                                       | 儲存最多 14 天           |
| **用途**     | **廣播通知**                                     | 工作佇列                 |
| **即時性**   | **即時推送給所有訂閱者**（離線訂閱者會遺失訊息） | 非同步，訊息保留到被消費 |

**核心考點**：SNS 最重要的特性是 **即時性 + 不儲存訊息**，訂閱者離線會遺失訊息

## Shield Standard vs Shield Advanced

| 功能               | **Shield Standard** | **Shield Advanced**                |
| ------------------ | ------------------- | ---------------------------------- |
| 費用               | 免費                | **$3000/月 起**                    |
| 防護層級           | Layer 3/4（網路層） | **Layer 3/4/7**（含應用層）        |
| 防護對象           | 所有 AWS 資源       | **EC2、ELB、CloudFront、Route 53** |
| **DDoS 成本保護**  | ❌                   | ✅ **防止流量攻擊產生的額外費用**   |
| **WAF 整合**       | ❌                   | ✅ **免費使用**                     |
| **24/7 DRT 支援**  | ❌                   | ✅                                  |
| **即時攻擊可視化** | ❌                   | ✅                                  |

## IPv4 vs IPv6 出站連線與 Gateway 方案

| 特性         | IPv4             | IPv6                |
| ------------ | ---------------- | ------------------- |
| **對外連線** | **NAT Gateway**  | **Egress-only IGW** |
| **雙向連線** | Internet Gateway | Internet Gateway    |
| **私有 IP**  | ✅ 有             | ❌（都是公開 IP）    |

## Blue-Green vs Canary 的關鍵差異

| 比較項目     | Blue-Green                           | Canary                       |
| ------------ | ------------------------------------ | ---------------------------- |
| **環境數量** | 需要兩套完整環境                     | 單一環境，部分流量導向新版本 |
| **成本**     | 高（雙倍資源）                       | 低（共用資源）               |
| **切換方式** | 一次性全切                           | 漸進式調整流量比例           |
| **風險控制** | 可快速回滾，但出問題影響 100% 使用者 | 出問題只影響小部分使用者     |

## 標準 KMS 與 Custom Key Store 的差異

| 項目         | 標準 KMS           | Custom Key Store          |
| ------------ | ------------------ | ------------------------- |
| 金鑰材料位置 | **AWS 管理的 HSM** | **你的 CloudHSM**         |
| 實體控制權   | **AWS**            | **你**                    |
| 複雜度       | **低**             | **高**（需管理 CloudHSM） |
| 成本         | **低**             | **高**（CloudHSM 費用）   |

## CloudHSM = 硬體加密模組

**HSM (Hardware Security Module)**：專門用來保管加密金鑰的實體硬體設備

## CloudHSM vs KMS

| 特性         | KMS              | CloudHSM            |
| ------------ | ---------------- | ------------------- |
| **控制權**   | AWS 管理         | **你完全控制**      |
| **合規**     | 一般用途         | **金融/政府高規格** |
| **金鑰存取** | AWS 可協助       | **只有你能存取**    |
| **價格**     | 便宜             | **貴（$1+/小時）**  |
| **整合**     | AWS 服務原生支援 | 需自行整合          |

## 考試判斷

- 題目提到 **FIPS 140-2 Level 3**、**單租戶**、**完全控制金鑰** → CloudHSM
- 一般加密需求 → KMS

**白話說**：CloudHSM 是給銀行、政府這種「連 AWS 都不能信任」的客戶用的。99% 的情況用 KMS 就夠了。

## KMS Key 類型速查表

| 類型                        | 誰能管理 Key Policy | 能否設定 Rotation | 能否查看 CloudTrail | 金鑰材料存放位置  | 獨立審計 FIPS 140-2 Level 3 |
| --------------------------- | ------------------- | ----------------- | ------------------- | ----------------- | --------------------------- |
| **AWS Owned**               | AWS（你看不到）     | AWS 自動處理      | ✗ 看不到            | AWS HSM           | ✗                           |
| **AWS Managed**             | AWS（你只能看）     | ✓ 自動每年        | ✓                   | AWS HSM           | ✗                           |
| **Customer Managed (標準)** | **你**              | ✓ 可選自動/手動   | ✓                   | AWS HSM           | ✗                           |
| **Custom Key Store**        | **你**              | ✓ 可選自動/手動   | ✓                   | **你的 CloudHSM** | **✓**                       |

### 關鍵判斷點

- AWS Owned vs AWS Managed：前者你完全看不到、後者你能看但不能改
- AWS Managed vs Customer Managed：前者 AWS 幫你管、後者你自己管（考試愛考這個）
- Customer Managed vs Custom Key Store：後者是你要「完全掌控硬體」+ 獨立審計需求時才用

### 考試出題邏輯

- 題目提到 "full control" → Customer Managed
- 題目提到 "compliance" + "dedicated HSM" → Custom Key Store
- 題目只說要 "encryption" 沒特別要求 → AWS Managed 最省事

## Aurora Endpoint 類型對比

| Endpoint             | 用途     | 指向                                    |
| -------------------- | -------- | --------------------------------------- |
| **Cluster Endpoint** | 寫入操作 | Primary instance (DDL/DML)              |
| **Reader Endpoint**  | 讀取操作 | 自動 load balance 到最多 15 個 replicas |
| **Custom Endpoint**  | 特定需求 | 你自訂的 instance 群組                  |

## VPN 連接方案對比

| 選項               | 為何不對                                        |
| ------------------ | ----------------------------------------------- |
| **VPC Peering**    | 只能連 VPC 對 VPC，**不能連 on-premises**       |
| **Direct Connect** | 專線連接，不走公網，且貴（**不適合當 backup**） |
| **PrivateLink**    | 用於服務存取（如連 S3、API），**不是連機房**    |

## S3 Object Lock 模式對比

| 項目     | **Governance Mode**    | **Compliance Mode**            |
| -------- | ---------------------- | ------------------------------ |
| 保護程度 | 軟性保護               | **硬性保護**                   |
| 誰能繞過 | 有特殊權限的人可以解除 | **誰都不能動，包括 root user** |
| 適用場景 | 一般資料保護           | **法規遵循、audit 要求**       |

## S3 Object Lock：Retention Period vs Legal Hold

| 特性     | **Retention Period** | **Legal Hold**   |
| -------- | -------------------- | ---------------- |
| 運作方式 | **設定時間範圍**     | **無限期鎖定**   |
| 解除條件 | **時間到就解除保護** | **必須手動解除** |

## RAM vs Organizations 比較

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

## RDS 讀取複本與高可用方案選擇

| 選項                    | 說明                       | 何時選                       |
| ----------------------- | -------------------------- | ---------------------------- |
| **Aurora Auto Scaling** | 自動調整 read replica 數量 | 讀取流量波動 + 要省錢        |
| **Provisioned IOPS**    | 固定的高 I/O 性能          | 需要穩定的高性能，不在意成本 |
| **Multi-AZ**            | 高可用性備援               | 關注災難恢復，非性能擴展     |
| **Read Replica (手動)** | 固定數量的副本             | 流量穩定，不需動態調整       |

## EBS vs FSx for Windows

| 特性     | **EBS**                  | **FSx for Windows**    |
| -------- | ------------------------ | ---------------------- |
| 儲存類型 | 區塊儲存 (Block Storage) | 檔案系統 (File System) |
| 掛載方式 | 只能掛給單一 EC2         | 多台機器同時存取       |
| 協定支援 | 無 SMB                   | 原生支援 SMB           |
| 使用場景 | EC2 的硬碟               | 共享檔案伺服器         |

## Amazon DLM vs AWS Backup

| 比較項目 | **Amazon DLM**            | **AWS Backup**                     |
| -------- | ------------------------- | ---------------------------------- |
| 定位     | **EBS snapshot 專用工具** | **跨服務集中式備份平台**           |
| 適用範圍 | **只管 EBS**              | **EBS、RDS、DynamoDB、EFS、S3...** |
| 複雜度   | 簡單直接                  | 功能完整但設定較複雜               |
| 成本     | 免費（只付儲存）          | 免費（只付儲存）                   |
| 考試答案 | **單純 EBS 問題選這個**   | **跨服務備份選這個**               |

## 安全防護需求與解決方案對應表

| 需求                       | 選擇方案             |
| -------------------------- | -------------------- |
| **DDoS 防護 (Layer 3/4)**  | **AWS Shield + NLB** |
| **Web 攻擊防護 (Layer 7)** | **WAF + ALB**        |
| **SQL Injection 防護**     | **WAF + ALB**        |
| **Rate Limiting (IP)**     | **WAF + ALB**        |

**判斷標準**：題目提到「SQL Injection」、「XSS」、「bot protection」、「web application firewall」→ 必定是 **ALB + WAF** (因為 WAF **只能**掛 ALB)

## 常見 SSM 使用情境對應表

| 情境                          | 正確答案        | 常見錯誤                  |
| ----------------------------- | --------------- | ------------------------- |
| 需要遠端連線 EC2 但不想開 SSH | Session Manager | 開 port 22 + Bastion Host |
| 存應用配置（如 DB 連線）      | Parameter Store | 寫在程式碼裡              |
| 需要自動輪替密碼              | Secrets Manager | Parameter Store           |
| 管理混合環境（AWS + 本地）    | SSM + SSM Agent | 只想到 AWS 內部           |

## AWS 成本管理工具對比

| 工具                       | 用途                    | 記憶點         |
| -------------------------- | ----------------------- | -------------- |
| **Cost Explorer**          | **分析歷史 + 預測未來** | 看圖表、找趨勢 |
| **AWS Budgets**            | **設定預算 + 超標告警** | 主動通知       |
| **Cost Anomaly Detection** | **AI 自動偵測異常花費** | 機器學習找異常 |
| **Cost and Usage Report**  | **詳細原始數據匯出**    | 給 BI 工具用   |
| **Billing Dashboard**      | **當月帳單總覽**        | 只看總數       |

## AWS Glue vs Amazon EMR

| 特性     | **AWS Glue**             | **Amazon EMR**                   |
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
| **SSE-KMS** | AWS KMS 管理   | **需要金鑰審計日誌、CloudTrail、金鑰輪替**       | CloudTrail |
| **SSE-C**   | 你自己管理金鑰 | **完全控制金鑰，但你要保管、每次請求都要帶金鑰** | 客戶自管   |

## VPC Peering vs Transit Gateway vs VPN 比較

| 特性           | **VPC Peering**           | **Transit Gateway** | **VPN**                |
| -------------- | ------------------------- | ------------------- | ---------------------- |
| **轉送能力**   | ❌ 不支援 (non-transitive) | ✅ 支援多 VPC 互通   | ✅ 可與 On-Premise 連線 |
| **CIDR 限制**  | 不能重疊                  | 不能重疊            | 可透過 NAT 解決        |
| **跨區域支援** | ✅ 支援，會收費            | ✅ 支援，集中管理    | ✅ 支援，但效能較低     |
| **適合場景**   | 少量 VPC 互聯             | 大規模多 VPC 架構   | 與 On-Premise 或其他雲 |

考試記法：

- **VPC Peering** → **一對一、無轉送、CIDR 不重疊**。
- **Transit Gateway** → **Hub-and-Spoke、大規模、多 VPC 可轉送**。
- **VPN** → **跨雲或 On-Premise，經公網加密，效能較差**。

## AWS 安全與威脅偵測服務

| 服務        | 用途                                          |
| ----------- | --------------------------------------------- |
| Rekognition | **圖像/影片內容分析**（臉部、物件、不當內容） |
| Macie       | **S3 敏感資料偵測**（PII、機密資料）          |
| GuardDuty   | **威脅偵測**（安全異常行為）                  |

## VPC 網路連通性問題的排查順序

| 類型     | Security Group           | NACL                    |
| -------- | ------------------------ | ----------------------- |
| 作用層級 | Instance                 | Subnet                  |
| 狀態     | Stateful（回傳自動允許） | Stateless（需雙向規則） |
| 預設行為 | Deny all inbound         | Allow all               |
| 優先檢查 | ✓ 先查這個               | 再查這個                |

題目提「兩個 subnet 之間通訊」→ 兩者都要檢查。

## AWS Code 系列服務對比

| 服務             | 用途                                           | 類比 / 備註                                     |
| ---------------- | ---------------------------------------------- | ----------------------------------------------- |
| **CodeCommit**   | 原始碼管理（Git repository）                   | 類似 GitHub、GitLab                             |
| **CodeBuild**    | 程式建置、執行測試                             | 類似 Jenkins build job、GitHub Actions          |
| **CodeDeploy**   | 自動部署到 EC2、Lambda、ECS                    | 類似 Ansible、Octopus Deploy                    |
| **CodePipeline** | CI/CD 流程編排（串起 Commit → Build → Deploy） | 類似 GitHub Actions workflow / Jenkins pipeline |
| **CodeArtifact** | 套件管理（npm、maven、pip repository）         | 類似 Artifactory、Nexus                         |
| **CodeStar**     | CI/CD 專案管理入口（整合上述所有服務）         | 現在用得少，多數用 CodePipeline 直接整合        |
