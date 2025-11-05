# Speed Notes for SAA

1. ★ AWS Lake Formation 核心功能與權限管理
   1. **整合服務**：與 AWS Glue 整合，建立資料目錄描述可用資料集及其業務應用
   2. **存取控制**：定義策略在細粒度級別控制資料存取（grant and revoke permissions）
   3. **權限配置**：支援 IAM users、roles、groups 及聯邦式 Active Directory users
   4. **權限指派方式**：針對目錄物件（如 tables、columns）而非 buckets 和 objects
2. 😇 FSx for Windows File Server = AWS 上的 **Windows 檔案伺服器**
   1. **特性**：完全託管的 **Windows 原生檔案系統**，支援 **SMB 協定**（Windows 的檔案共享協定）
   2. **存取方式**：**跨平台存取**，**Windows、Linux、macOS** 都能用
3. ★★ Amazon Data Lifecycle Manager (DLM) = **EBS Snapshot 生命週期管理工具**
   1. **功能**：自動按排程建立、保留、刪除 EBS snapshot
   2. **應用場景**：題目明確說 **EBS snapshot** 時，DLM 是標準答案（相比 AWS Backup）
4. 😇 AWS Storage Gateway 存取範圍限制
   1. **用途**：僅用於備份**本地伺服器**資料，**不支援存取 Amazon VPC 內的資源**⭐️
5. ★★ NLB (Network Load Balancer) - Layer 4 高效能負載均衡
   1. **工作方式**：只看 **IP + Port**，直接轉發 TCP/UDP 封包
   2. **架構設計**：每個 AZ 配置一個固定的**網路介面（ENI）**，ENI 可綁定**靜態 IP**⭐️
   3. **本質**：像一個「高效能的網路交換器」，IP 就是它的門牌
6. ★ ALB (Application Load Balancer) - Layer 7 智慧路由
   1. **工作方式**：需要解析 **HTTP Header、URL、Cookie** 等內容做**智慧路由**
   2. **架構設計**：背後是**動態擴展的節點池**，IP 會隨著流量自動增減變動
   3. **本質**：像一個「**反向代理伺服器**⭐️」，需要彈性擴展才能應對流量波動
7. ★★ AWS Systems Manager (SSM) **Session Manager** - 無需 SSH 的**安全遠端連線**
   1. **特點**：無需 SSH、無需開 port 22，直接在 AWS Console 連線進 EC2
   2. **優勢**：不用管理 SSH key、不用開防火牆、自動記錄操作歷史
8. ★ AWS Systems Manager **Parameter Store** - 集中式**配置**與**密碼**存儲
   1. **功能**：存放**配置資料、密碼、連線字串**⭐️
   2. Parameter Store 免費（標準版）適合簡單配置
   3. Secrets Manager 收費但支援**自動輪替**密碼
9. 😇 AWS Systems Manager **Run Command** - 批次遠端指令執行
   1. **用途**：批次執行指令到多台 EC2（**同時更新 100 台機器**等場景）
   2. **優勢**：不用一台一台 SSH 連線
10. 😇 AWS Systems Manager **Patch Manager** - 自動化系統更新管理○
    1. **功能**：自動化系統更新、安全補丁部署
    2. **配置**：可設定維護視窗（Maintenance Window）控制更新時機
11. 😇 AWS Cost Explorer：視覺化分析 AWS 帳單的工具，讓你看懂錢花在哪、預測未來開銷。
12. ★ AWS Cost Anomaly Detection - ML 自動異常偵測
    1. **功能**：用 ML 學習花費模式，**自動找出異常**（不用設定閾值）
    2. **運作方式**：系統自己判斷「這次不正常」，發現異常時發 SNS 通知
13. ★ AWS Budgets vs Cost Anomaly Detection - 考試判斷關鍵
    1. **AWS Budgets**：**你主動設定**閾值（「alert when cost exceeds $X」），超過時發出通知
    2. **Cost Anomaly Detection**：**AI 自動判斷**異常（「automatically detect unusual spending」），無需預設閾值
    3. **核心差異**：Budgets 是「**你說多少算超標**」，Anomaly Detection 是「**AI 說這次不對勁**」
14. ★★ S3 One Zone-IA vs Standard-IA 核心對比
    1. **可用性區域差異**：Standard-IA 跨 ≥3 個 AZ（SLA 99.9%、貴）；One Zone-IA 單一 AZ（SLA 99.5%、便宜 20%）
    2. **耐久性**：兩者皆 **11 個 9**（99.999999999%），但 AZ 毀損時 One Zone-IA **資料全失**
    3. **最低儲存天數**：兩者均 30 天
    4. **記憶技巧**：One Zone = **單點故障風險**；Standard-IA = **跨 AZ 備援安全但貴一點**
15. ★★ S3 One Zone-IA vs Standard-IA 考試選擇判斷標準
    1. **選 One Zone-IA**：資料**可重新產生**或**遺失可接受**，強調**成本最優化**（例：縮圖、暫存資料、可從其他來源重建的資料）
    2. **選 Standard-IA**：資料**重要且訪問頻率低**（每月或每季），需要**高可用性保證**（例：備份檔案、歷史紀錄、合規資料）
16. ★★ Security Group vs NACL - 網路安全層級對比
    1. **Security Group**：**EC2 instance 層級**，綁在**網卡 (ENI) 上**，精細控制單一主機的進出流量
    2. **NACL**：**Subnet 層級**，控制**整個子網路的進出**，粗粒度的網路邊界防護
17. ★★ AWS GuardDuty - 跨服務威脅偵測與異常監控
    1. **監控範圍**：EC2/VPC（透過 VPC Flow Logs）、IAM/API（透過 CloudTrail）、S3（透過 CloudTrail S3 data events）、DNS 查詢
    2. **進階保護**：EKS/Lambda **執行時期威脅**（需額外啟用）
18. ★★ Route 53 vs ELB - 負載均衡層級區分（考試重點）
    1. **看到「elasticity」**：首選 **ELB 系列**（ALB/NLB/CLB）
    2. **Route 53 應用場景**：**多區域流量分配**（Latency-based、Geolocation）、**災難恢復**（Failover）、**藍綠部署**（Weighted）
    3. **架構原則**：**不要把 Route 53 當成應用層的 Load Balancer 用**，這是錯誤的架構思維
19. ★★ EMR + Redshift 大數據分析架構
    1. **情境判斷**：需要 **big data processing frameworks**（Apache Hadoop/Spark）+ **標準 SQL 和 BI 工具分析**
    2. **服務角色**：EMR（**ETL 處理層** - 執行 Hadoop/Spark 做資料轉換和清洗） + Redshift（**分析層** - **支援 SQL 查詢和 BI 工具整合**）
20. ★★ Redshift 相比 S3+Athena 的優勢與應用場景
    1. **Redshift 優勢**：**列式儲存**（查詢效能遠超 S3+Athena）、**BI 工具整合**（原生支援 Tableau、PowerBI）、**MPP 架構**（join/aggregation 極快）
    2. **使用判斷**：S3+Athena 適合**偶爾查詢**；Redshift 適合**高頻分析**（無論用 EMR 或 Glue 處理，分析層都該用 Redshift）
21. 題目出現這些關鍵字，直接選：
    - 「最低延遲」但不限制地區 → Latency
    - 「特定國家用戶必須連特定資源」→ Geolocation
    - 「調整某地區流量偏向某資源」或「bias」→ Geoproximity
    - 「藍綠部署」、「A/B 測試」、「流量分配比例」→ Weighted
22. ★★ NLB Elastic IP - 客戶端白名單 IP 場景
    1. **問題**：客戶端只信任特定 IP，客戶端只信任特定 IP，你的服務必須用這些 IP 來接收請求
    2. **解法**：**NLB + Elastic IP**（將靜態 IP 綁定到每個 AZ 的 ENI 上）
