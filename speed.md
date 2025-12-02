# Super Speed Notes for SAA

★★ An **Edge-Optimized API Gateway** is **best for geographically distributed clients.**

1. API requests are routed to the **nearest CloudFront Edge Location** which improves latency.
2. The API Gateway still lives in **one** AWS Region.
3. 這是預設選項，而且自動整合 CloudFront⭐️

★★ VPC 內的應用如何安全存取 DynamoDB（避免走公網）

1. **解決方案：Gateway Endpoint**
2. 運作流程：EC2 (private subnet) → VPC route table (自動更新 DynamoDB CIDR) → Gateway Endpoint → DynamoDB (走 AWS 內網)⭐️

★★ Gateway Endpoint vs Interface Endpoint 比較⭐️

1. **Gateway Endpoint**：路由層級，**改 route table，免費**
2. **Interface Endpoint**：網路層級，加 ENI，要錢

★★ Kinesis Data Streams + Lambda 做即時資料脫敏

1. **標準套路**：AWS 處理敏感資料的推薦架構
2. **運作流程**：Kinesis 負責接收串流資料 → Lambda 在資料「transit（傳輸中）」時就進行轉換/匿名化 → 確保資料在落地 DynamoDB 前就已經處理好
3. **串流 + 即時處理 = Kinesis + Lambda**⭐️

- 😇 AWS Storage Gateway 存取範圍限制
   1. **用途**：僅用於備份**本地伺服器**資料，**不支援存取 Amazon VPC 內的資源**⭐️
- ★★ NLB (Network Load Balancer) - Layer 4 高效能負載均衡
   1. **工作方式**：只看 **IP + Port**，直接轉發 TCP/UDP 封包
   2. **架構設計**：每個 AZ 配置一個固定的**網路介面（ENI）**，ENI 可綁定**靜態 IP**⭐️
   3. **本質**：像一個「高效能的網路交換器」，IP 就是它的門牌
- ★ ALB (Application Load Balancer) - Layer 7 智慧路由
   1. **工作方式**：需要解析 **HTTP Header、URL、Cookie** 等內容做**智慧路由**
   2. **架構設計**：背後是**動態擴展的節點池**，IP 會隨著流量自動增減變動
   3. **本質**：像一個「**反向代理伺服器**⭐️」，需要彈性擴展才能應對流量波動
- ★ AWS Systems Manager **Parameter Store** - 集中式**配置**與**密碼**存儲
   1. **功能**：存放**配置資料、密碼、連線字串**⭐️
   2. Parameter Store 免費（標準版）適合簡單配置
   3. Secrets Manager 收費但支援**自動輪替**密碼
- ★★ S3 One Zone-IA vs Standard-IA 核心對比
   1. **可用性區域差異**：Standard-IA 跨 ≥3 個 AZ（SLA 99.9%、貴）；One Zone-IA 單一 AZ（SLA 99.5%、便宜 20%）⭐️
   2. **耐久性**：兩者皆 **11 個 9**（99.999999999%），但 AZ 毀損時 One Zone-IA **資料全失**
   3. **最低儲存天數**：兩者均 30 天
   4. **記憶技巧**：One Zone = **單點故障風險**；Standard-IA = **跨 AZ 備援安全但貴一點**
   5. **耐久性範圍限制**：One Zone-IA 的「耐久性」保證僅限於「**單一 AZ**」範圍內
   6. **高可用性考試陷阱**：考試問「需要高可用性」時，One Zone-IA 一定是錯的
   7. **災難級別差異**：One Zone-IA 的耐久性**不包含 AZ 級別災難**
   8. **術語精確性**：AWS 術語定義精確，不是欺騙而是**範圍限定**
   9. **考試核心重點**：測試你是否理解這些**細微但關鍵的差異**
- ★★ Security Group vs NACL - 網路安全層級對比
   1. **Security Group**：綁定在 **ENI（網卡）層級**，控制**單一網卡**的進出流量⭐️
   2. Security Group = 一份「**虛擬規則清單**」
      - 不是實體防火牆設備
      - 不是綁死在某台主機上
      - 可以被多個 ENI「**引用**」
      - 本質是「**邏輯上的策略集合**」
   3. **重點**：一個 SG 可綁定**多個 ENI**（可跨 EC2），一個 ENI 也可綁定**多個 SG**（最多 5 個）
   4. **NACL**：**Subnet 層級**，控制**整個子網路的進出**，所有進出 subnet 的流量都會經過⭐️
   5. **記憶技巧**：SG = **個人防彈衣**（穿在身上）；NACL = **社區大門**（進出都要過）
