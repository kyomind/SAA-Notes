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
7. ★★ AWS Systems Manager (SSM) Session Manager - 無需 SSH 的安全遠端連線
   1. **特點**：無需 SSH、無需開 port 22，直接在 AWS Console 連線進 EC2
   2. **優勢**：不用管理 SSH key、不用開防火牆、自動記錄操作歷史
8. ★ AWS Systems Manager Parameter Store - 集中式配置與密碼存儲
   1. **功能**：存放配置資料、密碼、連線字串
   2. **對比 Secrets Manager**：Parameter Store 免費（標準版）適合簡單配置；Secrets Manager 收費但支援自動輪替密碼
9. 😇 AWS Systems Manager Run Command - 批次遠端指令執行
   1. **用途**：批次執行指令到多台 EC2（同時更新 100 台機器等場景）
   2. **優勢**：不用一台一台 SSH 連線
10. 😇 AWS Systems Manager Patch Manager - 自動化系統更新管理
    1. **功能**：自動化系統更新、安全補丁部署
    2. **配置**：可設定維護視窗（Maintenance Window）控制更新時機