23. 😇 NLB + EIP 必要性情境分析
    1. **客戶端白名單場景**：客戶發請求 → 目標是你的 NLB IP → 你提供固定 IP → 客戶設白名單
    2. **IP 穩定性**：**NLB + EIP 確保 IP 不會變**（EIP 綁定到每個 AZ 的 ENI）
24. ★★ Route 53 Failover - 災難恢復場景
    1. **問題**：主站掛了，要**快速自動切換**到備援站點
    2. **解法**：**Route 53 Failover routing + 健康檢查**（檢測主站不健康時自動轉向備援）
    3. **適配服務**：搭配 **S3 靜態網站**或 **CloudFront** 作為備援端點
25. **S3 Object Lock** (Compliance Mode)
    1. 本質：強制 WORM 模式，連 root 帳號都不能刪/改
    2. 場景：法規遵循（例如金融業、醫療業需符合法規要求）
    3. 考點：題目提到「regulatory compliance」、「prevent deletion by anyone including root」→ 選這個
26. ★★ VPC Peering 不支援**邊緣到邊緣路由** (Transitive Routing) - 考試頻考陷阱
    1. **核心限制**：VPC Peering 是「點對點」連接，不支援跨 VPC 傳遞流量
    2. **實際案例**：VPC-1 有 Direct Connect 連到地端，VPC-1 和 VPC-2 有 Peering，但**地端無法透過 VPC-1 → VPC-2 的路徑存取 VPC-2 的資源**
    3. **考試判斷關鍵**：看到「需要備援線路連結多個 VPC」且「提案用 Peering 傳遞」→ 立刻判斷為**錯誤**
27. ★ VPC Peering 不支援的傳遞項目清單（必背）
    1. **VPN 連線、Direct Connect、Internet Gateway、NAT Gateway、VPC Endpoint、ClassicLink IPv6**
    2. **記憶技巧**：所有**邊界連接類型**都不支援 Peering 傳遞，Peering 只負責「兩個 VPC 之間」
28. ★★ 跨 VPC 高可用備援方案 - 直接連線原則
    1. **正確方案 1**：**在目標 VPC 建立第二條 Direct Connect**（兩條專線都直接連到同一 VPC，主線故障時備線接手）
    2. **正確方案 2**：**VPN over Internet 作為備援**（直接連到目標 VPC，成本低於 Direct Connect）
    3. **判斷標準**：備援線路必須**直接連到目標 VPC**，不能透過 Peering 傳遞
29. 👀 錯誤方案範例 - VPC Peering 傳遞路由的死路
    1. **❌ 在 VPC-2 建立 Direct Connect**：地端流量無法透過 VPC-1 ↔ VPC-2 Peering 轉傳到 VPC-2 的 Direct Connect
    2. **❌ 在 VPC-2 建立 VPN**：同樣限制，VPN 連到 VPC-2 但流量無法經 Peering 到達 VPC-1
30. 😇 Transit Gateway 解決方案 - VPC Peering 的升級選項
    1. **核心功能**：Transit Gateway **支援傳遞路由**，可以讓流量透過 TGW 在多個 VPC 間轉傳
    2. **使用場景**：需要複雜的 VPC 互聯且要支援流量轉傳時（比 Peering 更靈活但配置更複雜）
    3. **考試應用**：若題目提到「Transit Gateway」和「多個 VPC 備援」，則 TGW 路由方案可行
31. ★★ AWS Cost Allocation Tags - 按部門追蹤成本的標準方案
    1. **核心流程**：**打標籤** (Tag resources with department name) → **啟用** (Activate in Billing Console) → **自動分組** (AWS generates report)
    2. **實作方式**：在資源上加標籤（例：`Department: Engineering`），然後在 Billing Console 啟用 Cost Allocation Tags
    3. **產出成果**：AWS 自動按啟用的 tags 分組顯示成本，實現**跨部門成本追蹤**
32. 😇 成本分配標籤 vs 一般標籤的區別
    1. **一般標籤**：用於資源管理、自動化等用途
    2. **成本分配標籤**：需在 Billing Console **額外啟用**，才能在 AWS Cost Explorer 中按 tag 分組計費
    3. **考試判斷**：題目提「按部門分析成本」或「cost allocation」→ 選 Cost Allocation Tags（而非其他監控工具）
33. ★ DMS 的搬遷模式選擇
    1. **Full Load**：**一次性全量搬遷**，適合初次遷移或資料量不大的情況
    2. **CDC (Change Data Capture)**：**持續同步異動**，保持源系統和目標系統資料一致（zero-downtime migration）
    3. **組合策略**：常見做法是先做 Full Load 打底，再啟動 CDC 同步增量變更
34. ★ AWS DMS 搭配 S3 的格式最佳實踐
    1. **預設格式**：`.csv`（通用但體積大）
    2. **優化格式**：**`.parquet`**（壓縮率高、查詢效能優）
    3. **選擇判斷**：題目提「節省成本」或「提升查詢效能」→ 優先選擇 **Parquet 格式**
35. 遇到 DMS 的題目，記住三個判斷點：
    1. 搬什麼：關聯式、NoSQL、Data Warehouse 都行
    2. 怎麼搬：一次性 (Full Load) 還是持續同步 (CDC)
    3. 安全性：提到加密 → 想到 SSL + CA certificate
36. ★★ CloudWatch + SNS 監控告警標準組合
    1. **CloudWatch**：監控 **AWS 資源**（CPU、記憶體、磁碟等），設定**告警規則**
    2. **SNS**：當告警觸發時，**自動發送通知**給訂閱者（email、SMS、HTTP endpoint 等）
    3. **典型流程**：CloudWatch 偵測 → 告警觸發 → SNS 發送通知 → 人工或自動化回應
37. ★ SNS vs SES 服務選擇判斷標準
    1. **SNS 用途**：系統通知、告警、**事件驅動**的消息分發（內部系統間通信）
    2. **SES 用途**：**發送給終端使用者**的郵件（客戶、會員）、需要**退信管理、開信率追蹤、郵件信譽管理**
    3. **記憶技巧**：SNS = Simple Notification Service (系統內告警)；SES = Simple Email Service (用戶外發郵件)
38. ★★ EBS Volume AZ 限制與備份特性
    1. **核心特性**：**AZ 內自動複製**防止硬體故障；只能掛載到**同一個 AZ** 的 EC2
    2. **跨 AZ 使用方案**：先建 snapshot，再從 snapshot 在另一個 AZ 建立新 volume
39. ★★ EBS 高可用性限制 - 常見考試陷阱
    1. **限制**：EBS **不提供跨 AZ 高可用性**
    2. **解決方案**：需使用 **EFS** 或**多 AZ 架構**
40. 😇 EBS 核心概念記憶
    1. **本質**：EBS = **本地硬碟**概念，**綁定單一 AZ**
41. ★★ AWS Organizations + IAM Identity Center - 多帳戶統一管理與認證方案
    1. **AWS Organizations 解決的問題**：**多個帳戶統一管理**（帳單、資源控管）、用 **SCP (Service Control Policy)** 做權限邊界控管（連 root user 都受限）
    2. **IAM Identity Center 解決的問題**：**一組帳密登所有帳戶**（類似 SSO）、可整合**企業 AD、Okta 等目錄服務**
    3. **架構整合**：AWS Organizations + IAM Identity Center 組合達成**多帳戶統一身份驗證和權限管理**
42. ★★ SCP vs IAM Policy - 權限管理層級對比
    1. **SCP (Service Control Policy)**：作用於**整個 OU 或帳戶**層級、本質是**權限邊界（否決權）**、用於**組織層級限制**
    2. **IAM Policy**：作用於**單一 user/role** 層級、本質是**賦予權限**、用於**個別使用者授權**
    3. **記憶口訣**：SCP 是「**天花板**」（最高限制），IAM Policy 是「**地板**」（實際權限）