- ★★ Route 53 vs ELB - 負載均衡層級區分（考試重點）
   1. **看到「elasticity」**：首選 **ELB 系列**（ALB/NLB/CLB）
   2. Route 53 應用場景：⭐️
      - **多區域流量分配**（Latency-based、Geolocation）
      - **災難恢復**（Failover）
      - **藍綠部署**（Weighted）
   3. **不要把 Route 53 當成應用層的 Load Balancer 用**，這是錯誤的架構思維
- ★★ Redshift 相比 S3+Athena 的優勢與應用場景
   1. **Redshift 優勢**：
      - **列式儲存**：查詢效能**遠超** S3+Athena
      - **BI 工具整合**：原生支援 Tableau、PowerBI
      - **MPP 架構**：join/aggregation 極快
   2. S3 + Athena 適合**偶爾查詢**
   3. Redshift 適合**高頻分析**
   4. 無論用 EMR 或 Glue 處理，分析層都該用 Redshift⭐️
- Route 53 題目出現這些關鍵字，直接選：
  - 「最低延遲」但不限制地區 → Latency
  - 「**特定國家用戶**必須連特定資源」→ Geolocation⭐️
  - 「調整某地區流量偏向某資源」或「bias」→ **Geoproximity**⭐️
  - 「藍綠部署」、「A/B 測試」、「流量分配比例」→ Weighted
- ★★ Route 53 Failover - 災難恢復場景
   1. **問題**：主站掛了，要**快速自動切換**到備援站點
   2. **解法**：**Route 53 Failover routing + 健康檢查**（檢測主站不健康時自動轉向備援）
   3. **適配服務**：搭配 **S3 靜態網站**或**CloudFront + S3** 作為備援端點⭐️
- S3 Object Lock (**Compliance Mode**)⭐️
   1. 本質：強制 **WORM** 模式，**連 root 帳號都不能刪/改**
   2. 場景：法規遵循（例如金融業、醫療業需符合法規要求）
   3. 考點：題目提到「regulatory compliance」、「prevent deletion by **anyone** including root」→ 選這個
- ★★ VPC Peering 不支援**邊緣到邊緣路由** (Transitive Routing) - 考試頻考陷阱
   1. **核心限制**：VPC Peering 是「點對點」連接，不支援跨 VPC 傳遞流量
   2. **實際案例**：VPC-1 有 Direct Connect 連到地端，VPC-1 和 VPC-2 有 Peering，但**地端無法透過 VPC-1 → VPC-2 的路徑存取 VPC-2 的資源**⭐️
   3. **考試判斷關鍵**：看到「需要備援線路連結多個 VPC」且「提案用 Peering 傳遞」→ 立刻判斷為**錯誤**
- ★★ 跨 VPC 高可用備援方案 - 直接連線原則
   1. **正確方案 1**：**在目標 VPC 建立第二條 Direct Connect**（兩條專線都直接連到同一 VPC，主線故障時備線接手）→ 但成本高，不切實際
   2. **正確方案 2**：**VPN over Internet 作為備援**（直接連到目標 VPC，成本低於 Direct Connect）⭐️
   3. **判斷標準**：備援線路必須**直接連到目標 VPC**，不能透過 Peering 傳遞
- ★ DMS 的搬遷模式選擇
   1. **Full Load**：**一次性全量搬遷**，適合初次遷移或資料量不大的情況
   2. **CDC (Change Data Capture)**：**持續同步異動**，保持源系統和目標系統資料一致（zero-downtime migration）⭐️
   3. **組合策略**：常見做法是先做 Full Load 打底，再啟動 CDC 同步增量變更
