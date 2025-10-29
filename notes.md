# Speed Notes for SAA

1. ★ AWS Lake Formation 核心功能與權限管理
   1. **整合服務**：與 AWS Glue 整合，建立資料目錄描述可用資料集及其業務應用
   2. **存取控制**：定義策略在細粒度級別控制資料存取（grant and revoke permissions）
   3. **權限配置**：支援 IAM users、roles、groups 及聯邦式 Active Directory users
   4. **權限指派方式**：針對目錄物件（如 tables、columns）而非 buckets 和 objects
2. ★ FSx for Windows File Server = AWS 上的 Windows 檔案伺服器
   1. **特性**：完全託管的 **Windows 原生檔案系統**，支援 **SMB 協定**（Windows 的檔案共享協定）
   2. **存取方式**：跨平台存取，**Windows、Linux、macOS** 都能用
3. Amazon Data Lifecycle Manager (DLM) 就是一個「定時排程器」，**專門管理 EBS snapshot 的生命週期**：
   1. 創建：自動按排程備份
   2. 保留：依政策決定保留多久
   3. 刪除：自動清理過期的 snapshot
   4. 用 AWS Backup → 可以，但題目明確說 EBS snapshot 時，DLM 是標準答案