43. ★ 多帳戶架構考試判斷技巧
    1. 「**多帳戶管理**」→ **AWS Organizations**
    2. 「**集中式認證 / 企業目錄**」→ **IAM Identity Center**
    3. 「**控制帳戶權限**」→ **SCP**
44. 😇 AWS Organizations 基本結構與帳戶管理
    1. **獨立帳戶特性**：每個帳戶有**獨立的 account ID**、每個帳戶有**自己的 root user**
    2. **統一管理功能**：Organizations 提供**統一帳單**、**SCP 權限控管**、**集中管理**
45. ★★ 多帳戶架構實戰題解：正確答案組合（2選項）
    1. **選項一**：**Integrate AWS IAM Identity Center with the corporate directory service**（與企業目錄整合實現**集中式身份驗證**）+ **Configure SCP to manage AWS accounts**（配置 **SCP 管理帳戶權限**）
    2. **選項二**：**Implement AWS Organizations to create multi-account architecture**（建立**多帳戶架構**提供**整合檢視和集中管理**）
46. ★ AWS Directory Service vs IAM Identity Center - 考試陷阱對比
    1. **AWS Directory Service 錯誤原因**：僅管理**目錄服務**（如 AD）、**不是做多帳戶認證**、缺少**跨帳戶身份聯邦功能**
    2. **正確做法**：**IAM Identity Center 整合 Directory Service**（IAM Identity Center 提供多帳戶認證，Directory Service 提供目錄管理）
47. ★ Amazon Pinpoint 服務識別關鍵字
    1. **Marketing**：行銷用途
    2. **Personalized**：個人化訊息（可帶入姓名、喜好等變數）
    3. **Large number of customers**：大量客戶管理
48. 😇 Amazon Pinpoint 核心功能
    1. **客戶分群**：Segmentation
    2. **測試優化**：A/B Testing
    3. **多管道發送**：SMS、Email、Push、Voice
    4. **效果追蹤**：開信率、點擊率
49. ★★ Amazon AppFlow - SaaS 應用整合專用服務
    1. **核心價值**：無需寫程式就能在 **AWS 和第三方 SaaS 之間傳輸資料**
    2. **識別關鍵字**：**Salesforce/Slack 等 SaaS** → **AWS S3**、**歸檔 + 分析需求**
50. 😇 Amazon AppFlow 核心特性
    1. **原生支援**：50+ SaaS（Salesforce、Slack、Google Analytics、Zendesk 等）
    2. **簡化設定**：點選設定，不用寫 API 串接
    3. **彈性觸發**：支援排程或事件觸發
    4. **自動處理**：資料轉換和加密
51. ★★ AWS Trusted Advisor - 最佳實踐檢查工具
    1. **本質**：AWS 內建的**最佳實踐檢查工具**，自動掃描帳號並給建議
    2. **五大檢查類別**：Cost Optimization（閒置資源、低使用率 instances）、Performance（服務限制使用率、throughput 瓶頸）、Security（Security Group 開放規則、IAM 使用情況）、Fault Tolerance（EBS/RDS 快照、Multi-AZ 設定）、Service Limits（各服務配額使用情況）
52. 應考三個小技巧
    1. 陷阱題很少
    2. 不要 overthink
    3. 如果一個方案看起來可行，但過於複雜，那可能是錯的
53. Site-to-Site VPN 的兩個部分是：
    1. Customer Gateway (CGW) - 客戶端的 VPN 設備
    2. Virtual Private Gateway (VGW) - AWS 端的 VPN 閘道
    3. 兩扇門對接，VPN 隧道才能建立
    4. 如果題目問「如何**讓本地機房連到 AWS**」→ 答案通常就是 Site-to-Site VPN
    5. CGW 需要一個**固定的公網 IP**
54. ★★ ENI (Elastic Network Interface) 故障轉移機制 - 高可用性架構
    1. **核心概念**：ENI = 虛擬網卡，記憶 **Private IP、Elastic IP、MAC address**；**可從一台 EC2 拔下來插到另一台**
    2. **使用場景**：資料庫 instance、NAT instance、需要固定 IP 的應用
    3. **運作流程**：主 instance 掛 ENI（綁定應用 Private IP）→ DNS 指向此 IP → 主 instance 掛時**拔下 ENI 插到備援機器** → **流量自動轉移，無需改 DNS、Route Table**
55. ★★ ENI Detach/Attach 技術操作與限制條件
    1. **操作步驟**：**Detach**（解除故障 EC2 的 ENI 綁定）→ **Attach**（綁定 ENI 到備援 EC2）
    2. **核心限制**：**Primary ENI (eth0) 不能拔**（只有 secondary ENI 能做 detach/attach）；兩台 EC2 必須**同一 AZ**；ENI 與目標 EC2 必須**同一 subnet**
    3. **技術原因**：EC2 instance **必須至少有一張 ENI 才能運作**，eth0 是 launch 時自動建立的 primary ENI，所以 AWS 不允許 detach（故障轉移必須用 secondary ENI）
56. 😇 ENI 自動故障轉移實務與範例
    1. **實現方式**：通常寫成自動化腳本，**監控發現主機掛時自動執行 detach/attach**
    2. **運作範例**：主機 (i-abc123) eth1 (10.0.1.50) → 故障後 detach eth1 → attach 到備援機 (i-xyz789) eth1 ← **同一 IP 自動轉移，無需改 DNS、Route Table**
57. ★★ ENI HA 常見錯誤方案 - 考試陷阱（EIP + Transit Gateway）
    1. **錯誤點 1：不需要 EIP**：題目說應用是 **private**（內網）；EIP 是給 **public** 訪問用的；內網通訊用 **Private IP 就好**
    2. **錯誤點 2：Transit Gateway 用途錯誤**：**Transit Gateway 用來連接多個 VPC、VPC 和 on-premises，充當網路中心樞紐**；**不能做故障轉移**，不能「自動搬移 EIP」
58. 😇 ENI vs EIP vs Transit Gateway 服務用途判斷標準
    1. **Private 應用 HA**：用 **ENI detach/attach**（故障轉移用 secondary ENI + Private IP）
    2. **考試陷阱提醒**：看到熟悉服務名稱（EIP、Transit Gateway）就選下去是錯的；EIP 解決 public 訪問問題、Transit Gateway 解決網路連接問題，都**不是 instance 故障轉移**工具
59. ★★ CloudFront + Lambda@Edge - 動態內容處理能力
    1. **Lambda@Edge 執行時間點**：**Viewer Request**（用戶請求到達時）、**Origin Request**（轉發到源站前）、**Origin Response**（源站回應後）、**Viewer Response**（回傳給用戶前）
    2. **使用判斷**：需要「**看請求內容做決定**」→ **Lambda@Edge**；只是「加固定 headers」→ **Response Headers Policy**
60. 😇 Lambda@Edge 核心概念
    1. **本質**：**CloudFront 的攔截器**，在邊緣節點執行程式碼
    2. **典型場景**：動態內容改寫、請求/回應處理、條件邏輯判斷
61. ★★ Route 53 + S3 靜態網站託管 - 考試常見組合
    1. **必要條件**：**S3 bucket 名稱必須完全等於 domain name**（例：bucket 名稱為 `example.com`）
    2. **配置流程**：**Registered domain name**（已註冊域名）→ **Route 53 DNS 設定** → **S3 bucket（啟用靜態網站裝載）** → **ALIAS 記錄指向 S3**
62. 😇 ECS Task 基本特性
    1. **執行環境**：可以跑在 **EC2 或 Fargate**（無伺服器）
    2. **權限與網路**：每個 Task 有**自己的 IAM Role**（Task Role）、每個 Task 有**獨立的 ENI**（網路介面）
63. 😇 Task Definition vs Task - 藍圖與實例
    1. **Task Definition**：**藍圖**（定義要跑什麼 image、要多少 CPU/RAM）
    2. **Task**：**實例**（根據藍圖實際跑起來的容器）
    3. **類比**：**ECS Task ≈ Docker Compose 的一組容器**，是**容器化應用程式的執行單位**
