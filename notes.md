# Speed Notes for SAA

1. ★ **TCP Health Check** 僅支援 Network Load Balancer 和 Classic Load Balancer，ALB 不支援 TCP health check
   1. ALB 只支援 HTTP/HTTPS health check
2. ★★ DataSync vs Storage Gateway — 「遷移」vs「整合」
   1. **DataSync**：一次性或定期的資料**遷移/搬遷**（關鍵詞：migrate, move, transfer, accelerate）
   2. **Storage Gateway**：混合雲**持續整合**，讓地端像在用雲端（關鍵詞：hybrid, integrate, on-premises access）
   3. **判斷技巧**：題目說 "immediately move" → DataSync；題目說「地端需持續存取雲端」→ Storage Gateway
3. ★★ S3 Object Lock — WORM 合規需求的標準答案
   1. **用途**：看到「不可修改」「合規」「WORM」→ S3 Object Lock
   2. **Governance Mode**：特定權限可覆寫（較彈性）
   3. **Compliance Mode**：連 root 都無法刪除（最嚴格，醫療/金融適用）
   4. **保留機制**：Retention Period（保留期限）或 Legal Hold（法律保留）
4. ★★ CloudTrail Data Events vs Management Events
   1. **Management Events**：控制平面操作（CreateBucket, DeleteBucket 等），**預設啟用**
   2. **Data Events**：資料平面操作（GetObject, PutObject 等），**預設不啟用，需手動開啟**
   3. **考試重點**：題目要「物件層級稽核」→ 必須啟用 Data Events
5. 😇 DataSync vs Storage Gateway 都能搬資料，差異在「搬完之後」
   1. **DataSync**：搬完就結束，任務導向（一次性遷移或定期同步備份）
   2. **Storage Gateway**：搬完要持續用，架構導向（地端應用透過 Gateway 持續存取雲端儲存）
6. 😇 AWS AppSync — 託管 GraphQL API 服務
   1. **Serverless**：不用管伺服器，自動擴展
   2. **GraphQL**：不是 REST，讓前端「要什麼拿什麼」的查詢語言
   3. **多資料源整合**：可同時串接 DynamoDB、Lambda、HTTP APIs 等
   4. **即時訂閱**：內建 WebSocket 支援 Pub/Sub
7. ★★ AWS Batch vs Lambda — 運算密集型任務的正確選擇
   1. **Lambda 硬限制**：最大執行 15 分鐘、記憶體 10 GB、最多 6 vCPU
   2. **結論**：Lambda 為「短暫、輕量、事件驅動」設計，不適合重型運算
8. ★★ AWS Batch 核心概念
   1. **本質**：批次運算任務的編排服務，自動開 EC2、分配任務、跑完關機
   2. **平行執行**：定義多個 jobs，Batch 自動分散到多台 EC2
   3. **資源彈性**：可指定 EC2 instance type（如 `r5.8xlarge` 有 32 vCPU + 256 GB RAM）
   4. **最少維運**：不用自己管 EC2 lifecycle、job queue、失敗重試
9. ★ AWS Batch 選擇時機 — 關鍵字組合
   - **Batch processing / 批次處理**
   - **HPC / 高效能運算 / simulation**
   - **長時間運算**（超過 15 分鐘）
   - **大量平行任務 + 需要大資源**
   - **「least operational overhead」+ 運算密集**
10. ★ Batch vs Lambda vs EC2 自建 — 場景選擇
    1. **Lambda**：短任務（<15min）、事件觸發、輕量
    2. **AWS Batch**：長時間批次運算、需大資源、平行處理
    3. **EC2 + Auto Scaling**：需要完全控制環境、長期運行服務
    4. **陷阱提醒**：看到「simulation」「several hours」「大記憶體」→ 直接排除 Lambda
11. ★★ IAM Database Authentication — RDS 的 Token-based 認證
    1. **核心概念**：用 IAM 身份 + 短期 token 連接 RDS，取代傳統帳號密碼
    2. **運作流程**：Application 用 IAM credential 呼叫 `generate-db-auth-token` → 取得 15 分鐘有效 token → 用 token 當密碼連 RDS
    3. **優勢**：無需在應用程式存密碼、透過 IAM Policy 集中管理權限
12. ★ IAM DB Authentication 限制與特性
    1. **支援引擎**：僅 MySQL、PostgreSQL（**不支援 SQL Server、Oracle**）⭐️
    2. **Token 有效期**：15 分鐘
    3. **強制加密**：必須使用 SSL/TLS 連線
13. 😇 IAM DB Authentication 考試關鍵字
    - 「short-lived token」「temporary credentials」
    - 「IAM user/role 連接 RDS」
    - 「不想在應用程式存密碼」
    - 「centralized access management for database」
14. ★ IAM 認證服務快速區分
    1. **IAM Identity Center**：人登入 AWS Console（SSO 單一登入）
    2. **Secrets Manager**：存密碼、自動換密碼（應用程式取用）
    3. **IAM DB Authentication**：不用密碼，用 token 連 RDS
15. ★★ EBS Encryption 加密涵蓋範圍 — 一次加密，全面保護
    1. **加密演算法**：AES-256
    2. **自動加密範圍**：Data at rest（靜態資料）、Data in transit（Volume ↔ EC2 傳輸）、Snapshots、衍生 Volumes
    3. **核心概念**：啟用後上述四項**全部自動加密**，不需額外設定
16. ★★ EBS 加密運作機制與特性
    1. **加密位置**：在 **EC2 Host Server** 上進行，非 Instance 內部
    2. **對應用透明**：不需改 code，Instance 看到的是解密後資料
    3. **效能影響**：幾乎為零（硬體加速）
17. ★ EBS 加密的區域性限制
    1. **KMS 綁定**：EBS 加密使用 KMS，Key 綁定 Region
    2. **跨區複製**：Snapshot 跨 Region 複製時，需在目標 Region **重新加密**
18. ★★ EBS Encryption 考題陷阱
    1. **題目問**：如何確保 EBS 資料在傳輸過程中的安全？
    2. **陷阱選項**：設定 Security Group、使用 VPN
    3. **正確答案**：啟用 EBS Encryption（**自動處理 in-transit 加密**）
    4. **記憶點**：EBS Encryption = **at-rest + in-transit 都包了**