- ★★ CloudWatch + SNS 監控告警標準組合
   1. **CloudWatch**：監控 **AWS 資源**（CPU、記憶體、磁碟等），設定**告警規則**
   2. **SNS**：當告警觸發時，**自動發送通知**給訂閱者（email、SMS、HTTP endpoint 等）⭐️
   3. **典型流程**：CloudWatch 偵測 → 告警觸發 → SNS 發送通知 → 人工或自動化回應⭐️
- ★ SNS vs SES 服務選擇判斷標準
   1. **SNS 用途**：系統通知、告警、**事件驅動**的消息分發（內部系統間通信）
   2. **SES 用途**：**發送給終端使用者**的郵件（客戶、會員）、需要**退信管理、開信率追蹤、郵件信譽管理**⭐️
   3. **記憶技巧**：SNS = Simple Notification Service (系統內告警)；SES = Simple Email Service (用戶外發郵件)
- ★★ EBS Volume AZ 限制與備份特性
   1. **核心特性**：**AZ 內自動複製**防止硬體故障
   2. 只能掛載到**同一個 AZ** 的 EC2⭐️
   3. **跨 AZ 使用方案**：先建 snapshot，再從 snapshot 在另一個 AZ 建立新 volume⭐️
- ★★ EBS 高可用性限制 - 常見考試陷阱
   1. **限制**：EBS **只能掛載在單一 AZ**,不提供跨 AZ 高可用性⭐️
   2. **解決方案**：
      - 改用 **EFS**(自動跨 AZ)
      - 或設計**多 AZ 架構**(每個 AZ 各自部署 EC2 + EBS)
- ★★ AWS Organizations + IAM Identity Center - 多帳戶統一管理與認證方案⭐️
   1. **AWS Organizations 解決的問題**：**多個帳戶統一管理**（帳單、資源控管）、用 **SCP (Service Control Policy)** 做**權限邊界控管**（連 root user 都受限）⭐️
   2. **IAM Identity Center 解決的問題**：**一組帳密登所有帳戶**（類似 SSO）、可整合**企業 AD、Okta 等目錄服務**
   3. **架構整合**：AWS Organizations + IAM Identity Center 組合達成**多帳戶統一身份驗證和權限管理**⭐️
- ★★ SCP vs IAM Policy - 權限管理層級對比⭐️
   1. **SCP (Service Control Policy)**：
      - **作用範圍**：作用於**整個 OU 或帳戶**層級⭐️
      - **本質特性**：本質是**權限邊界（否決權）**
      - **使用場景**：用於**組織層級限制**
   2. **IAM Policy**：
      - **作用範圍**：作用於**單一 user/role** 層級
      - **本質特性**：本質是**賦予權限**
      - **使用場景**：用於**個別使用者授權**
   3. **記憶口訣**：
      - **SCP 比喻**：SCP 是「**天花板**」（最高限制）
      - **IAM Policy 比喻**：IAM Policy 是「**地板**」（實際權限）
- ★ AWS Directory Service vs IAM Identity Center - 考試陷阱對比
   1. **AWS Directory Service 錯誤原因**：僅管理**目錄服務**（如 AD）、**不是做多帳戶認證**、缺少**跨帳戶身份聯邦功能**
   2. **正確做法**：**IAM Identity Center 整合 Directory Service**（IAM Identity Center 提供多帳戶認證,Directory Service 提供目錄管理）
   3. **目錄管理 (Directory Management)**
      - **定義**：集中式管理使用者帳號、群組、權限的資料庫系統
      - **內容**：使用者名稱、密碼、群組成員資格、組織結構
      - **類比**：公司員工通訊錄系統
   4. **角色分工**
      - **Directory Service**：儲存「誰是誰」(目錄資料庫)
      - **IAM Identity Center**：管理「誰能做什麼」(認證 + 授權)
   5. **考試陷阱範例**
      - ❌ 題目：整合地端 AD 並實現多帳戶 SSO → 只選 AWS Directory Service (缺 SSO)
      - ✅ 正確：IAM Identity Center + Directory Service 組合⭐️
- ★★ Amazon AppFlow - **SaaS 應用整合專用服務**⭐️
   1. **核心價值**：無需寫程式就能在 **AWS 和第三方 SaaS 之間傳輸資料**
   2. **識別關鍵字**：**Salesforce/Slack 等 SaaS** → AWS S3、歸檔 + 分析需求