64. ★★ SQS vs Amazon MQ - 消息佇列服務對比
    1. **SQS**：**AWS 原生服務**，簡單便宜，適合新應用開發
    2. **Amazon MQ**：**相容 ActiveMQ/RabbitMQ**，用於遷移舊系統（已用 ActiveMQ/RabbitMQ 的應用無需改程式碼）
65. ★★ Bastion Host 網路架構與安全設計
    1. **位置限制**：**必須在公有子網路（Public Subnet）**，才能從 internet 連線進來，是唯一的入口點
    2. **IP 配置**：需要 **Elastic IP 或 Public IP**；私有子網路的資源不需要也不該有
66. ★ Elastic IP 安全最佳實踐 - 私有子網路資源的關鍵限制
    1. **技術能力 vs 安全原則**：技術上可以在私有子網路的 EC2 附加 Elastic IP，但違反安全最佳實踐
    2. **風險分析**：私有子網路 EC2 若附加 Elastic IP，就變成可從 internet 直接訪問，**破壞了「私有」的安全設計**
    3. **設計原則**：**公網資源用 EIP，私網資源不該有**
67. ★★ AWS DMS NoSQL 遷移能力 - 容易忽視的考試重點
    1. **服務核心定位**：**DMS = 資料搬運工**，只要 AWS 支援的資料庫就幾乎都能搬（不限於關聯式）
    2. **NoSQL 遷移路線**：主要是 **MongoDB → DynamoDB** 這條路線
    3. **考試應用**：DMS 不是只給 RDB 用的，**NoSQL 遷移也是它的強項**，考試中會出現此場景

68. ★★ AWS Network Firewall - VPC 層級 Layer 7 流量過濾
    1. **核心能力**：支援 **Layer 7 檢查**，可以基於 **URL、FQDN 進行過濾**（Security Group/NACL 只能基於 IP/Port）
    2. **應用場景**：自動阻擋惡意 URL、黑名單 FQDN、VPC 級別的入侵偵測
69. ★★ 多層防禦架構 (Defense-in-Depth) 考試判斷技巧
    1. **關鍵字識別**：「malicious URLs」「blacklisted FQDNs」→ **Layer 7 過濾** → **Network Firewall**⭐️
    2. **架構設計**：「layered approach」「defense-in-depth」→ **公私有 subnet 分離** + **Network Firewall 在邊界**
    3. **成本控制**：「minimum resources」→ **只用 Network Firewall**（無需 WAF、Shield 等額外服務）
70. 👀 多層防禦常見陷阱選項
    1. **用 WAF**：WAF 主要保護應用層，**不是 VPC 級別過濾**
    2. **只用 Security Group**：**無法識別 URL 或 FQDN**，僅能基於 IP/Port
    3. **複雜多 VPC 架構**：題目要求最小資源，不適用此方案
71. 😇 Network Firewall 部署架構
    1. **位置**：VPC 邊界（不掛在 ALB 上）
    2. **流量必經**：所有進出流量 → Network Firewall（Layer 7 檢查）→ 應用層
72. ★ CIDR Block 核心概念與 VPC 地址管理
    1. **Block 定義**：**一個完整的 IP 地址段**（例：Primary 10.0.0.0/16、Secondary 10.1.0.0/16）
    2. **多 Block 支援**：VPC 最多 **5 個 CIDR blocks**（1 個 Primary + 4 個 Secondary），每個 block **獨立的地址池**
    3. **Exhaustion 含義**：「blocks are nearing exhaustion」= **所有 CIDR blocks 加總的可用空間快用完**
73. 😇 VPC CIDR Block 名詞釐清
    1. **複數用法**：AWS 文件習慣用複數「blocks」因為 VPC 支援多個 CIDR（題目可能只有 1 個 block，但「blocks」是通用說法）
    2. **配置範例**：Primary: 10.0.0.0/16（必須有）+ Secondary: 10.1.0.0/16、172.31.0.0/16（額外附加）
74. ★★ VPC IPv4 Exhaustion 擴展方案 - IPv6-Only Subnet
    1. **問題場景**：VPC 的 IPv4 CIDR blocks **加總快用完**（exhaustion）
    2. **正確解法**：建立 **IPv6-only subnet**（前提：VPC 已啟用 IPv6）
    3. **核心優勢**：IPv6 **無限供應**，不佔用 IPv4 資源，實現**無限擴展性**
75. ★ IPv4 vs IPv6 架構分層 - VPC 設計原則
    1. **VPC 層級**：IPv4 **強制存在、不可移除**（無法停用或完全遷移）
    2. **Subnet 層級**：可以 **IPv6-only**（純 IPv6，不需要 IPv4 CIDR）
    3. **架構設計**：VPC 保持雙棧（IPv4 + IPv6）；新 subnet 用 IPv6-only 擴展
76. 👀 IPv4 Exhaustion 常見陷阱方案
    1. **❌ 移除/停用 VPC 的 IPv4**：**不可能**，IPv4 是強制的
    2. **❌ 新增更大的 IPv4 subnet**：**不符合 scalability**，IPv4 本質有限
    3. **✅ 建 IPv6-only subnet**：**符合題意**，實現長期擴展
77. ★ VPC 和 Subnet 的 IPv4 層級差異 - 架構分層理解
    1. **VPC 層級**：創建時**必須指定 IPv4 CIDR**（例：10.0.0.0/16）、**地址池總量**、**無法移除**
    2. **Subnet 層級**：**從 VPC CIDR 切分**（例：10.0.1.0/24）、**實際配 IP 給資源的地方**、EC2/RDS 等的 IPv4 地址來自此層
78. 😇 IPv4 層級架構比喻與陷阱認知
    1. **層級比喻**：VPC = 倉庫總容量、Subnet = 具體貨架
    2. **陷阱認知**：「停用 VPC 的 IPv4」= 拆掉整個倉庫，**所有貨架（subnet）都會掛**，這是為什麼不可能移除 VPC 層級的 IPv4
79. ★ VPC IPv6 啟用與 Subnet 建立流程
    1. **VPC 建立**：**必須指定 IPv4 CIDR**（例：10.0.0.0/16）；IPv6 CIDR **可選、隨時可加**
    2. **IPv6-only Subnet 建立**：VPC **先有 IPv6 CIDR**（例：2001:db8::/56） → **Subnet 的 IPv6 範圍從 VPC 的 IPv6 CIDR 切分**（例：2001:db8::/64）
    3. **獨立性**：IPv6-only subnet **不佔用 IPv4 CIDR**，完全獨立運作
80. 😇 VPC IPv6 實務配置範例
    1. **雙棧 VPC**：VPC: 10.0.0.0/16（IPv4）+ 2001:db8::/56（IPv6）
    2. **IPv6-only Subnet**：2001:db8::/64（純 IPv6，不需要 IPv4 CIDR 分配）
    3. **應用場景**：IPv4 exhaustion 時直接建 IPv6-only subnet 擴展，VPC 已有 IPv6 CIDR 即可實現
81. ★★ RDS Proxy - Lambda 高併發連線池管理方案
    1. **問題核心**：Lambda 高併發時每個實例建立新連線，導致 RDS **連線數爆掉**
    2. **解決方案**：**RDS Proxy 複用連線池**，Lambda 不直接連 RDS，改透過 Proxy 的 **warm connection pool** 分配連線，即使 Lambda 瞬間擴展數萬個也能穩定控制對 RDS 的實際連線數
82. 😇 RDS Proxy 支援範圍與額外好處
    1. **支援資料庫**：**MySQL、PostgreSQL、Aurora**
    2. **額外收益**：**降低 failover 時間**、**IAM 整合增強安全性**
