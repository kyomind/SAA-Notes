# Super Speed Notes for SAA

- ★★ An **Edge-Optimized API Gateway** is **best for geographically distributed clients.**
   1. API requests are routed to the **nearest CloudFront Edge Location** which improves latency.
   2. The API Gateway still lives in **one** AWS Region.
   3. 這是預設選項，而且自動整合 CloudFront⭐️
- ★★ VPC 內的應用如何安全存取 DynamoDB（避免走公網）
    1. **解決方案：Gateway Endpoint**
    2. 運作流程：EC2 (private subnet) → VPC route table (自動更新 DynamoDB CIDR) → Gateway Endpoint → DynamoDB (走 AWS 內網)⭐️
- ★★ Gateway Endpoint vs Interface Endpoint 比較⭐️
    1. **Gateway Endpoint**：路由層級，**改 route table，免費**
    2. **Interface Endpoint**：網路層級，加 ENI，要錢
- ★★ Kinesis Data Streams + Lambda 做即時資料脫敏
    1. **標準套路**：AWS 處理敏感資料的推薦架構
    2. **運作流程**：Kinesis 負責接收串流資料 → Lambda 在資料「transit（傳輸中）」時就進行轉換/匿名化 → 確保資料在落地 DynamoDB 前就已經處理好
    3. **串流 + 即時處理 = Kinesis + Lambda**⭐️
- ★ DynamoDB Global Secondary Index (GSI) 的 Auto Scaling⭐️
    1. **獨立容量**：GSI 有自己的 RCU/WCU，**獨立**於 base table
    2. **場景示例**：Base table: 100 RCU/WCU、GSI-1: 50 RCU/WCU、GSI-2: 200 RCU/WCU ← 各自可單獨設定 Auto Scaling
    3. **優勢**：查詢集中在某個 GSI 時，不會影響其他 index 或 base table 的容量
- 👀 Lambda 是唯一「**沒有內建 caching 功能**」的服務⭐️
    1. 記憶點：**Lambda = 無狀態 = 無內建 cache**
- ★★ EBS volume 的可用性與複製特性
    1. **可用性：自動在同一 AZ 內複製**（防單點故障）⭐️
    2. **掛載限制**：只能掛載到**同 AZ** 的 EC2⭐️
- ★ EventBridge 的定位與核心概念
    1. **服務定位**：Event Bus (事件匯流排)
    2. **技術類比**：等同於 message queue / event broker (類似 RabbitMQ、Kafka)
    3. **核心用途**：用來「**當某件事發生時，觸發其他動作**」⭐️
- ★ EventBridge 的典型應用場景⭐️
    1. **EC2 狀態監控**：當 EC2 instance 狀態改變 → 發送通知
    2. **S3 檔案處理**：當 S3 有新檔案上傳 → 觸發 Lambda
    3. **負載均衡監控**：當 ALB target 變成 unhealthy → 執行某個動作
- ★ ALB Layer 7 路由的具體條件⭐️
    1. **域名路由**：Host header (api.example.com、admin.example.com)
    2. **路徑路由**：Path (`/users/*`、`/orders/*`)
    3. **方法與參數路由**：HTTP method (GET、POST)、Query string (?version=v2)
    4. **進階路由**：Source IP (特定 IP 走特定 target)、HTTP headers (自定義 header)
- ★★ 高可用 (HA - High Availability) 的特性⭐️
     1. **範圍**：同一 Region
     2. **切換方式**：自動切換
     3. **架構**：多個可用區域 (Multi-AZ)
- ★★ EBS 可用性與複製特性總結
     1. **基本綁定**：EBS 綁定單一 AZ，不跨 AZ、不跨 region
     2. **跨域保護**：要跨 AZ/region 保護資料 → 需手動建 snapshot 並複製⭐️
- ★ S3 存取控制方案的選擇建議
     1. **不推薦**：ACL（太細碎難管理）
     2. **推薦方案一**：Bucket Policy（大部分情況）⭐️
     3. **推薦方案二**：IAM Role（需要 Console 或臨時憑證時）
- ★ AWS 存取方式的定義與區別
     1. **Console（AWS Management Console）**：用瀏覽器登入 <https://console.aws.amazon.com> 看到的圖形化介面
     2. **Programmatic access**：用 CLI、SDK、API 呼叫 AWS（需要 **Access Key**）⭐️
     3. **Console access**：人類用瀏覽器「點點點」操作 AWS（需要登入帳號）
- ★★ 跨帳號存取的考試判斷標準
     1. **選題關鍵詞**：「不同 AWS 帳號」、「帳號 A 存取帳號 B」
     2. **解決方案一**：STS + AssumeRole（跨帳號臨時存取）⭐️
     3. **解決方案二**：Resource-based Policy（例如 S3 Bucket Policy 直接開放給另一個帳號）
- ★ Lambda@Edge 的適用場景分析
     1. **適用**：需要在「邊緣」處理邏輯（降低延遲）、A/B testing、個人化內容、安全驗證（檢查 header、token）、URL 重寫、重導向
     2. **不適用**：需要長時間運算（Lambda@Edge 有執行時間限制）⭐️
     3. 時間限制：5 秒或 30 秒（視不同觸發點而定）
- ★★ VPC 的定義與核心特性
     1. **定義**：VPC = 你在 AWS 上的私有網路區域
     2. **邏輯隔離**：你的網路與其他 AWS 用戶完全分開
     3. **完全控制**：IP 範圍（CIDR）、subnet、route table、gateway 都你說了算
     4. **混合雲支援**：可透過 **VPN/Direct Connect 連回地端機房**⭐️
- ★★ VPC 的必懂核心元件
     1. **Subnet**：public（可上網）vs private（不可上網）
     2. **Internet Gateway**：讓 public subnet 連外網
     3. **NAT Gateway**：讓 private subnet **單向連外網**（安全）
     4. **Route Table**：決定流量往哪走
     5. **Security Group**：instance 層級防火牆（stateful）
     6. **NACL**：**subnet 層級**防火牆（stateless）⭐️
- 😇 VPC 考試高頻考點
     1. **常考連接方案**：VPC peering、Transit Gateway、PrivateLink
     2. **基礎地位**：VPC 是 AWS 網路的基礎，**幾乎所有服務都跑在 VPC 裡**⭐️
- ★ ENI（Elastic Network Interface）的定義與作用
     1. **定義**：ENI = 虛擬網卡
     2. **Lambda 在 VPC 內的運作機制**：AWS 自動建立 ENI 連接 VPC
     3. **資源消耗**：每個 subnet/security group 組合需要 ENI，ENI 消耗 subnet 的 IP⭐️
- ★ Lambda + VPC 的核心記憶點⭐️
     1. **簡潔定義**：Lambda 要進 VPC = 需要網卡（ENI）= 需要 IP
     2. **常考場景**：Lambda 連 RDS、EFS、內部 API 都需要在 VPC 內⭐️
- ★ Lambda 在 VPC 內 scale 失敗的第二大原因：Subnet IP 不足
     1. **原因分析**：ENI 需要消耗 IP，Subnet CIDR 太小（如 /28 只有 16 個 IP）導致 IP 枯竭
     2. **考試重點**：小型 subnet 無法支撐 Lambda 的大規模 scale，造成限流⭐️
- ★ RDS Multi-AZ Failover **流程**
     1. **故障前**：`db.rds.amazonaws.com` → Primary (AZ-A)
     2. **故障後**：`db.rds.amazonaws.com` → Standby→Primary (AZ-B)
     3. **結果**：應用層無感知，**connection string 不變**⭐️
