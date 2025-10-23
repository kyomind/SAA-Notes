# Table Summary Notes

## AWS 服務的 Caching 機制

| 服務        | Caching 機制                                                |
| ----------- | ----------------------------------------------------------- |
| API Gateway | 可啟用 stage-level caching，暫存 API 回應內容（TTL 可調整） |
| DynamoDB    | 有 DAX (DynamoDB Accelerator)，提供 in-memory cache         |
| CloudFront  | 本質就是 CDN，核心功能就是 edge caching                     |

## Gateway Endpoint vs Interface Endpoint

| 特性     | Gateway Endpoint | Interface Endpoint    |
| -------- | ---------------- | --------------------- |
| 支援服務 | S3、DynamoDB     | 其他 AWS 服務         |
| 費用     | 免費             | 按小時 + 流量收費     |
| 實作方式 | 修改 route table | 建立 ENI (private IP) |
| 使用場景 | 考試首選         | 需要 PrivateLink 時   |

## RDS Read Replica 按位置分類

| 位置              | 用途                        | 說明                                 |
| ----------------- | --------------------------- | ------------------------------------ |
| 同 Region 不同 AZ | ✅ 分散讀取 + 災難備援       | 主要用途，既能分流讀取又能跨 AZ 容錯 |
| 同 AZ             | ✅ 分散讀取                  | 單純分流讀取，但沒有 AZ 層級容錯     |
| 跨 Region         | ✅ 分散讀取 + 跨區域災難備援 | 全球化應用、異地備援                 |

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
| 可用區域   | 單一 AZ  | 同一個 AZ                  |
| 適用場景   | 普通應用 | 高可用叢集 (如 Oracle RAC) |
| 考試出現率 | 常見     | 罕見                       |

## EFS Performance Mode 選擇比較

| 特性         | General Purpose | Max I/O                   |
| ------------ | --------------- | ------------------------- |
| **適用場景** | 一般網站、CMS   | **大量併發存取**          |
| **延遲**     | 較低（ms）      | 較高（可接受）            |
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

## 考試判斷公式：題目關鍵字對應解答

| 關鍵字                    | 正確答案                |
| :------------------------ | :---------------------- |
| 限制請求頻率 / Rate limit | **WAF Rate-Based Rule** |
| 封鎖特定 IP/地區          | WAF Regular Rule        |
| Layer 4 DDoS 防護         | **AWS Shield**          |
| 整合 ALB                  | 用 WAF（可直接關聯）    |
| 整合 CloudFront           | 用 WAF（可直接關聯）    |

## 考試陷阱題:ALB vs NLB vs CLB

| 特性        | ALB          | NLB            | CLB (舊版)     |
| ----------- | ------------ | -------------- | -------------- |
| OSI Layer   | Layer 7      | Layer 4        | Layer 4/7      |
| 適用協定    | HTTP/HTTPS   | TCP/UDP/TLS    | HTTP/HTTPS/TCP |
| 路由能力    | **內容路由** | 無 (只看 port) | 基本           |
| 效能        | 高           | **極高**       | 中             |
| 靜態 IP     | ❌            | ✅              | ❌              |
| WebSocket   | ✅            | ✅              | ✅              |
| Lambda 支援 | ✅            | ❌              | ❌              |
| 價格        | 中           | 低             | 低             |

**判斷標準**:

- 題目提到「**根據 URL path 路由**」→ ALB
- 題目提到「**需要靜態 IP**」或「**極致效能**」→ NLB
- 題目提到「**WebSocket**」→ ALB 或 NLB 都可以,但通常選 ALB

## WAF 支援的服務

| 服務            | 用途       | 典型場景                   |
| --------------- | ---------- | -------------------------- |
| **CloudFront**  | CDN        | 保護靜態網站、全球分發內容 |
| **ALB**         | 負載均衡器 | 保護後端 API/應用程式      |
| **API Gateway** | API 管理   | 保護 RESTful/WebSocket API |

## WAF 規則類型與用途

| 規則類型               | 範例                   | 實務用途     |
| :--------------------- | :--------------------- | :----------- |
| **IP 白/黑名單**       | 阻擋來自中國的 IP      | 地理封鎖     |
| **Query String**       | `?admin=true`          | 防止參數注入 |
| **HTTP Header**        | `User-Agent: BadBot`   | 阻擋爬蟲     |
| **Request Body**       | 包含 `<script>`        | 防 XSS 攻擊  |
| **Rate Limiting**      | 單 IP 每分鐘 > 1000 次 | 防 DDoS      |
| **SQL Injection 規則** | 自動偵測 SQL 注入模式  | 防資料庫攻擊 |

## 安全服務防護層級對比

| 服務               | 防護層級         | 用途                   | 能否阻擋 HTTP 內容？       |
| :----------------- | :--------------- | :--------------------- | :------------------------- |
| **Security Group** | Layer 4 (傳輸層) | 控制 EC2 的進出流量    | ❌ 只能看 IP + Port         |
| **NACL**           | Layer 4 (傳輸層) | 控制 Subnet 的進出流量 | ❌ 只能看 IP + Port         |
| **AWS WAF**        | Layer 7 (應用層) | 檢查 HTTP/HTTPS 內容   | ✅ 可以看 URL、Header、Body |
| **AWS Shield**     | Layer 3/4        | 自動防 DDoS            | ❌ 不看內容，只擋大流量     |

## WAF 題目關鍵字對應

| 關鍵字                 | 答案                                      |
| :--------------------- | :---------------------------------------- |
| 「阻擋特定國家」       | ✅ WAF Geo Match                           |
| 「防止 SQL Injection」 | ✅ WAF Managed Rules                       |
| 「限制請求頻率」       | ✅ WAF Rate Limiting                       |
| 「只允許公司 IP 存取」 | ❌ Security Group (不用 WAF)               |
| 「防 DDoS 攻擊」       | 看情況：Layer 7 → WAF，Layer 3/4 → Shield |

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
- NAT = 單向閥門（只能出去下載更新、呼叫 API）

---

## Security Group vs NACL

|              | Security Group          | NACL                       |
| ------------ | ----------------------- | -------------------------- |
| **層級**     | Instance（EC2、RDS 等） | Subnet                     |
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

## SNS 訂閱端點類型

| 訂閱者類型           | 使用場景                       |
| -------------------- | ------------------------------ |
| **SQS Queue**        | 解耦非同步處理，確保訊息不遺失 |
| **Lambda**           | 觸發無伺服器函數，自動執行邏輯 |
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