83. ★★ AWS Global Accelerator - 非 HTTP 協定與靜態 IP 場景
    1. **典型使用場景**：**遊戲伺服器（UDP）、IoT（MQTT）、VoIP、需要靜態 IP 的應用、快速區域 failover**
    2. **核心特性**：**Anycast IP** 提供靜態全球 IP、支援**非 HTTP 協定**（相比 CloudFront 只支援 HTTP/HTTPS）
84. 👀 CloudFront vs Global Accelerator 考試判斷陷阱
    1. **CloudFront**：S3 靜態網站、內容分發、**HTTP/HTTPS 協定**
    2. **Global Accelerator**：**非 HTTP 協定或需要固定 IP**（UDP/MQTT/VoIP 等）
    3. **記憶重點**：不要因為看到「全球加速」就選 Global Accelerator，要看**協定類型和後端服務特性**
85. ★★ 解耦架構 (Decoupled Architecture) 服務判斷 - 系統間通訊與消息傳遞
    1. **核心概念**：**讓系統各組件獨立運作，透過中間層傳遞訊息/任務**，避免直接溝通
    2. **標準解耦服務**：**SQS**（訊息佇列，A 系統 → 佇列 ← B 系統）、**SNS**（Pub/Sub 發佈/訂閱）、**EventBridge**（事件驅動）、**SWF**（複雜工作流協調）
86. 👀 解耦架構常見陷阱選項 - 錯誤原因分析
    1. **RDS/DynamoDB**：資料庫服務，存放資料但**不能解決系統間通訊問題**
    2. **VPC Peering**：網路連線工具，讓 VPC 互通但**on-premises 根本無法使用**（Peering 只限 VPC 之間）
87. ★ Amazon SWF (Simple Workflow Service) - 工作流程協調與長時間任務管理
    1. **核心功能**：**協調分散式應用中的工作流程**，確保任務按順序執行、追蹤狀態
    2. **關鍵特性**：**支援 human tasks（人工介入）**、**長時間工作流（最長 1 年）**、屬於**解耦架構工具**
88. 😇 SWF vs Step Functions - 工作流服務選擇與實務應用
    1. **實務優先順序**：**優先用 Step Functions**（現代化、更好的整合）
    2. **SWF 考試應用**：SAA-C03 考試仍會出現 SWF，**舊系統或複雜分散式工作流才考慮**
89. ★★ AWS Snowball - 大規模資料傳輸的實體硬碟搬運服務
    1. **核心定位**：**實體硬碟搬運服務**，AWS 寄硬碟 → 複製資料 → 寄回 → 上傳 S3
    2. **使用判斷標準**：**上傳超過 1 週**或**數十 TB 以上且網路慢/貴** → 選 Snowball
90. ★ S3 Transfer Acceleration - 網路路由優化限制與應用場景
    1. **本質**：**透過 CloudFront edge locations 加速上傳到 S3**，改善網路路由⭐️
    2. **核心限制**：**只能優化網路路由，無法突破頻寬上限**（100 Mbps 上傳 250 TB ≈ 231 天，即使用 Acceleration 可能變 200 天）
    3. **適用場景**：**網路好但距離遠**的情況
91. ★★ AWS STS (Security Token Service) - 臨時憑證與身份授權
    1. **核心功能**：**產生臨時憑證**，讓使用者/服務跨帳號或提權存取資源
    2. **運作流程**：呼叫 AssumeRole → STS → 回傳**臨時憑證**（Access Key + Secret + Session Token）→ 用臨時憑證存取資源
92. ★★ STS 主要使用場景與應用
    1. **跨帳號存取**：Dev 帳號存取 Prod 帳號資源
    2. **臨時提權**：EC2 需要短暫存取 S3
    3. **聯合身份**：公司 AD 使用者登入 AWS
    4. **MFA 保護**：敏感操作需 MFA 才給臨時憑證
93. ★ STS 考試判斷關鍵字：看到「**跨帳號**」「**臨時權限**」「**AssumeRole**」→ 選 **STS**
94. 😇 Multitier（多層式架構）核心概念
    1. **定義**：將應用程式切分成**多個獨立的層級**，各層專注處理特定功能
    2. **常見三層架構**：
       - **Presentation Tier**（展示層）：前端 UI、使用者介面
       - **Application Tier**（應用層）：後端 API、商業邏輯
       - **Data Tier**（資料層）：資料庫、儲存系統
95. ★★ Site-to-Site VPN 連線必要元件
    1. **核心觀念**：要讓 AWS VPC 跟地端機房溝通，雙方都要有「門」（**Customer Gateway** 和 **Virtual Private Gateway**）
    2. **VPC 外部配置**：**Customer Gateway**（你的 VPN 設備，硬體或軟體）+ **靜態公網 IP**（此設備必須有固定的公網 IP）
96. 😇 Site-to-Site VPN 架構與實戰經驗
    1. **典型應用場景**：傳統企業有地端的 SAP、Oracle 等系統搬不動，用 Site-to-Site VPN 跟 AWS 打通
    2. **架構示意**：你的機房 [Customer Gateway (需要 static IP)] ←--VPN Tunnel--→ AWS VPC [Virtual Private Gateway]
97. ★★ Site-to-Site VPN 題目判斷標準
    1. **VPC 外部設定**：題目問「VPC 外部要設定什麼」→ 一定是 **Customer Gateway**
    2. **VPC 內部配置**：題目問「VPC 內部要設定什麼」→ **Virtual Private Gateway + Route Table + Security Group**（三個都需要）
98. ★★ Virtual Private Gateway (VPG) 架構與公網暴露原理
    1. **VPG 公網特性**：**VPG 有 AWS 提供的公網 endpoint**；建立時 AWS 自動分配**兩個公網 IP**（用於 HA）
    2. **連線流程**：你的機房（公網 IP）→ Internet（加密通道）→ VPG（AWS 公網 endpoint）→ VPC 內部網路
99. ★ Site-to-Site VPN 安全設計核心觀念
    1. **VPG 暴露vs內部資源私有**：**VPG 本身要能從公網存取**，但連線是**加密的 IPsec tunnel**；**VPC 內的資源（EC2、RDS）不需要公網 IP**，透過 VPG 就能跟機房溝通
    2. **架構區別**：Site-to-Site VPN ≠ 將 EC2 放在 public subnet；VPC **內部資源仍是私有的**，只是透過加密通道與地端溝通
100. ★★ AWS Outposts - 邊界運算與本地處理方案
     1. **核心本質**：**把 AWS 搬到你家機房**，在地端運行 AWS 服務（EC2、RDS、ECS 等），享受託管服務便利同時兼顧本地延遲和合規性
     2. **典型應用場景**：需要 **low latency（低延遲）**、**local data processing（本地資料處理）**、**regulatory compliance（法規遵循）** 的工作負載
101. ★★ CloudFront + S3 全球軟體分發方案
     1. **場景**：全球多個 Outposts 要從 us-west-2 S3 下載大型軟體更新檔
     2. **延遲優勢**：**Edge locations 快取**，不用每次都從 us-west-2 拉⭐️
     3. **成本優勢**：CloudFront 流量費比跨區 S3 傳輸便宜
     4. **管理優勢**：設定一次就完成，自動全球分發
102. ★★ CloudFront Signed URLs - S3 內容安全分發
     1. **權限控制**：透過 Signed URLs 限制誰能下載軟體更新，不是誰都能存取
     2. **時效性管理**：URL 可設定過期時間，確保只有授權使用者在時間範圍內存取
     3. **考試陷阱**：題目出現「**secure distribution**」→ 直接選 **Signed URLs**
103. ★★ S3 Server-Side Encryption (SSE) 核心概念與實作方式
     1. **技術特性**：使用 **256-bit AES-256 演算法**、每個物件用**獨立密鑰加密**、**密鑰本身也會加密**並定期輪替
     2. **實作方式**：在 **bucket policy** 中要求所有上傳須帶 **x-amz-server-side-encryption header**
