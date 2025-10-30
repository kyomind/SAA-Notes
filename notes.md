# Speed Notes for SAA

1. ★ AWS Lake Formation 核心功能與權限管理
   1. **整合服務**：與 AWS Glue 整合，建立資料目錄描述可用資料集及其業務應用
   2. **存取控制**：定義策略在細粒度級別控制資料存取（grant and revoke permissions）
   3. **權限配置**：支援 IAM users、roles、groups 及聯邦式 Active Directory users
   4. **權限指派方式**：針對目錄物件（如 tables、columns）而非 buckets 和 objects
2. ★ FSx for Windows File Server = AWS 上的 Windows 檔案伺服器
   1. **特性**：完全託管的 **Windows 原生檔案系統**，支援 **SMB 協定**（Windows 的檔案共享協定）
   2. **存取方式**：跨平台存取，**Windows、Linux、macOS** 都能用
3. ★★ Amazon Data Lifecycle Manager (DLM) = EBS Snapshot 生命週期管理工具
   1. **功能**：自動按排程創建、保留、刪除 EBS snapshot
   2. **應用場景**：題目明確說 EBS snapshot 時，DLM 是標準答案（相比 AWS Backup）
4. 😇 AWS Storage Gateway 存取範圍限制
   1. **用途**：僅用於備份**本地伺服器**資料，**不支援存取 Amazon VPC 內的資源**
5. ★★ NLB (Network Load Balancer) - Layer 4 高效能負載均衡
   1. **工作方式**：只看 **IP + Port**，直接轉發 TCP/UDP 封包
   2. **架構設計**：每個 AZ 配置一個固定的**網路介面（ENI）**，ENI 可綁定**靜態 IP**
   3. **本質**：像一個「高效能的網路交換器」，IP 就是它的門牌
6. ★ ALB (Application Load Balancer) - Layer 7 智慧路由
   1. **工作方式**：需要解析 **HTTP Header、URL、Cookie** 等內容做**智慧路由**
   2. **架構設計**：背後是**動態擴展的節點池**，IP 會隨著流量自動增減變動
   3. **本質**：像一個「反向代理伺服器」，需要彈性擴展才能應對流量波動
7. ★★ AWS Systems Manager (SSM) **Session Manager** - 無需 SSH 的安全遠端連線
   1. **特點**：無需 SSH、無需開 port 22，直接在 AWS Console 連線進 EC2
   2. **優勢**：不用管理 SSH key、不用開防火牆、自動記錄操作歷史
8. ★ AWS Systems Manager **Parameter Store** - 集中式配置與密碼存儲
   1. **功能**：存放配置資料、密碼、連線字串
   2. **對比 Secrets Manager**：Parameter Store 免費（標準版）適合簡單配置；Secrets Manager 收費但支援自動輪替密碼
9. 😇 AWS Systems Manager **Run Command** - 批次遠端指令執行
   1. **用途**：批次執行指令到多台 EC2（同時更新 100 台機器等場景）
   2. **優勢**：不用一台一台 SSH 連線
10. 😇 AWS Systems Manager **Patch Manager** - 自動化系統更新管理
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
