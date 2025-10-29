# Speed Notes for SAA

1. ★ AWS Lake Formation 核心功能與權限管理
    1. **整合服務**：與 AWS Glue 整合，建立資料目錄描述可用資料集及其業務應用
    2. **存取控制**：定義策略在細粒度級別控制資料存取（grant and revoke permissions）
    3. **權限配置**：支援 IAM users、roles、groups 及聯邦式 Active Directory users
    4. **權限指派方式**：針對目錄物件（如 tables、columns）而非 buckets 和 objects
2. ★ FSx for Windows File Server = AWS 上的 Windows 檔案伺服器
    1. **特性**：完全託管的 **Windows 原生檔案系統**，支援 **SMB 協定**（Windows 的檔案共享協定）
    2. **存取方式**：跨平台存取，**Windows、Linux、macOS** 都能用