104. 😇 SSE-S3 vs SSE-C 加密方案選擇
     1. **SSE-S3**：AWS 託管密鑰，使用 `x-amz-server-side-encryption header` 啟用
     2. **SSE-C**：客戶提供密鑰，需提供 `x-amz-server-side-encryption-customer-algorithm`、`x-amz-server-side-encryption-customer-key`、`x-amz-server-side-encryption-customer-key-MD5` 三個 headers
105. ★ SCP 常見陷阱 - 權限控管工具不是登入機制
     1. **SCP 的真正用途**：**限制子帳號的權限範圍**（禁止使用特定 region、禁止刪除 CloudTrail）
     2. **考試陷阱**：題目提 Organizations → 容易聯想 SCP；但若題目要的是**統一登入機制**（而非權限控管），SCP 就是**錯誤答案**
     3. **記憶重點**：**SCP 跟登入無關**，是權限控制工具；登入問題應找 **IAM Identity Center**
106. ★ Directory Service vs IAM Identity Center 選擇陷阱
     1. **Directory Service 限制**：**只負責目錄管理**（AD Connector、Managed Microsoft AD），太籠統、**缺少跨帳戶認證功能**
     2. **正確答案**：跨帳戶統一登入應選 **IAM Identity Center 整合 Directory Service**（前者做多帳戶認證，後者做目錄管理）
107. ★★ AWS Config - 資源配置變更持續記錄與合規檢查
     1. **核心功能**：**持續記錄 AWS 資源配置狀態變化**、**自動檢查是否符合規範**（類似「配置的 Git history + 自動 linter」）
     2. **Config Rules**：自動檢查資源是否符合規則（例如：所有 S3 bucket 必須加密、EC2 必須在特定 VPC）
108. 😇 AWS Config vs CloudTrail - 配置記錄與稽核對比
     1. **CloudTrail**：記錄「**誰做了什麼操作**」（API calls）、誰在什麼時間改了什麼
     2. **Config**：記錄「**資源配置的狀態變化**」（configuration snapshots）、檢查配置是否符合規範
     3. **應用場景**：合規稽核、安全分析、故障排查找出根本原因
109. ★★ EC2 存取 AWS 服務最佳實踐 - IAM Role vs Access Key
     1. **正確做法**：**給 EC2 掛 IAM Role**（自動輪換臨時憑證、無需管理金鑰、遵循最小權限原則）
     2. **絕對禁止**：**在 EC2 裡放 IAM User 的 Access Key**（洩露風險高、無自動輪換）
     3. **記憶口訣**：**機器用 Role，人類用 User**
110. 😇 MFA - 多因子驗證安全防護
     1. **應用對象**：**系統管理員和人類使用者**（登入 AWS Console、使用 API 時）
     2. **防護機制**：多一層驗證，防止帳號被盜用（類似銀行 App 的 OTP）
     3. **記憶**：MFA = **Multi-Factor Authentication**（雙因子驗證）
111. ★ MFA 對 Console 和 API 的不同驗證方式
     1. **Console 登入**：輸入帳密 → 輸入 MFA code → 進入（直覺式流程）
     2. **API 呼叫**：需要用**臨時憑證（STS）+ MFA token**（比 Console 複雜，通常只用於高權限操作如刪 RDS、改 IAM policy）
     3. **考試重點**：題目說「MFA can control access to AWS service APIs」指的就是透過 STS 機制實現
112. ★★ Auto Scaling Cooldown Period - 擴縮容間隔控制
     1. **功能**：防止 Auto Scaling group 在前一次擴容/縮容完成前立即執行下一次擴縮容
     2. **預設值**：300 秒
     3. **特性**：可依需求調整配置
113. ★★ Snowball Edge - 邊緣運算與離線資料處理
     1. **內建運算能力**：不只儲存，有 EC2、Lambda、S3 等服務；vs 普通 Snowball 只是搬運硬碟
     2. **適用場景**：**無本地運算資源**（只有感測器、攝影機）、**斷網環境**（礦場、海上鑽油平台）、**資料量超大需先縮小**（原始 1PB→處理後 100TB）
114. ★ Snowball Edge 考試判斷標準
     1. **選 Snowball Edge**：看到「**離線 / 斷網 / 邊緣運算 / 本地處理 / 即時分析**」等關鍵字
     2. **選普通 Snowball**：只說「**大量資料搬 AWS**」，沒提本地運算需求
115. ★★ AWS Storage Gateway - 混合雲儲存統一存取
     1. **核心概念**：**讓地端應用透過標準協定存取 AWS 雲端儲存**，就像存取本地儲存一樣
     2. **三種模式**：**File Gateway**（NFS/SMB）、**Volume Gateway**（iSCSI）、**Tape Gateway**（磁帶庫）
116. ★ File Gateway 實務應用
     1. **協定**：NFS / SMB
     2. **後端**：Amazon S3
     3. **適用場景**：檔案共享、內容分發、備份
117. ★ Volume Gateway 深入對比 - Cached vs Stored
     1. **協定**：iSCSI (區塊儲存)
     2. **後端**：S3 + EBS Snapshots
     3. **Cached 模式**：**主資料在雲端 S3，常用資料快取本地**（讀快、容量受限）
     4. **Stored 模式**：**主資料在地端，非同步備份到 S3**（容量大但初次上傳慢）
118. 👀 Tape Gateway - 舊系統備份整合
     1. **協定**：iSCSI VTL (虛擬磁帶庫)
     2. **後端**：S3 / Glacier
     3. **用途**：取代實體磁帶備份系統，適合長期歸檔需求
119. 😇 Storage Gateway 核心價值四要素
     1. **無痛整合**：地端應用不需改寫程式碼
     2. **混合雲資料同步**：資料自動同步到 AWS
     3. **成本優化**：用 S3 取代昂貴的地端儲存設施
     4. **災難復原**：雲端自動備份，提升 DR 能力
120. ★★ Storage Gateway 考試判斷關鍵字
     1. **看到「hybrid storage」或「on-premises 持續存取雲端」**→ **Storage Gateway**
     2. **需要 NFS/SMB 檔案共享**→ **File Gateway**
     3. **需要區塊儲存/磁碟介面**→ **Volume Gateway**
     4. **需要磁帶介面/舊備份系統相容**→ **Tape Gateway**
121. ★★ Lambda VPC 存取原理 - ENI 自動建立機制
     1. **預設行為**：Lambda **預設不在你的 VPC 內**，跑在 AWS 自己的網路環境
     2. **VPC 存取方式**：當設定 Lambda 要存取 VPC 資源（例如 RDS）時，AWS 會**自動建立 ENI**（虛擬網卡）做為連線橋樑
     3. **技術原理**：Lambda 透過 ENI 連進你的 VPC，就像**插網路線**讓 Lambda 能存取私有網路資源
122. ★★ Lambda 多組合 VPC 配置 - ENI 數量與 IP 消耗
     1. **ENI 產生規則**：每個「**Subnet + Security Group**」組合產生一個 ENI，相同組合複用同一個 ENI⭐️
     2. **配置範例**
        - Subnet A + Security Group X → 1 個 ENI
        - Subnet B + Security Group X → 另 1 個 ENI
        - Subnet A + Security Group Y → 又 1 個 ENI
     3. **IP 消耗影響**：Lambda 連 VPC 會消耗 subnet IP，若 CIDR 太小可能 **IP 不足導致 Lambda 無法冷啟**
123. ★★ ENI 與 Private IP 的綁定關係 - IP 消耗機制
     1. **IP 分配規則**：**每個 ENI = 至少 1 個 private IP**；ENI 會從 subnet 的 CIDR 中分配至少一個 private IP
     2. **本質限制**：**ENI 本身一定會佔用 IP**，無論是否在使用中（每個 ENI 都會消耗 1 個 private IP）
