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
23. 重點情境說明 NLB + EIP 的必要性
    1. 客戶發請求 → 目標是你的 NLB IP
    2. 你必須提供固定 IP → 讓客戶設白名單
    3. NLB + EIP 確保 IP 不會變