124. ★★ NAT Gateway 保護機制核心原理
     1. **Stateful Connection Tracking**：只允許「**內部發起的連線**」的回應封包通過
     2. **運作流程**：出站（私有 EC2 → NAT → 外部，NAT 記錄連線）→ 回應（外部 → NAT → 私有 EC2，NAT 查表放行）→ 主動連入（外部 → NAT，查無連線紀錄直接丟棄）
125. ★★ NAT Gateway 安全防護原理分析
     1. **私有 IP 不可路由**：10.x.x.x 在公網無效，外部根本不知道內部有哪些 IP
     2. **只看到 NAT 公開 IP**：攻擊者掃描的是 NAT Gateway 的公開 IP
     3. **無連線表 = 丟棄**：外部主動發起的封包沒有對應的連線紀錄
126. ★ NAT Gateway 考試場景判斷標準
     1. **✅ 通過場景**：私有 EC2 下載套件、呼叫 API（內部主動發起）
     2. **❌ 阻擋場景**：外部掃描 NAT IP、從家裡 SSH 進私有 EC2（外部主動發起）
     3. **記憶口訣**：「**內部主動我放行，外部主動我不理**」
127. ★★ NAT Gateway 部署位置限制 - 必須在 Public Subnet
     1. **核心原因**：**NAT Gateway 自己需要公開 IP (Elastic IP) 才能對外通訊**
     2. 如果放在 Private Subnet，它自己都出不去，無法幫其他機器上網
     3. **Public Subnet 定義**：**Route Table 有 0.0.0.0/0 → Internet Gateway**、**裡面的資源有 Public IP 或 Elastic IP**
128. ★ NAT Gateway 部署配置與運作原理
     1. **部署要求**：必須指定一個 **Elastic IP** 給 NAT Gateway
     2. **網路配置**：它所在的 Public Subnet 的 **Route Table 會指向 IGW**
     3. **運作結果**：因此 NAT Gateway 自己就能上網，才能幫助 Private Subnet 的資源對外通訊
129. ★★ DynamoDB Gateway Endpoint - VPC 私有存取解決方案
     1. **問題背景**：DynamoDB 是 **public service**，VPC 內的 EC2 預設要透過 **IGW 走公網存取**
     2. **解決方案**：**Gateway Endpoint 讓流量走 AWS 內網**，用 private IP 存取
130. 實作方式：
     1. 在 VPC 建立 Gateway Endpoint
     2. **關聯到 Route Table**（自動新增 DynamoDB prefix list 路由）⭐️
     3. 完全免費，流量不出 AWS 網路
131. ★★ AWS Backup vs DynamoDB 原生備份 - 跨帳號/區域災難恢復
     1. **DynamoDB 原生 on-demand backup 限制**：無法跨帳號、無法跨區域、沒有進階功能（cost tags、cold storage）
     2. **AWS Backup 優勢**：跨帳號/區域複製、統一管理多種 AWS 資源備份、支援 cost allocation tags、可轉 cold storage 降成本
132. ★ DynamoDB 存取與備份考試判斷標準
     1. **關鍵字判斷**：「disaster recovery + 跨帳號/區域」→ **AWS Backup**；「VPC private 存取 DynamoDB」→ **Gateway Endpoint**
     2. **考試重點**：測試你知不知道這兩個服務的邊界和適用場景
133. ★★ NACL vs Gateway Endpoint - 功能界限與常見考試陷阱
     1. **NACL 功能限制**：只能控制「**允許/拒絕**」流量，**無法改變路由路徑**
     2. **錯誤認知**：即使 NACL 允許到 `dynamodb.us-east-1.amazonaws.com`，流量依然會走 **IGW → Internet**
     3. **正確做法**：必須用 **Gateway Endpoint** 才能改變路由，讓流量走 AWS 內網
     4. **功能類比**：NACL = **門禁卡**（控制能不能進出）；Gateway Endpoint = **專用通道**（改變走的路線）
134. ★★ DynamoDB 原生備份 vs AWS Backup - 跨帳號災難恢復限制
     1. **原生備份限制**：DynamoDB **on-demand backup 不支援跨帳號/區域**，僅限本帳號內使用
     2. **AWS Backup 優勢**：支援**跨帳號/區域備份**，提供完整的災難恢復方案
     3. **記憶口訣**：「**NACL 管安全，Endpoint 改路徑**」- 看到題目說「用 NACL 走內網」直接排除
135. ★★ Kinesis Video Streams + Rekognition - 即時影像分析標準組合
     1. **應用場景**：**即時監控、人臉辨識、物體偵測**
     2. **服務組合**：**Kinesis Video Streams**（串流影像收集）+ **Amazon Rekognition**（AI 影像分析）
     3. **Rekognition 分類**：**Image**（靜態圖片分析）和 **Video**（影片串流分析）兩種，考試時別搞混
     4. **考試判斷**：看到「即時影像分析」或「人臉/物體偵測」→ 直接選這個組合
136. ★ CloudFront Geo-restriction - 地理區域存取限制功能
     1. **功能定位**：**限制特定地區存取內容**（黑名單概念），純粹的**存取控制工具**
     2. **非路由功能**：**不是選擇性路由**，無法實現「歐洲去 Frankfurt，亞洲去 Singapore」的智慧導向
     3. **核心區別**：**Restriction = 禁止存取**；**Routing = 智慧導向**（後者需要 Route 53 Geolocation/Geoproximity）
     4. **考試應用**：題目提到「阻擋特定國家」→ **Geo-restriction**；提到「就近存取」→ **Route 53 地理路由**
137. ★★ Route 53 Failover Routing 配置 - Active-Passive 災難恢復
     1. **核心設定**：建立 Primary + Secondary 記錄，routing policy 選 Failover
     2. **Health Check 運作**：監控 Primary endpoint，Route 53 定期發送 HTTP/HTTPS/TCP 檢查；**Evaluate Target Health = Yes** 檢查 ALB 背後的 targets 狀態
     3. **網路開放**：NACL 和 Route Table 必須允許 Route 53 health checker 的 IP，否則 Health Check 永遠失敗
138. 👀 Route 53 Failover 常見陷阱
     1. **只設 Failover Policy 沒設 Health Check**：不會自動切換
     2. **防火牆擋 Route 53 IP**：Health Check 誤判為 unhealthy
     3. **忘記 Evaluate Target Health**：ALB 活著但 EC2 全掛還是導流量過去
139. ★★ API Gateway - REST / WebSocket API 的統一管理平台
     1. **統一入口**：提供 **REST API 和 WebSocket API** 的中央存取點
     2. **流量管理**：**Throttling**（限速）和 **Quota**（配額管理）控制 API 使用量
     3. **請求/回應轉換**：支援 **HTTP Header、Body、Path 參數**的轉換與修改
     4. **快取機制**：內建 **Response Caching**，減少後端呼叫
     5. **認證授權**：支援 **IAM、Lambda authorizer、Cognito** 多種認證方式
     6. **後端整合**：與 **Lambda、EC2、HTTP 端點、其他 AWS 服務**無縫整合
140. 😇 API Gateway 應用場景與架構定位
     1. **定位**：API 的「前門」，負責流量調度、安全驗證、轉換和限流
     2. **典型架構**：Clients → API Gateway（認證、限流、轉換）→ Lambda / EC2（商業邏輯）→ 資料庫
141. S3 安全場景的服務分工：
     1. GuardDuty：偵測異常存取、惡意行為
     2. CloudTrail：記錄所有 API 操作（事後查帳，純記錄）
     3. Macie：偵測 S3 內的敏感資料（PII、機密資訊）
     4. Config：檢查 S3 設定是否符合規範
     5. Amazon Inspector：掃描 EC2 上的 S3 存取程式碼漏洞
142. ★★ RDS Multi-AZ Failover 機制 - DNS (CNAME) 切換原理
     1. **Failover 工作方式**：**RDS endpoint (CNAME)** 指向 primary 的 DNS 記錄，failover 時 CNAME 自動指向 standby，**應用程式無需改 connection string**
     2. **為何不是 IP 切換**：**IP 是綁定 subnet 的**，subnet 不能跨 AZ（Primary 在 AZ-A：IP `10.0.1.10`，Standby 在 AZ-B：IP `10.0.2.20`，無法搬遷）
     3. **記憶點**：**Multi-AZ Failover = DNS (CNAME) 切換，不是 IP 切換**，這也是為何 RDS endpoint 是 DNS 名稱而非 IP 的原因
143. ★★ AWS Control Tower - 多帳號自動化管理中心⭐️
     1. **核心定位**：**AWS Organizations** 的升級版，提供**自動化 baseline 部署和持續合規檢查**（而非只是帳戶管理）
     2. **核心功能**：**自動建立 multi-account 架構**、**預設 security baseline**、**偵測和修復不合規配置**
     3. **考試判斷關鍵字**：「**多帳號 + 標準化配置**」、「**治理策略 + 自動化建立**」、「**預設 baseline + 持續合規檢查**」 → 選 **Control Tower**
144. ★★ EKS - Kubernetes cloud-agnostic 多雲支援
     1. **Cloud-agnostic 定義**：「**我不在乎底層是哪朵雲**」，能**無痛切換多個雲平台**（AWS EKS ↔ GCP GKE ↔ Azure AKS）
     2. **Kubernetes 開源特性**：**開源容器編排標準**，YAML、Helm Charts、kubectl 指令**完全相容**各雲平台
     3. **遷移優勢**：**遷移時無需改程式碼**，只需改底層基礎設施，**不被單一雲端綁架**
145. ★ ECS - AWS 專屬容器服務與遷移風險
     1. **AWS 專屬定位**：**AWS 原生服務**，Task Definition 和配置**只能在 AWS 運作**
     2. **遷移成本高**：遷移到其他雲平台（GCP、Azure）時**必須完全重寫配置**，**不符合 cloud-agnostic 原則**
     3. **考試判斷**：題目提「**多雲環境**」「**遷移到其他雲**」「**避免雲端綁架**」→ **EKS**；題目**只在 AWS 內運作** → **ECS** 也可以
146. ★★ AWS DataSync - 大規模資料遷移的自動化工具
     1. **核心定位**：**地端 → AWS 的專用資料傳輸工具**，比開源工具快 **10 倍**，無需寫 script 手動排程
     2. **三大關鍵特性**：**自動化傳輸**（無需管理排程、監控）、**高速傳輸**（優化網路效能）、**支援冷資料歸檔**（直接送 Glacier Deep Archive）
     3. 題目提「**大量資料**」「**自動化遷移**」「**加速傳輸**」→ **DataSync**
     4. 題目提「**實體硬碟**」→ **Snowball**；題目提「**混合雲持續存取**」→ **Storage Gateway**
147. ★★ Gateway Endpoint Policy - 集中式 S3 存取控制方案
    1. **核心概念**：Gateway Endpoint 讓 VPC 內的資源**不透過網際網路**直接存取 S3/DynamoDB，想像成「**內網專線**」
    2. **存取控制方式**：控制「**哪些 S3 buckets 可以被存取**」有兩種方式
       - **Bucket Policy**：在每個 bucket 設定
       - **Endpoint Policy**：在 Gateway Endpoint 設定
148. ★★ Endpoint Policy vs Bucket Policy - 集中管理優勢
    1. **效率對比**
       - **Bucket Policy**：10 個 trusted buckets = 設定 10 次
       - **Endpoint Policy**：10 個 trusted buckets = 設定 1 次
    2. **考試判斷**：AWS 考試偏好「**集中管理、簡化維運**」的方案
       - **multiple buckets** + Gateway Endpoint 場景 → **Endpoint Policy**
    3. **關鍵記憶**：Endpoint Policy **優先於 Bucket Policy**，是額外的限制層
149. ★ 權限驗證三層模型 - IAM/Bucket/Endpoint Policy 共同作用
    1. **驗證流程**：三層依序檢查
       - **IAM Policy**：使用者有什麼權限
       - **Bucket Policy**：bucket 允許誰存取
       - **Endpoint Policy**：這條路徑允許存取哪些 bucket
    2. **通過條件**：三者**都要 Allow** 才能成功存取，任一層 Deny 就無法通過
150. ★★ Gateway Endpoint Policy 配置機制
    1. **政策設定**：建立 Gateway endpoint 時可附加 **endpoint policy** 控制對服務的存取權限
    2. **政策修改**：可修改附加到 endpoint 的政策、新增或移除 route tables
    3. **政策關係**：Endpoint Policy **不會覆蓋或替代** IAM user policies 或服務專屬政策（如 S3 bucket policies），是**獨立的控制層**
151. 😇 Bucket Policy vs Endpoint Policy 使用判斷
    1. **技術可行性**：兩者都能控制流量到 trusted S3 buckets
    2. **實務優勢**
       - **Bucket Policy**：逐個 bucket 設定，**時間成本高**
       - **Endpoint Policy**：單一 policy 設定，**效率高**
    3. **題目判斷**：看到「**trusted S3 buckets**」+ 「multiple buckets」→ 優先選 **Endpoint Policy**

152. ★★ AWS Secrets Manager - 動態密碼管理與自動輪換
    1. **核心價值**：API 動態取得最新密碼、自動輪換（auto rotation）、應用程式無需改 code
    2. **解決的痛點**：密碼寫死在程式碼、要換密碼需改 code 重新部署、多個 app 共用密碼漏改爆炸
    3. **輪換流程**：產生新密碼 → 更新 RDS → 更新 secret → 驗證連線
153. 😇 Secrets Manager 考試判斷標準與應用場景
    1. **關鍵需求識別**：加密儲存 credentials、定期自動輪換、應用程式永遠用最新版本
    2. **考試判斷關鍵字**：看到「**自動輪換**」+ 「**RDS credentials**」→ 選 **Secrets Manager**
154. ★★ AWS Glue - 全託管 ETL 服務與低成本資料轉換
    1. **核心價值**：全託管的 ETL 服務，不用管理伺服器、自動擴展、按使用量付費
    2. **標準流程**：S3 (.csv) → **Glue Crawler**（自動發現 schema）→ **Glue ETL Job**（轉換成 Parquet）→ S3 (Parquet 格式)
    3. **Crawler 自動化**：自動掃描 S3 識別資料結構、建立 **Data Catalog**（無需手動定義 schema）
155. ★★ Glue ETL Job - Parquet 轉換與排程執行
    1. **ETL 能力**：支援 Parquet/ORC 格式轉換（內建）、可設定定時執行、Serverless 無需管理基礎設施
    2. **低維運優勢**：全託管無伺服器維護、只在 job 執行時計費、相比自建 EMR 叢集成本更低
156. ★ Parquet 格式優勢與 Glue 考試判斷
    1. **Parquet 特性**：欄位式儲存（columnar）查詢效能優、高壓縮率降低儲存成本、與 Athena/Redshift Spectrum 整合
    2. **考試判斷關鍵字**：看到「**ETL**」「**CSV → Parquet/ORC**」「**最少維運成本**」「**定期資料處理**」「**Schema discovery**」→ 選 **Glue**
157. ★★ S3 Object Lock 架構關係與模式組合
    1. **Object Lock 功能開關**：整體控制機制，啟用後可使用 Retention Mode 或 Legal Hold
    2. **Retention Mode（二選一）**：**Compliance Mode**（最嚴格，無法覆寫）、**Governance Mode**（可特殊權限覆寫）
    3. **Legal Hold（獨立開關）**：可搭配任何 Retention Mode 使用，無期限限制
158. ★ S3 Object Lock 考試判斷要點
    1. **Retention Mode**：必選其一（或都不選），設定保留期限
    2. **Legal Hold**：可選，獨立於 Retention Mode，無期限
    3. **記憶技巧**：Legal Hold 是額外的鎖，可以疊加在任何 Retention Mode 上
