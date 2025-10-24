# Speed Notes for SAA

1. ★★ EC2 Auto Scaling 的伸縮範圍**預設是整個 region**
   1. 預設跨 AZ
   2. 可以設定成單一 AZ，但省不了多少錢
   3. 數據傳輸：跨 AZ 有費用（約 $0.01/GB），但通常佔比很小
   4. 不能跨 region
2. ★★ **ELB 也不能跨 region，只能跨 AZ**
   1. 可以回想一下 **cross-zone balancing** 的概念
   2. 跨 Region 需要用 Route 53 + 多個 Regional ELB
3. 👀 A subnet is a **range** of IP addresses in your VPC.
4. ★ The AWS PrivateLink (which is also known as **VPC Endpoint**) **is just a highly available, scalable technology** that enables you to privately connect your VPC to the AWS services as if they were in your VPC.
5. ★ Public Subnet ≠ 自動有 Public IP，而是「**有資格配置** Public IP + 有 IGW 路由」
6. When you create a EC2 Capacity Reservation, you specify（綁定了）:
   1. – The **Availability Zone** in which to reserve the capacity
   2. – The **number of instances** for which to reserve capacity
   3. – The **instance attributes**, including the instance type, tenancy, and platform/OS
7. ★★ **You are limited to running On-Demand Instances per your vCPU-based On-Demand Instance limit per region**.
   1. **AWS 的 On-Demand Instance quota 是以 vCPU 為單位計算，而非實例數量**
8. ★ Install the Amazon **CloudWatch agent** to all the EC2 instances that gather the memory and disk utilization data.
   1. View the custom metrics in the CloudWatch console.
   2. 這些 metrics 會被收集到 CloudWatch Logs，而且邏輯要自己寫
9. ★ CloudWatch 自訂 metrics：You need to prepare a custom metric using a Perl or other shell script, as there are **no** ready-to-use metrics for the following:
   1. **Memory utilization**
   2. Disk swap utilization
   3. Disk space utilization
   4. Page file utilization
   5. Log collection
10. 👀 CloudWatch 預設可以看到以下這些 EC2 指標：
    1. **CPU Utilization**
    2. Disk Read activity
    3. Network In/Out
    4. Network packets In/Out
11. ★★ Auto Scaling group 的 scaling policy 有四種，都要了解具體內涵：
    1. Target Tracking Scaling
    2. Step Scaling
    3. Simple Scaling
    4. Scheduled Scaling
12. ★ DynamoDB 的 RCU 和 WCU 是完全獨立的，可以各自調整，互不影響
13. ★★ Hot Key 問題不是總容量不夠，而是**單一 partition 被打爆**
    - 增加 RCU 只是增加總容量，但熱門商品還是集中在同一個 partition
    - **花大錢解決不了問題**
14. ★ DAX 的缺點（考試也會考）
    - **只支援 DynamoDB**，不能用在 RDS、Aurora
    - **最終一致性**（Eventually Consistent），快取可能有**延遲**
    - **成本**：DAX 節點要另外收費（但比暴力增加 RCU 便宜）
15. 👀 DAX = DynamoDB 的 Redis
    - Redis 用來快取 API response
    - DAX 用來快取 DynamoDB 熱門資料
    - 存取速度從毫秒降到微秒
16. 👀 DAX 是一個完全管理、高可用、**針對 DynamoDB 的記憶體快取**，可提升高達 10 倍的效能
    1. 相比詳細的技術細節，題目更常問「什麼時候用 DAX」而非「DAX 的技術規格」
17. ★ DynamoDB Streams = 資料庫的變更日誌（Change Data Capture, CDC）
18. ★★ 白話：每當 DynamoDB 有資料新增/修改/刪除，Streams 會即時記錄這個事件
    1. **然後可以觸發 Lambda 做後續處理**
19. ★★ An **Edge-Optimized API Gateway** is **best for geographically distributed clients.**
    1. API requests are routed to the **nearest CloudFront Edge Location** which improves latency.
    2. The API Gateway still lives in **one** AWS Region.
    3. 這是預設選項，而且自動整合 CloudFront⭐️
20. ★ 目的關鍵需求：在 CloudFront Edge Locations 執行認證
    1. 不要打回 Origin
    2. 降低延遲——**用戶在哪裡，認證就在哪裡執行**
21. ★★ Lambda@Edge 是什麼？白話：**在 CDN 節點上跑的 Lambda，讓你在離用戶最近的地方處理請求**
22. 👀 **API Gateway 都是部署在單一 Region**，不在 Edge
    - 用戶還是要跨國打到你的 API Gateway，延遲沒改善
    - CloudFront 前面再放 API Gateway 是畫蛇添足
23. 👀 Lambda@Edge 可以在 **CloudFront Edge Location** 執行程式碼
24. 👀 The maximum size of **an item in a DynamoDB table** is **400 KB**.
25. ★ Which AWS service allows you to build Serverless workflows using AWS services (e.g., Lambda) and supports **human approval**?
    - Step Functions
26. 👀 DynamoDB **原生支援「Export to S3」功能**，幾個點擊就完成，零程式碼、零維護、最便宜
27. ★ DynamoDB + TTL (Time To Live) = 完美的 Session 管理方案
28. ★ Amazon Cognito lets you add user sign-up, sign-in, and access control to your web and mobile apps quickly and easily.
    1. Scales to millions of users
    2. Supports sign-in with **social** identity providers, and enterprise identity providers via **SAML 2.0 and OpenID Connect**.
29. 👀 A **launch template** is a template that an Auto Scaling group uses to launch EC2 instances.
    1. When you create a launch template, you specify information for the instances
    2. such as the ID of the **Amazon Machine Image (AMI)**, the instance type, a key pair, one or more security groups, and a block device mapping.
30. 👀 You can specify your launch template with multiple Auto Scaling groups.
    1. However, you can only specify **one** launch template for an Auto Scaling group at a time
    2. and **you can't modify a launch template after you've created it.**
31. 👀 Therefore, if you want to change the launch template for an Auto Scaling group
    1. you must create a template
    2. and then **update** your Auto Scaling group with the new launch template.
32. ★ To improve the **scalability** of your distributed system, you can add an **Auto Scaling group** to your EC2 instances.
33. ★★ 經典高可用架構（Task Queue Pattern）:
    1. **EC2 instances** = 消費者，從 SQS 取出訊息並處理
    2. **Auto Scaling 觸發**：監控 SQS queue size，隊列長 → scale out、隊列短 → scale in
34. 👀 Predictive Scaling 假設 Auto Scaling Group 內所有 EC2 實例能力相同，才能準確預測需要多少台機器。
    1. 這是一個邊界知識，考試優先度不高
35. ★ Step Scaling Policy：
    1. 你要明確定義每個區間要做什麼
    2. 可以立即回應多個告警（即使前一次擴展還在進行中）
    3. 適合流量**變化劇烈**的場景
36. ★ 直白說：這題在考你理解「**高可用架構 = 多 AZ + Auto Scaling + ELB**」的**標準組合**
37. ★★ In Auto Scaling, the following statements are correct regarding the **cooldown period**:
    1. It ensures that the Auto Scaling group does not launch or terminate additional EC2 instances before the previous scaling activity **takes effect**.
    2. Its **default** value is **300 seconds**.
    3. It is a **configurable** setting for your Auto Scaling group.
38. (已移除)
39. ★ Step Scaling Warm-up Period
     1. 作用：新實例啟動後，**不立即計入** metrics，避免誤判需求
     2. Warm-up 防止因新實例尚未就緒而過度擴展
     3. Step Scaling **專屬**設定 (Target Tracking 用 Default Cooldown)
     4. 預設值是 0 秒（無 Warm-up），需要手動設定
40. ★ DynamoDB 的 throughput（吞吐量）是**平均分配到各個 physical partition**
    1. 如果 partition key 設計不當會發生什麼？
41. ★★ 壞例子：用 country 當 partition key（台灣用戶佔 80%）
    - **80% 請求打到同一個 partition → 只能用 250 RCU**
    - 其他 partition 閒置 → 浪費 750 RCU
    - 結果：throttling（被限流）
42. ★★ 好例子：用 userId 當 partition key（**high-cardinality**）
    - 請求平均分散到 4 個 partition
    - 每個 partition 都在工作 → 完整利用 1000 RCU
43. ★★ **High-cardinality** attributes（高基數屬性）：
    1. 意思：有很多不同值的欄位
    2. 例子：userId、orderId、timestamp + randomId
    3. 反例：status（只有 active/inactive）、gender、country
44. ★ 延伸知識（考試會考）
    1. Composite key：partition key + sort key 組合可以增加靈活性
    2. Write sharding：在 partition key 加隨機 suffix（例如 `userId#random(1-10)`）進一步分散流量
    3. GSI（**Global Secondary Index**）：如果 primary key 無法改，可用 GSI 換個 partition key
45. ★ 直白講：partition key 就是決定「資料放哪一櫃」的依據，櫃子越多、分得越均勻，系統跑越快。**選高基數欄位 = 讓 DynamoDB 自動幫你 load balance**。
46. ★★ VPC 內的應用如何安全存取 DynamoDB（避免走公網）
    1. **解決方案：Gateway Endpoint**
    2. 運作流程：EC2 (private subnet) → VPC route table (自動更新 DynamoDB CIDR) → Gateway Endpoint → DynamoDB (走 AWS 內網)⭐️
47. ★★ Gateway Endpoint vs Interface Endpoint 比較⭐️
    1. **Gateway Endpoint**：路由層級，**改 route table，免費**
    2. **Interface Endpoint**：網路層級，加 ENI，要錢
48. ★ DynamoDB Streams 處理敏感資料的考試判斷標準
    1. **適合用途**：複製、備份、ETL、通知 ✅
    2. **不適合用途**：資料驗證、匿名化 ❌（太晚了）
    3. **記憶點**：Streams = after write，敏感資料要 before write 處理
49. 👀 ❌ 錯誤原因：題目要求 NoSQL database，**Redshift 是 relational database（data warehouse）**
50. ★★ Kinesis Data Streams + Lambda 做即時資料脫敏
    1. **標準套路**：AWS 處理敏感資料的推薦架構
    2. **運作流程**：Kinesis 負責接收串流資料 → Lambda 在資料「transit（傳輸中）」時就進行轉換/匿名化 → 確保資料在落地 DynamoDB 前就已經處理好
    3. **串流 + 即時處理 = Kinesis + Lambda**⭐️
51. ★★ DynamoDB Hot Partition 問題的核心原理
    1. **分散機制**：DynamoDB 會**根據 partition key 把資料分散儲存到不同的物理分區**，每個分區分到的 I/O 容量是平均的
    2. **問題成因**：如果 partition key 設計不好，導致某些分區被大量存取，其他分區閒置
52. ★ DynamoDB throughput(吞吐量)問題 → 請認明 **DynamoDB Auto Scaling**
53. 😇 DynamoDB On-Demand 計費模式
    1. **特點**：按實際請求數付費，無需預先設定容量
    2. **缺點**：成本較高(是 provisioned 的 2-3 倍)
54. 😇 DynamoDB Provisioned 計費模式
    1. **特點**：預先設定 Read/Write Capacity Units (RCU/WCU)
    2. **限制**：超過容量就會被 **throttle**
55. ★ DynamoDB Global Secondary Index (GSI) 的 Auto Scaling⭐️
    1. **獨立容量**：GSI 有自己的 RCU/WCU，**獨立**於 base table
    2. **場景示例**：Base table: 100 RCU/WCU、GSI-1: 50 RCU/WCU、GSI-2: 200 RCU/WCU ← 各自可單獨設定 Auto Scaling
    3. **優勢**：查詢集中在某個 GSI 時，不會影響其他 index 或 base table 的容量
56. 👀 Lambda 是唯一「**沒有內建 caching 功能**」的服務⭐️
    1. 記憶點：**Lambda = 無狀態 = 無內建 cache**
57. ★★ CloudFront + S3 分發靜態檔案的典型用法
    1. **題目關鍵詞**："distribute globally" + "static files" → CDN 的標準應用
    2. **運作流程**：CloudFront 在全球 **Edge Location** 快取 S3 檔案，用戶從最近的節點下載，大幅降低延遲
58. ★★ 創建 Global Table 的**前置條件**：啟用 DynamoDB Streams
    1. 必須開啟 **Streams** 功能才能建立 Global Table
    2. **Global Table 需要透過 Streams 來捕捉資料變更，並複製到其他 region。**
59. ★ Kinesis Data Streams 的選擇優勢
    1. **天生為串流設計**：資料持續流入，多個應用程式可同時消費
    2. **Replay 功能**：保留資料 24 小時到 365 天，可重新讀取
    3. **高吞吐量**：每秒處理 GB 級別資料沒問題
    4. **多消費者**：不同應用可各自處理同一份資料流
60. ★ RDS Snapshot 的考試重點
    1. **長期保存場景**：題目提到「長期保存」、「超過 35 天」、「合規要求」→ 選 Snapshot
    2. **Snapshot 的持久性**：RDS 刪除時自動備份會一起消失，但手動快照會保留
61. 😇 S3 **不適合**大量小檔案的原因
    1. **成本問題**：每個物件都有固定成本，儲存 1 個 1GB 檔案 vs 1000 個 1MB 檔案價格相同，但小檔案有 1000 次 API 請求成本
    2. **API 計費**：PUT/GET 請求按次數收費，1 萬個 10KB 小檔案 = 1 萬次請求，API 費用可能比儲存費用還貴
62. 👀 DocumentDB 的限制分析
    1. **功能限制**：DocumentDB 是 AWS 的 MongoDB 相容服務，但沒有 Serverless 模式（只有 cluster 模式，需要自己管理節點數量）、也不支援 Global Database（只能單區域，頂多跨 AZ）
    2. **考試關鍵**：即使 DocumentDB 相容 MongoDB，但在「Serverless + Global Database」的硬需求下不符合
63. 👀 DocumentDB vs 其他選項的選型標準
    1. **正確答案是 DynamoDB**：題目說「migrate MongoDB」但沒說要「完全相容」，Serverless + Global Database 才是硬需求
    2. **實務取捨**：DynamoDB 是唯一同時滿足這兩點的選項，實務上雖需改寫程式，但這是唯一可行方案
64. ★ EBS 加密預設開啟的機制
    1. **帳號層級設定**：AWS 帳號層級可以強制開啟 EBS 加密
65. ★ EBS 加密的自動應用範圍
    1. **新建 EBS volume**：自動加密
    2. **Snapshot 複製**：從未加密 snapshot 複製時新 snapshot 會被加密
    3. **Launch instance**：掛載的 EBS 也會自動加密
66. ★ 既有 EBS volume **無法直接加密**
    1. **限制**：舊 volume 無法直接加密
    2. **唯一作法**：建立 snapshot → 複製 snapshot（選擇加密）→ 從加密 snapshot 建立新 volume
67. 👀 EBS 只支援**對稱加密 key**（symmetric key）
    1. 非對稱 key (asymmetric) 用於簽章、驗證
    2. 不能加密 EBS
68. (刪除)
69. ★★ EBS volume 的可用性與複製特性
    1. **可用性：自動在同一 AZ 內複製**（防單點故障）⭐️
    2. **掛載限制**：只能掛載到**同 AZ** 的 EC2⭐️
70. ★ EBS Multi-Attach 限制
    1. **支援 volume type**：僅限 **io1/io2 (Provisioned IOPS SSD)**
    2. **實例要求**：必須**同 AZ** + Nitro-based instance
    3. **其他限制**：其他 volume type **不支援** Multi-Attach
71. 👀 EBS volume 的彈性調整
    1. **可調整項目**：Volume type、大小、IOPS 可線上修改
    2. **停機需求**：無需停機
72. (刪除)
73. ★ EBS Snapshot 的跨 region 備份方案
    1. **跨 region 備份**：需手動建立 Snapshot → 複製到其他 region
74. ★★ EBS Snapshot 存放位置的考試陷阱
    1. **錯誤選項**：Amazon RDS ❌
    2. **正確答案**：Amazon S3 ✅
    3. **核心概念**：EBS snapshot **自動存到 S3**（跨 AZ 冗餘），不是儲存在資料庫
75. 😇 EBS 加密金鑰來源
    1. **支援的 key 類型**：AWS-managed keys (AWS 代管) ✅ 、Customer-managed keys (你在 KMS 建立) ✅
    2. **加密位置**：EC2 host
    3. **傳輸保護**：EC2 ↔ EBS 傳輸時也加密（Data at rest 加密）
76. 👀 EBS 加密金鑰選擇的考試要點
    1. **共同點**：兩種 key 都可以用於 EBS 加密
    2. **AWS-managed 特點**：自動管理、免維護
    3. **Customer-managed 特點**：可控 rotation、權限、audit log
77. (刪除)
78. ★ 自動化 EBS snapshot 管理：Amazon Data Lifecycle Manager (DLM)
    1. **自動建立 snapshot**：定期備份
    2. **自動刪除過期 snapshot**：省成本
    3. **設定保留政策**：合規需求
79. 😇 Aurora Global Database 與 DynamoDB Global Tables 的全球複製方案
    1. **功能相同**：都能做到全球資料的複製和同步
    2. **基礎限制**：本身都是**以 Region 為基礎**，只是有全球同步的選項
    3. **應用場景**：考試遇到需要全球資料同步的需求，可考慮這兩個服務
80. ★ 大量 Linux servers 共享檔案存取需求分析：EFS
    1. **題目關鍵字**：「1000 Linux servers」+「rapidly changing data」= EFS
    2. **多台機器同時讀寫**：EFS 可以，EBS 不行
    3. **POSIX 文件系統語義**：EFS 提供，S3 不提供
    4. **頻繁變更需求**：適合 EFS，不適合 S3 歸檔
81. 😇 S3 不適合快速變更資料的原因分析
    1. **表面特性**：S3 提供與 EFS 相同等級的高可用性和高擴展性
    2. **核心限制**：不適合儲存快速變更的資料，主要用於歸檔和靜態內容
82. 👀 FSx for Windows File Server
    1. **連接能力**：可以連接到多個 EC2 實例
    2. **作業系統限制**：僅限於 Windows OS
83. ★ **考試重點**：當有大量並發存取需求時，如何正確配置 EFS
    1. **正確答案**：EFS + Provisioned Throughput + Max I/O
    2. **錯誤選項**：EFS + Bursting Throughput + General Purpose
84. 😇 EFS Throughput Mode 選擇標準
    1. **Provisioned**：**高頻**存取，資料量小
    2. **Bursting**：資料量大，**偶爾**存取
85. 😇 EFS Performance Mode 選擇標準
    1. **Max I/O**：數百個以上連線(高並發)
    2. **General Purpose**：一般應用
86. 😇 ELB Target Unhealthy 問題的判斷標準
    1. **常見原因**：Health check path 不存在、Health check port 錯誤、Timeout/Interval 設定不合理
    2. **解題原則**：直接了當，這種題別想太複雜
87. 👀 ALB **健康檢查**支援範圍
    1. 只支援 HTTP/HTTPS 健康檢查
    2. **不支援** TCP 層級檢查
88. ★ WAF **Rate-Based Rule** 的用途與場景
    1. 場景：單一 IP 在短時間內發送大量請求（DDoS、爬蟲、暴力破解）
    2. 解法：使用 AWS WAF 的 Rate-Based Rule 限制請求頻率
89. 😇 WAF **Regular Rule** 的用途與場景
    1. **作用原理**：符合條件就阻擋
    2. **常見封鎖目標**：特定 IP、地區、**User-Agent**
90. ★ CloudWatch Application Insights 的核心功能
    1. **主要用途**：為**應用程式**和底層 AWS 資源提供**可觀測性** (observability)
    2. **自動化能力**：自動設定最佳監控器，持續分析資料以偵測應用程式問題
91. 😇 CloudWatch Application Insights 的考試情境
    1. **考試場景**：需要分析 ALB 流量模式並快速排查問題
    2. **解題思路**：先取得原始資料，再自動化分析與問題偵測
92. 😇 ALB Access Logs 的資料收集機制
    1. **啟用方式**：Enable access logs on ALB
    2. **記錄內容**：來源 IP、請求路徑、回應時間、狀態碼等
    3. **儲存位置**：資料會自動存到 S3（壓縮格式）
93. 😇 CloudWatch Application Insights 整合 ALB logs 的自動化分析
    1. **核心功能**：自動分析這些 log，找出異常流量模式
    2. **視覺化呈現**：產生視覺化 dashboard
    3. **效率提升**：減少手動翻 S3 log 的時間
94. ★ CloudTrail 的監控範圍與定位
    1. **監控對象**：記錄「**誰對 AWS 做了什麼操作**」，不是「應用程式的流量」
    2. **核心功能**：AWS **API 呼叫**的稽核追蹤 (audit trail)
95. ★ CloudTrail 記錄的具體操作類型
    1. **資源管理操作**：誰 (IAM user/role) 建立了 EC2、誰刪除了 S3 bucket
    2. **安全設定變更**：誰修改了 Security Group 規則
    3. **資料庫操作**：誰啟動了 RDS snapshot
96. ★ EventBridge 的定位與核心概念
    1. **服務定位**：Event Bus (事件匯流排)
    2. **技術類比**：等同於 message queue / event broker (類似 RabbitMQ、Kafka)
    3. **核心用途**：用來「**當某件事發生時，觸發其他動作**」⭐️
97. ★ EventBridge 的典型應用場景⭐️
    1. **EC2 狀態監控**：當 EC2 instance 狀態改變 → 發送通知
    2. **S3 檔案處理**：當 S3 有新檔案上傳 → 觸發 Lambda
    3. **負載均衡監控**：當 ALB target 變成 unhealthy → 執行某個動作
98. ★★ ALB 的 Layer 7 路由能力（考試必考）
    1. **與 NLB 最大區別**：ALB 具備 **Layer 7 路由能力**
    2. **路由判斷依據**：可根據多種 HTTP 層級條件進行智慧路由
99. ★ ALB Layer 7 路由的具體條件⭐️
    1. **域名路由**：Host header (api.example.com、admin.example.com)
    2. **路徑路由**：Path (`/users/*`、`/orders/*`)
    3. **方法與參數路由**：HTTP method (GET、POST)、Query string (?version=v2)
    4. **進階路由**：Source IP (特定 IP 走特定 target)、HTTP headers (自定義 header)
100. ★ ALB vs NLB 的 gRPC 支援差異（考試陷阱題）
     1. **ALB 支援 gRPC**：可以處理 gRPC 協定的應用程式
     2. **NLB 不支援 gRPC**：只能處理純 TCP/UDP 流量
     3. **考試陷阱**：題目會用「UDP」、「TCP」、「Layer 4」等字眼誘導你選 NLB，但**若涉及 gRPC 必須選 ALB**
101. ★★ ELB Cross-Zone Load Balancing 的運作機制
     1. **基本概念**：Load balancer nodes 負責將客戶端請求分發到已註冊的 targets
     2. **啟用 Cross-Zone**：每個 load balancer node 會將流量分散到**所有啟用 AZ** 中的已註冊 targets
     3. **停用 Cross-Zone**：每個 load balancer node 只會將流量分散到**同一 AZ** 內的已註冊 targets
102. ★★ Cross-Zone Load Balancing 的啟用時機判斷
     1. **適合啟用場景**：各 AZ 的 EC2 數量差異很大（如 2 vs 8 的分布）、需要確保所有機器負載一致、使用 ALB（免費）
     2. **考試重點**：理解不同 AZ 間實例數量不均時的流量分配策略
103. ★ WAF (Web Application Firewall) 的核心功能
     1. **防護層級**：專門**保護應用層**（Layer 7）流量，阻擋惡意 HTTP/HTTPS 請求
     2. **開發者類比**：就像在 API 前面加一層 middleware，根據規則決定請求能否進入
104. 😇 AWS Firewall Manager 的定位與功能
     1. **服務定位**：中央管理工具，用來在多個帳號/多個資源上統一部署和管理 WAF/Shield 規則
     2. **開發者類比**：WAF 是你寫的 middleware 防護邏輯，Firewall Manager 是 Infrastructure as Code 工具（像 Terraform），讓你一次把這個 middleware 部署到 100 台機器上
105. ★ Amazon Aurora 的核心特性
     1. **資料庫相容性**：相容 MySQL 和 PostgreSQL
     2. **儲存系統**：分散式、容錯、自我修復的儲存系統，自動擴展至 128TB
     3. **高可用架構**：最多 15 個低延遲讀副本、支援 3 個 AZ 複製
     4. **備份與復原**：可用時間點復原、**持續備份到 S3**
106. 😇 Amazon Neptune 是一個快速、可靠、完全管理的**圖形資料庫服務**，適合打造**社交網路、推薦引擎和詐欺偵測**應用程式。
107. 😇 Amazon DocumentDB 是一個完全管理的**文件資料庫服務**，與 MongoDB 相容，適合用於內容管理、目錄和即時分析等應用場景。
108. 👀 Amazon S3 is indeed a key-value store! (where the key is the full path of the object in the bucket)
109. ★ Amazon Redshift 的核心特性與應用場景
     1. **服務定位**：AWS 的資料倉儲服務，專門為 **OLAP** (分析型查詢) 設計
     2. **Columnar Storage**：讀取特定欄位時效率極高，無需掃描整個 row
     3. **數據規模**：能處理 PB 級資料
     4. **BI 工具相容**：原生支援 QuickSight、Tableau 等可直接連接
110. ★★ 高可用 (HA - High Availability) 的特性⭐️
     1. **範圍**：同一 Region
     2. **切換方式**：自動切換
     3. **架構**：多個可用區域 (Multi-AZ)
111. ★★ 災難恢復 (DR - Disaster Recovery) 的特性
     1. **範圍**：跨 Region
     2. **復原方式**：手動或自動復原
     3. **備份工具**：快照 (Snapshot) / 備份 (Backup)
112. ★★ Multi-AZ 解決的是高可用性（High Availability），不是災難恢復（Disaster Recovery）
113. ★★ 災難恢復的核心思維：資料要能在另一個 Region 復原
114. 👀 Redshift 的兩個功能
     1. **題目關鍵詞**：「forces traffic through your VPCs」
     2. **Enhanced VPC Routing**：強制所有 COPY/UNLOAD 流量走 VPC（可控管路由、監控）
     3. **Redshift Spectrum**：只是查詢 S3 資料的功能，不涉及「強制走 VPC」
115. ★ AWS Glue 的核心功能
     1. **定位**：完全管理的 ETL (Extract, Transform, Load) 服務
     2. **主要特性**：可在幾次點擊內建立、執行和監控 ETL 工作，無需自行維護伺服器
116. ★ Amazon EMR (Elastic MapReduce) 的核心特性
     1. **原生支援框架**：Spark、Hive、Presto、Hadoop
     2. **服務特點**：Managed Service，AWS 幫你管底層叢集
     3. **成本效益**：用完即關，成本可控
     4. **易用性**：資料工程師熟悉的工具，直接上手
117. ★ Amazon QuickSight 的核心特性
     1. **服務定位**：AWS 的 **BI 工具**
     2. **主要功能**：專門做 **dashboard** 和報表
     3. **原生整合**：原生連接 Redshift（一鍵設定）
     4. **架構優勢**：Serverless，DevOps 不用管基礎設施
118. 👀 Glue Job Bookmarks 用來追蹤 ETL job 已經處理過的資料，避免重複處理。
119. 😇 Kinesis Data Analytics 可以直接讀取 Kinesis Data Streams 的資料，進行即時分析和處理。
120. ★ AWS Machine Learning 服務列表
     - **Rekognition**：人臉偵測、物體標籤識別、名人辨識
     - **Transcribe**：音訊轉文字（例如字幕生成）
     - **Polly**：文字轉語音
     - **Translate**：多語言翻譯
     - **Lex**：建構對話式機器人（Chatbots）
     - **Connect**：雲端聯絡中心
     - **Comprehend**：自然語言處理 (NLP)
     - **SageMaker**：適合每位開發者和資料科學家的機器學習平台
     - **Kendra**：ML 驅動的搜尋引擎
     - **Personalize**：實時個性化推薦
     - **Textract**：文件中的文字和資料偵測
121. 😇 Amazon Lex 整合 ASR (語音轉文字) 和 NLU (**意圖辨識**) 於一個服務，適合建構對話機器人○
122. 👀 Amazon Polly 的進階語音控制功能
     1. **Pronunciation Lexicons (發音字典)**：自訂特定詞彙發音（例：EC2 → 讀成 Elastic Compute Cloud）
     2. **SSML (Speech Synthesis Markup Language)**：控制語音表達方式（強調、停頓、呼吸聲、耳語）
123. 👀 Amazon Comprehend Medical 的核心功能
     1. **用途**：AWS 專門處理醫療文字的 NLP 服務，為非結構化醫療文檔設計
     2. **功能特性**：自動識別醫療實體（藥物、疾病、劑量、症狀）、自動偵測 PHI（姓名、日期、地址等敏感資訊）
     3. **合規性**：HIPAA eligible（符合醫療合規）
124. ★★ Infrequent Access 儲存類別的考試陷阱
     1. **IA 的定位**：Infrequent Access（不常存取）= 不常用但隨時要拿
     2. **S3 One Zone-IA 的分類**：是 Infrequent Access 儲存類別，不是 Archive（封存）類別
125. ★★ Glacier 封存類別的選用原則
     1. **Glacier 的用途**：Glacier（封存）= 封存不急著用
     2. **選題標準**：題目若要求 backup/archive，應選 Glacier 系列，不是 IA 系列
126. ★ Amazon Data Lifecycle Manager (DLM) 的核心功能
     1. **用途**：自動化管理 EBS snapshots 的生命週期，無需手動建立或寫 Lambda 排程
     2. **自動建立**：定期自動快照
127. 😇 DLM 的保留與刪除管理
     1. **保留政策**：依政策保存（如：保留 7 天、30 天）
     2. **自動刪除**：過期快照自動清除，省成本
128. 😇 AWS Backup 與 DLM 的定位區別
     1. **AWS Backup 的特性**：多服務集中備份平台（EBS、RDS、DynamoDB、EFS 等）
     2. **應用場景**：適合跨服務統一備份策略
129. ★ Instance Store-backed AMI 的特性
     1. **Root device**：Instance Store（臨時儲存）
     2. **資料生命週期**：資料只存在於 instance 生命週期內，instance 終止 → 資料自動刪除
130. ★ EBS-backed AMI 的特性
     1. **Root device**：EBS volume
     2. **資料持久性**：Instance 終止後，EBS 可保留（看設定），資料持久性高
131. 😇 AMI 儲存類型的白話理解
     1. **類比概念**：AMI 就像安裝光碟，EBS-backed 裝到「外接硬碟」（可拆卸）、Instance Store-backed 裝到「內建暫存」（不可拆）
     2. **現狀**：現在 99% 都用 EBS-backed，Instance Store-backed 幾乎淘汰
132. ★ EBS Snapshot 儲存位置的常見誤解
     1. **錯誤說法**：EBS snapshot 不會寫入 Amazon RDS
     2. **正確位置**：EBS snapshot 儲存在 Amazon S3（不是 RDS）
133. ★ EBS 複製範圍的常見誤解
     1. **錯誤觀念**：EBS 會跨 region 複製 ❌
     2. **正確觀念**：EBS volume 只在單一 AZ 內自動複製多份 ✅
134. ★★ EBS 可用性與複製特性總結
     1. **基本綁定**：EBS 綁定單一 AZ，不跨 AZ、不跨 region
     2. **跨域保護**：要跨 AZ/region 保護資料 → 需手動建 snapshot 並複製
135. ★ EBS 線上修改支援與限制
     1. **可修改項目**：Volume size（只能增加）、Volume type、IOPS / throughput
     2. **修改特性**：無需停機，修改後需等 6 小時才能再次修改
     3. **考試重點**：EBS 支援熱修改，不用停機
136. ★★ EBS SSD 系列（注重 IOPS）
     1. **gp3/gp2 (General Purpose)**：通用型，CP 值最高，預設選項
     2. **io2/io1 (Provisioned IOPS)**：需要穩定高 IOPS 的場景（**資料庫**）
137. ★ EBS HDD 系列（注重 Throughput）
     1. **st1 (Throughput Optimized)**：大量順序讀寫（Big Data、Log）
     2. **sc1 (Cold HDD)**：最便宜，不常存取的冷資料
138. 👀 EBS 已淘汰的類型
    - Magnetic volumes（上一代產品，考試可能還會出現但不要選）
139. ★ Custom Identity Broker 的運作與應用
     1. **完整流程**：員工 → 企業認證系統 (LDAP/AD) → Identity Broker (驗證身份 + 呼叫 STS AssumeRole + 返回憑證) → 臨時憑證 → 存取 AWS
     2. **適用情況**：企業用 LDAP/AD 等非 SAML 2.0 系統、題目說「existing corporate identity system」、需要「programmatic access」給內部員工
140. ★ IAM 認証整合方案的選擇指南
     1. **SAML 2.0 相容**：直接用 IAM Identity Provider（不需要自己寫 broker）
     2. **外部用戶登入**：用 Cognito
     3. **企業舊系統**：Custom Identity Broker（過渡方案，優先考慮升級到 SAML 2.0 或 OIDC）
141. ★★ IAM User 建立後的預設狀態
     1. **有身份**：可以被識別 ✅
     2. **沒有權限**：不能做任何事 ❌
     3. **沒有憑證**：無法驗證身份 ❌
142. ★★ 透過 CLI/API 呼叫 AWS 的兩個必要步驟
     1. **Access Key**：讓程式能用這組 Key 證明「我是這個 User」（身份驗證）
     2. **IAM Policy**：賦予權限，允許存取 S3、DynamoDB、Lambda 等服務（授權）
143. ★ S3 存取控制方案的選擇建議
     1. **不推薦**：ACL（太細碎難管理）
     2. **推薦方案一**：Bucket Policy（大部分情況）
     3. **推薦方案二**：IAM Role（需要 Console 或臨時憑證時）
144. ★ IAM Role vs Bucket Policy 的關鍵差異
    - **只有 IAM Role 能讓跨帳號用戶用 Console 登入查看 S3**（Bucket Policy 只支援程式存取）
145. ★ AWS 存取方式的定義與區別
     1. **Console（AWS Management Console）**：用瀏覽器登入 <https://console.aws.amazon.com> 看到的圖形化介面
     2. **Programmatic access**：用 CLI、SDK、API 呼叫 AWS（需要 Access Key）
     3. **Console access**：人類用瀏覽器點點點操作 AWS（需要登入帳號）
146. ★ GuardDuty 威脅偵測與判斷標準
     1. **選題關鍵詞**：出現「malicious」、「suspicious」、「threat」→ 選 GuardDuty
     2. **服務定位**：S3 威脅偵測服務
     3. **偵測能力**：已知惡意 IP 存取 S3、Bucket Policy/ACL 被改成公開、異常 API 呼叫模式（如暴力掃描權限設定）
147. ★ CloudTrail 與 GuardDuty 的核心差異
     1. **CloudTrail 的用途**：記錄 API 呼叫（稽核、合規、事後調查）
     2. **GuardDuty 的用途**：分析行為模式進行即時威脅偵測
     3. **定位區別**：CloudTrail 只是「錄影機」，記錄誰做了什麼，但不會主動告訴你「這是惡意行為」
     4. **聯動關係**：GuardDuty 會分析 CloudTrail 的日誌，自動標記可疑活動
148. ★★ AWS Security Token Service (STS) 的核心功能
     1. **服務定位**：發「**臨時通行證**」的服務，讓你可以跨 AWS 帳號存取資源，或給外部使用者臨時權限
     2. **選題關鍵詞**：跨帳號存取（Cross-account access）、臨時權限（Temporary credentials）、AssumeRole、第三方存取 AWS 資源（例如：外部廠商需要臨時存取你的 S3）
149. ★★ 跨帳號存取的定義與實務場景
     1. **白話定義**：讓 A 帳號的使用者，可以操作 B 帳號的 AWS 資源
     2. **典型組織架構**：母公司帳號 (Prod) → 子公司 A 帳號 (Dev) / 子公司 B 帳號 (Test)
     3. **應用場景**：Dev 帳號的工程師需要部署到 Prod 帳號 → 跨帳號存取
150. ★★ 跨帳號存取的考試判斷標準
     1. **選題關鍵詞**：「不同 AWS 帳號」、「帳號 A 存取帳號 B」
     2. **解決方案一**：STS + AssumeRole（跨帳號臨時存取）
     3. **解決方案二**：Resource-based Policy（例如 S3 Bucket Policy 直接開放給另一個帳號）
151. ★ AssumeRole 的定義與考試判斷
     1. **選題關鍵詞**：看到 AssumeRole → 答案 99% 是 STS
     2. **白話定義**：一個 API 動作，讓你「扮演」另一個 IAM Role，取得該 Role 的權限
152. ★★ CloudWatch Metric Stream 的三大特徵（SAA 必考）
     1. **特徵一**：Near real-time（不是批次，是串流）
     2. **特徵二**：必須經過 Kinesis Firehose（直接連線標配）
     3. **特徵三**：可直接送到 3rd party（Datadog、Splunk、New Relic）
153. ★ CloudWatch Contributor Insights 的核心功能
     1. **用途**：找出「Top-N 貢獻者」問題，分析「誰貢獻最多流量/請求」
     2. **應用場景**：題目要找「top IP addresses」，這是典型的應用
154. 😇 EventBridge Archive and Replay
     1. **Archive**：自動把 events 存到 S3（AWS 管理），長期保存事件
     2. **Replay**：需要時重新發送歷史 events 到 dev 環境進行測試
155. ★★ SAML 2.0-Based Federation 的應用場景
     1. **問題情境**：公司已有 Microsoft Active Directory (AD)，員工想用公司帳號登入 AWS
     2. **解決方案**：SAML 2.0-Based Federation
156. ★ 選擇 SAML Federation 的三大原因
     1. **已有 AD 基礎**：公司用 Microsoft AD 管理員工帳號
     2. **SSO 需求**：員工想用「同一組」公司帳號登入 AWS，不想再記一組 IAM 帳密
     3. **集中管理**：IT 部門只要在 AD 管理員工權限，AWS 會信任 AD 的認證結果
157. ★★ Lambda 適合的場景分析
     1. **間歇性、事件驅動的工作負載**：流量不可預測、想省錢且流量不高
     2. **執行時間限制**：執行時間短（< 15 分鐘）
158. ★ Lambda 不適合的場景分析
     1. **需要持續運行**：改用 EC2/ECS
     2. **執行時間超過 15 分鐘**：改用 Step Functions + Lambda 或 ECS
     3. **需要固定 IP**：改用 VPC + NAT Gateway，但複雜且貴
159. ★ S3 Event 觸發 Lambda 的應用場景
     1. **情境**：穿戴裝置健康數據 → S3 → 處理分析
     2. **為什麼選 Lambda + Python**：事件驅動（新數據上傳自動觸發）、無需管理伺服器、Python 生態系豐富（pandas、numpy）、按需付費
160. ★ S3 Event → Lambda 的考試重點
     1. **推送模式**：S3 主動呼叫 Lambda，不是拉取模式
     2. **檔案過濾**：支援 prefix/suffix 過濾特定檔案
     3. **Event 物件內容**：包含 bucket name、object key、檔案大小等完整資訊
161. ★ Kinesis Data Streams + Lambda 非同步處理架構
     1. **問題場景**：使用者上傳檔案，但處理耗時，不想讓使用者等待
     2. **核心解決方案**：請求立即寫入 Kinesis Data Streams → 回傳成功訊息給使用者（不等處理）
     3. **非同步流程**：Lambda 非同步從 Streams 讀取資料並處理 → 完成後寫入 S3 儲存
162. ★★ Lambda **呼叫類型**（Invocation Type）的兩種方式
     1. **同步（Synchronous）**：呼叫者等待結果，例如 API Gateway → Lambda
     2. **非同步（Asynchronous）**：呼叫後不等結果，例如 S3 事件 → Lambda
     3. **重點**：呼叫類型由事件源決定，無法人為改變
163. ★★ Kinesis Data Streams 與 Lambda 的特殊運作機制
     1. **運作方式**：使用 Event Source Mapping，Lambda **主動 poll（輪詢）** Kinesis stream
     2. **自動功能**：批次處理記錄、管理 checkpoint（進度追蹤）、自動處理 retry
164. ★★ Stream-based 服務的 Lambda 整合模式
     1. **常見服務**：DynamoDB Streams、SQS、Kinesis Data Streams
     2. **核心特點**：都是 Lambda 主動 poll，而非被動接收
     3. **考試陷阱**：很多人誤以為 Kinesis 是「推送」給 Lambda，實際上是 Lambda 去「拉取」
165. ★★ Lambda 事件源的判斷標準
     1. **Stream-based 服務**：= Lambda poll（主動拉取）
     2. **Event-based 服務**：= 推送給 Lambda（S3、SNS）
     3. **應用影響**：會直接影響架構設計中的權限配置和錯誤處理策略
166. ★★ Lambda@Edge = **在 CloudFront 的 edge location 執行 Lambda**
     1. **核心優勢**：在離用戶最近的地方處理請求，大幅降低延遲
167. ★ Lambda@Edge 的 4 個請求/回應介入時間點
     1. **Viewer Request**：使用者 → CloudFront
     2. **Origin Request**：CloudFront → Origin
     3. **Origin Response**：Origin → CloudFront
     4. **Viewer Response**：CloudFront → 使用者
168. ★ Lambda@Edge 的實務應用範例：動態圖片格式轉換
     1. **需求**：根據 User-Agent header 判斷裝置類型，回傳不同圖片格式
     2. **實現步驟**：CloudFront 收到請求 → Lambda@Edge 讀取 User-Agent → 修改 response header 或直接生成不同格式 → 回傳給使用者
     3. **格式差異**：手機 → WebP（小檔案）；桌面 → PNG/JPEG（原始格式）
169. ★ Lambda@Edge 的適用場景分析
     1. **適用**：需要在「邊緣」處理邏輯（降低延遲）、A/B testing、個人化內容、安全驗證（檢查 header、token）、URL 重寫、重導向
     2. **不適用**：需要長時間運算（Lambda@Edge 有執行時間限制）
170. ★ 記憶點：Lambda@Edge 的核心概念
     1. **類比**：想像在 CDN 的每個節點都放了一個小腦袋，可以做簡單判斷和修改，而不用每次都回 Origin
171. ★★ VPC 的定義與核心特性
     1. **定義**：VPC = 你在 AWS 上的私有網路區域
     2. **邏輯隔離**：你的網路與其他 AWS 用戶完全分開
     3. **完全控制**：IP 範圍（CIDR）、subnet、route table、gateway 都你說了算
     4. **混合雲支援**：可透過 VPN/Direct Connect 連回地端機房
172. ★★ VPC 的必懂核心元件
     1. **Subnet**：public（可上網）vs private（不可上網）
     2. **Internet Gateway**：讓 public subnet 連外網
     3. **NAT Gateway**：讓 private subnet 單向連外網（安全）
     4. **Route Table**：決定流量往哪走
     5. **Security Group**：instance 層級防火牆（stateful）
     6. **NACL**：subnet 層級防火牆（stateless）
173. ★ VPC 應用場景的判斷標準
     1. **需要隔離 + 安全**：用 VPC ✅
     2. **需要自訂網路**：用 VPC ✅
     3. **AWS 預設配置**：AWS 預設就給你一個 default VPC
174. 😇 VPC 考試高頻考點
     1. **常考連接方案**：VPC peering、Transit Gateway、PrivateLink
     2. **基礎地位**：VPC 是 AWS 網路的基礎，幾乎所有服務都跑在 VPC 裡
175. ★ ENI（Elastic Network Interface）的定義與作用
     1. **定義**：ENI = 虛擬網卡
     2. **Lambda 在 VPC 內的運作機制**：AWS 自動建立 ENI 連接 VPC
     3. **資源消耗**：每個 subnet/security group 組合需要 ENI，ENI 消耗 subnet 的 IP
176. ★ EC2ThrottledException 的發生原因與解決方案
     1. **發生原因**：Subnet IP 不足、ENI 配額達上限、Lambda 快速 scale 時建立 ENI 太慢
     2. **解決方案**：確保 subnet 有足夠 IP（至少保留幾十個）、用 `/24` 或更大的 CIDR、Lambda 會重用 ENI，不是每次執行都建新的
177. ★ Lambda + VPC 的核心記憶點
     1. **簡潔定義**：Lambda 要進 VPC = 需要網卡（ENI）= 需要 IP
     2. **常考場景**：Lambda 連 RDS、EFS、內部 API 都需要在 VPC 內
178. ★ Lambda 在 VPC 內 scale 失敗的第一大原因：ENI 配額達上限
     1. **原因分析**：VPC 有 ENI 數量限制，Lambda scale 時需要更多 ENI
     2. **考試重點**：當 Lambda 快速擴展時，可能因為 ENI 配額已滿而無法建立新 instance
179. ★ Lambda 在 VPC 內 scale 失敗的第二大原因：Subnet IP 不足
     1. **原因分析**：ENI 需要消耗 IP，Subnet CIDR 太小（如 /28 只有 16 個 IP）導致 IP 枯竭
     2. **考試重點**：小型 subnet 無法支撐 Lambda 的大規模 scale，造成限流
180. 👀 Lambda@Edge 是 Amazon CloudFront 的一個功能，讓您可以在應用程式的使用者附近運行代碼，從而提高性能並降低延遲。
181. ★ Athena vs Kinesis 的核心定位區別
     1. **Athena**：對 S3 上的**靜態資料**做 SQL 查詢 → batch processing / 事後查詢
     2. **Kinesis**：處理**即時串流資料** → 即時 real-time 應用（Data Streams/Analytics/Firehose）
     3. **考試判斷**：歷史資料/S3 分析 = Athena；real-time 流量 = Kinesis
182. ★ 記憶點：Athena vs Kinesis 的核心概念
     1. **Athena**：事後諸葛（查過去的資料）
     2. **Kinesis**：當下反應（處理現在的資料流）
183. 😇 Multi-AZ failover = CNAME 切換，不碰 IP
184. ★ Multi-AZ Failover 核心機制
     1. **實作方式**：CNAME 切換才是 failover 的方式
     2. **故障轉移流程**：故障前（your-db.xxx.rds.amazonaws.com → Primary DB (AZ-A)）→ 故障後（your-db.xxx.rds.amazonaws.com → Standby DB (AZ-B，升級為新 Primary)）
185. ★ Multi-AZ Failover 的優勢
     1. **應用層無感知**：connection string 不變，無需修改程式
     2. **自動 Promote**：Standby 被 promote 成新 Primary
     3. **容錯轉移時間**：通常 60-120 秒
186. ★★ Multi-AZ Failover 為什麼用 **DNS 切換**而不是 IP 切換（VPC 基礎架構理解）
     1. **技術限制**：IP 地址綁定 subnet，**subnet 無法跨 AZ**（AZ-A: subnet-1 → 10.0.1.0/24、AZ-B: subnet-2 → 10.0.2.0/24）
     2. **核心原理**：Primary 和 Standby 在**不同 subnet**，IP 不可能互換 → 只能用 DNS/CNAME 切換
187. 👀 RDS 不支援 Recovery Manager (RMAN，Oracle 恢復管理工具）的原因
     1. **管理方式**：RDS 是託管服務，**AWS 控制底層 OS 和備份機制**
     2. **RMAN 限制**：需要 OS level 存取權限、直接操作檔案系統、自行管理備份儲存
     3. **替代方案**：只能透過 RDS 提供的 **automated backup 和 snapshot**
188. 👀 Global Accelerator 在 RDS 題目中出現的考試陷阱
     1. **服務定位**：Global Accelerator 是**網路服務**，用於將使用者流量導向最近的 endpoint，降低延遲
     2. **與 RDS 無關**：完全不涉及資料庫性能提升
     3. **考題關鍵**：看到 Global Accelerator 出現在 RDS 題目 → **直接排除**（誘導選項）
189. ★ Read Replicas 的真實作用與常見誤解
     1. **核心功能**：Read Replicas **不會提升 Primary DB 的效能**
     2. **正確理解**：只是讓應用程式從 replica 讀取資料、分散讀取流量、降低 Primary 的負載
     3. **效能限制**：**Primary 的 IOPS、運算能力不變**（不是性能提升，是讀取分流）
190. ★★ Multi-AZ 同步複製的核心特性
     1. **同步複製本質**：Multi-AZ 使用 **synchronous（同步）** 複製，每次寫入都要等 standby 確認
     2. **實務意義**：寫入延遲較高（通常幾毫秒）、資料零遺失（主節點掛掉時 standby 100% 有最新資料）、自動容錯移轉（DNS 自動指向 standby）
191. ★★ RDS **IAM Database Authentication** 的三層雙向設定（缺一不可）
     1. **EC2 IAM Role**：允許呼叫 **rds-db:connect** 權限
     2. **RDS 啟用 IAM Auth**：開啟 IAM Database Authentication 功能
     3. **DB 建立 IAM User**：資料庫層級建立對應的 IAM 使用者帳號
192. ★★ RDS Microsoft SQL Server 連線加密的完整實現（兩步驟缺一不可）
     1. **伺服器端設定**：設定 **rds.force_ssl = true** + **重開 DB**（static parameter）
     2. **用戶端設定**：下載 **Amazon RDS Root CA 憑證** → 匯入伺服器 → 應用程式使用 **SSL 連線**
193. 😇 RDS 各引擎都支援 SSL/TLS 加密連線
     1. **支援的引擎**：**MySQL**、**PostgreSQL**、**MariaDB**、**Oracle**、**SQL Server**、**Aurora (MySQL/PostgreSQL)**
194. 😇 RDS 各引擎的 SSL/TLS 設定方式差異
     1. **MySQL/PostgreSQL**：使用 **require_secure_transport** parameter 進行設定
     2. **SQL Server**：使用 **rds.force_ssl** parameter 進行設定
     3. **Oracle**：透過 **option group** 進行設定
195. ★★ S3 Transfer Acceleration + Multipart Upload
     1. **Transfer Acceleration**：全球邊緣節點加速跨地區傳輸，速度提升 50-500%
     2. **Multipart Upload**：大檔案平行上傳，失敗可單獨重傳部分
     3. **組合應用**：跨區域 + 大量資料上傳時同時使用
196. ★★ S3 防誤刪：Versioning + MFA Delete
     1. **Versioning**：保留歷史版本、刪除只標記 delete marker、可復原
     2. **MFA Delete**：需 MFA 才能改變 versioning 狀態和永久刪除版本
     3. **組合用途**：基本防護用 Versioning，強化防護加上 MFA Delete
197. 😇 S3 One Zone-IA 的選擇邏輯
     1. **特性**：**可快速存取**（毫秒級同 S3 Standard）、**檔案可重建**（easily reproducible，單 AZ 掛無妨）、**成本降 80%**（vs Standard）
     2. **判斷標準**：檔案「可重建 + 需要快速存取」→ One Zone-IA 最佳解
198. 👀 S3 儲存類別選型的常見誤區
     1. **誤選情境**：很多人會選 **S3-IA (Intelligent-Tiering)** 或 **S3 Standard-IA**
     2. **錯誤原因**：Standard-IA 跨 3 個 AZ 成本較高（若檔案可重建則浪費）；Intelligent-Tiering 自動移動檔案但無法針對特定 prefix 處理
199. 👀 S3 儲存類別選型的 3 個關鍵問題
     1. **能重建嗎**：檔案遺失能復原 → 考慮 One Zone-IA（單 AZ）
     2. **多快要拿**：毫秒級 → IA；數分鐘 → Glacier；數小時 → Glacier Deep Archive
     3. **需差異化嗎**：不同檔案不同保留策略 → 用 Lifecycle Policy + prefix filter
200. ★ S3 儲存類別選型的考試判斷邏輯
     1. **選 Intelligent-Tiering**：存取模式「不可預測/不斷變化」、題目關鍵字 `unpredictable access patterns`、`unknown usage`
     2. **選 Lifecycle Policy**：存取模式「已知」、需要「針對不同檔案做不同處理」、題目明確說明時間點或分類邏輯
     3. **記憶點**：Intelligent-Tiering = **自動但無法客製**；Lifecycle = **手動但精準控制**
201. ★★ Lifecycle Policy 的核心功能：自動化資料生命週期管理
     1. **定義**：Lifecycle Policy = **規則引擎**，用於設定「條件」+「動作」
     2. **運作模式**：S3 自動執行，無需人工介入
202. ★ AWS Organizations + SCP 的多帳戶隔離架構
     1. **核心需求**：在同一個組織內隔離 Production 和 Development 環境，限制只有通過稽核的服務能在 Production 使用
     2. **AWS Organizations**：統一管理**多個 AWS 帳戶**的容器
     3. **Organizational Units (OUs)**：邏輯分組（Prod OU、Dev OU）
     4. **SCP (Service Control Policy)**：套用在 OU 層級的**權限邊界**，控制整個 OU 內所有帳戶能用什麼服務
203. 😇 SCP 的運作方式與權限控制機制
     1. **Prod OU 策略**：套用 SCP → 只允許已稽核的服務（例如：EC2、RDS、S3）
     2. **Dev OU 策略**：不套用限制 → 團隊自由實驗任何服務
     3. **權限邊界特性**：**即使 Prod 帳戶內的 IAM 使用者有 Admin 權限，也無法使用被 SCP 禁止的服務**
204. 👀 S3 與檔案系統的類比記憶法
     1. **物件層級**：`arn:aws:s3:::bucket-name` → 資料夾本身（可以 ls、rm -rf）
     2. **檔案層級**：`arn:aws:s3:::bucket-name/*` → 資料夾內的檔案（可以 cat、touch）
     3. **GetObject 操作**：讀取檔案，當然要加 `/*`
205. 👀 IAM Condition Key：aws:**RequestedRegion** 的用途
     1. **功能定義**：限制 **API 呼叫的目標區域**
     2. **應用場景**：例如限制使用者只能在特定 Region 建立或修改資源
206. ★★ EventBridge 權限組態的核心邏輯
     1. **權限判斷原則**：**誰主動 vs 誰被動決定 Policy 類型**
     2. **Lambda 作為目標**：EventBridge → Lambda（EventBridge 主動呼叫）使用 **Resource-Based Policy**（附加在 Lambda 上）
     3. **Kinesis Data Streams 作為目標**：EventBridge → Kinesis（EventBridge 需要推送權限）使用 **Identity-Based Policy**（附加在 EventBridge 的 IAM Role 上）
207. ★★ S3 Client-Side Encryption：使用 AWS KMS key
     1. **金鑰管理者**：AWS KMS 管理金鑰
     2. **AWS 能看到什麼**：AWS 知道你的 KMS KeyId，但看不到**原始資料**
     3. **風險評估**：金鑰遺失機率低，但 **AWS 有金鑰存取權限**
208. ★★ S3 Client-Side Encryption：使用 Client-side master key
     1. **金鑰管理者**：**你自己管理金鑰**（完全掌控）
     2. **AWS 能看到什麼**：AWS 完全看不到**金鑰和原始資料**
     3. **風險評估**：**金鑰遺失 = 資料永久無法解密**（零恢復可能）
209. 👀 S3 Client-Side Encryption 的核心概念
     1. **加密時機**：加密動作發生在**資料傳到 S3 之前**（端對端加密）
     2. **應用場景**：需要完全控制加密金鑰、確保 AWS 無法存取原始資料時使用
210. ★★ To enable client-side encryption, you have the following options:
     - Use an AWS KMS key.
     - Use a client-side master key.
211. ★★ 記住：S3 Server-Side Encryption 只支援 AES-256，沒有 AES-128 選項。
212. ★★ S3 Server-Side Encryption (SSE) 加密方案對比
     1. **SSE-S3**：**AES-256** 加密，**AWS 管理金鑰**
     2. **SSE-KMS**：**AES-256** 加密，**KMS 管理金鑰**
     3. **SSE-C**：**AES-256** 加密，**你提供金鑰**
213. 😇 S3 + CloudFront 靜態內容分發架構
     1. **核心概念**：S3 + CloudFront 是 AWS 標準的靜態內容分發方案
     2. **運作流程**：CloudFront 在全球 Edge Location 快取 S3 檔案，用戶從最近節點下載
214. ★★ SNS 常見考試組合
     1. **SNS + SQS (Fan-out pattern)**：一個訊息廣播給多個 queue，各自獨立處理
     2. **SNS + Lambda**：事件驅動架構，自動觸發多個 Lambda
     3. **CloudWatch Alarm → SNS → Email/SMS**：監控告警通知
215. ★★ SNS Fan-out Pattern（重要考點）
     1. **定義**：SNS Topic 發布 1 則訊息 → **複製並推送給多個端點**
     2. **典型架構**：
        - SQS Queue 1（訂單處理）
        - SQS Queue 2（庫存更新）
        - Lambda（發送 Email）
        - HTTP endpoint（第三方 webhook）
216. ★★ SNS Message Filtering（重要考點）
     1. **功能**：訂閱者可設定 **filter policy**，**只接收符合條件的訊息**
     2. **典型架構**：
        - SNS Topic（Quote Requests）
        - SQS Queue 1（filter: type="insurance"）
        - SQS Queue 2（filter: type="loan"）
        - SQS Queue 3（filter: type="mortgage"）
217. ★ SNS Message Filtering 的考試判斷標準
     1. **情境**：多個下游系統，但各自**只需要特定類型的訊息**
     2. **錯誤做法**：多個 SNS Topic（管理複雜）；所有訊息發給所有 SQS 再用 Lambda 過濾（浪費資源）
     3. **正確做法**：**1 個 SNS Topic + 多個 SQS + Filter Policy**
218. 😇 SNS Filter Policy 的實現細節
     1. **發布訊息時**：帶上 attributes（如 `type: "insurance"`, `amount: 50000`）
     2. **訂閱時設定 filter**：定義要接收的條件（如 `{"type": ["insurance"]}`）
     3. **SNS 自動分流**：根據 filter policy 決定推送給哪些訂閱者
219. ★ SNS Message Filtering 的實踐判斷
     1. **正確做法**：**Filter in SNS subscription**（訂閱層級過濾）✅
     2. **錯誤做法**：**Filter in SQS queue**（隊列層級過濾）❌ 太晚過濾，已經浪費資源
220. ★★ SQS Short Polling（預設模式）
     1. **ReceiveMessageWaitTimeSeconds**：0（預設）
     2. **行為**：只查詢部分 server，**立即回應**（無等待）
     3. **適用場景**：高吞吐量需求
     4. **缺點**：容易產生**空請求**（訊息未到時仍回傳），浪費成本
221. ★★ SQS Long Polling（成本優化模式）
     1. **ReceiveMessageWaitTimeSeconds**：> 0（最高 20 秒）
     2. **運作方式**：**等待訊息到達才回應**，如期間沒訊息就 hold 著
     3. **適用場景**：降低成本、減少空請求
     4. **優勢**：**減少 API 呼叫**，伺服器負擔更輕
222. `ReceiveMessageWaitTimeSeconds` 這個參數就是 Long vs Short Polling 的唯一差異。
223. Encryption in transit vs Encryption at rest
     1. **Encryption in transit(in-flight)**：資料在傳輸過程中加密（SSL/TLS）
     2. **Encryption at rest**：資料靜止存放時加密（AES-256、KMS）
224. API Gateway 有三種 Endpoint 類型
     1. **Regional endpoint**：部署在特定區域，適合內部或區域性應用
     2. **Edge-optimized endpoint**：使用 CloudFront 全球加速，適合公開 API
     3. **Private endpoint**：只能在 VPC 內存取，適合內部服務
225. ★ API Gateway + Lambda 的整合模式
     1. **同步呼叫**：API Gateway → Lambda（等待結果回傳）
     2. **非同步呼叫**：API Gateway → SQS → Lambda（解耦，適合長時間處理）
     3. **考試判斷**：需要即時回應用同步，需要排隊處理用非同步
226. ★★ Server-Side Encryption (SSE) 的核心概念
     1. **加密時機**：資料寫入 AWS 服務（S3/EBS/RDS 等）時，AWS 在**伺服器端自動加密**
     2. **解密時機**：讀取資料時，AWS 在**伺服器端自動解密**後才回傳給你
     3. **使用者角色**：只負責指定加密金鑰，不用自己寫加解密程式
227. ★★ AWS KMS 的兩種金鑰類型
     1. **AWS Managed Keys**：AWS 自動產生，名稱固定（如 aws/s3、aws/rds），無法手動建立，適合基礎加密需求
     2. **Customer Managed Keys (CMK)**：需要手動建立，當需要控制權限、輪替政策、刪除時機時使用
228. 😇 KMS 金鑰自動輪換
     1. **啟用方式**：在 CMK 設定中啟用自動輪換功能
     2. **只有** Customer Managed Keys (CMK) 支援自動輪換，AWS Managed Keys 不支援
     3. **輪換週期**：後端金鑰每 **365 天**自動輪換一次
229. ★★ 跨帳號分享加密資源的兩個必要步驟
     1. **分享資源本身**：分享 AMI/Snapshot 等資源給目標帳號
     2. **分享 KMS Key 權限**：務必同時分享相應的 KMS 金鑰權限（常被忽略）
     3. **關鍵提醒**：沒有 Key 權限 = 資源看得到但用不了
230. 😇 KMS Key Rotation 的兩種方式
     1. **Automatic Rotation**：固定每年輪換一次，頻率**不可調整**
     2. **Manual Rotation**：頻率**可自訂**，需要時隨時手動進行輪換
231. ★★ KMS Key Policy 的角色
     1. **定義**：控制 KMS **CMK** 存取權限的**主要機制**，類似 S3 Bucket Policy
     2. **核心作用**：直接定義誰能使用這把金鑰
     3. **適用場景**：跨帳號授權、Root 權限控制
232. ★★ KMS 存取控制的三層機制
     1. **KMS Key Policy**：直接定義誰能使用此 CMK，是**必要條件**
     2. **IAM Policy**：定義使用者能做什麼操作，需搭配 Key Policy 才有效
     3. **Grants**：程式化臨時授權方式，適合動態權限和自動化場景
233. ★★ KMS 存取權限的判斷邏輯
     1. **完整判斷**：能否使用 KMS CMK = (KMS Key Policy 允許) **AND** (IAM Policy 允許)
     2. **關鍵點**：Key Policy 是**必要條件**，IAM Policy 單獨存在無效
     3. **考試記憶**：題目問「control access to KMS CMK」→ 答 **Key Policy**
234. 😇 KMS Key Policy 與 IAM Policy 的比喻
     1. **Key Policy**：「門鎖」（直接控制存取權）
     2. **IAM Policy**：「鑰匙」（使用者能否持有權限）
235. 😇 AWS Managed Keys vs CMK 的 Key Policy 管理差異
     1. **AWS Managed Keys**：無法修改 Key Policy
     2. **Customer Managed Keys (CMK)**：可以自由修改 Key Policy
236. ★ AWS Systems Manager (SSM) Parameter Store 的用途
     1. **核心功能**：儲存密碼和機密資訊
     2. **版本管理**：追蹤值隨時間的變化歷史
237. ★ AWS Shield Advanced 的關鍵特性
     1. **24/7 支援**：需要 DDoS Response Team (DRT) 的專業協助
     2. **成本保護**：提供 AWS 帳單報銷保障，降低 DDoS 攻擊成本
     3. 一個月要價 3,000 美元
238. ★★ SQS Visibility Timeout 的運作機制
     1. **定義**：訊息的「**處理鎖定期**」，控制訊息可見性
     2. **核心行為**：Consumer 收到訊息後，其他 consumer 在此期間暫時看不到這則訊息；時間到還未刪除，訊息會自動重新出現在 queue 中
     3. **時間設定**：預設 30 秒，最長 12 小時
239. ★★ SQS 優先權處理的正確姿勢
     1. **核心觀念**：SQS 沒有原生的訊息優先權機制 ❌
     2. **正確做法**：使用**多個 queue 分流**，並控制 consumer 的**輪詢順序** ✅
     3. **記憶點**：想要優先權 = 用多個 queue 分流
     4. **EC2 Consumer 行為**：持續檢查 Premium Queue → 有就處理；Premium 空了 → 才檢查 Free Queue
240. 👀 AWS 中負責不同任務協調的服務比較
     - SQS = 訊息佇列,解耦元件
     - SWF = 工作流程協調
241. SWF 現在較少出現在考試，AWS 主推 Step Functions 取代 SWF。遇到工作流程題目，Step Functions 是更新的答案
242. S3 Event 可發送到：
     - SNS Topic
     - SQS Queue
     - Lambda
243. ★★ S3 → SNS 實現 Fanout Pattern 的關鍵設計
     1. **問題**：S3 event notification 只能設一個目標，無法同時通知多個團隊 ❌
     2. **解決方案**：使用 **SNS fanout pattern**：S3 → SNS Topic → 多個 endpoint（可同時通知多個接收者） ✅
     3. **對比**：直接用 SQS 只能一對一，無法 fanout
     4. **考試記憶**：需要多個接收者 = SNS fanout pattern
244. 😇 S3 Event 通知多方的考試技巧
     1. **題目特徵**：看到「S3 event 要通知多方」
     2. **答案必有**：SNS
245. ★★ SNS 與 SQS 的訂閱關係是 Push Model
     1. **核心概念**：SQS 訂閱 SNS，**不是 poll SNS**
     2. **運作方式**：**SNS 是 push model**⭐️，主動推送訊息到訂閱的 SQS queue
     3. **訂閱流程**：SQS queue 訂閱 SNS topic → SNS 主動推送訊息到 queue
246. ★★ SQS 訊息保留期**預設 4 天，最長 14 天**
     1. 若 batch job 每週才處理
     2. 需調整 `SetQueueAttributes` 將訊息保留期設為 7 天以上
247. ★★ SQS 送訊息到 DLQ 的觸發條件
     1. **唯一觸發條件**：超過 **maxReceiveCount**
     2. **判斷邏輯**：當一則訊息被 receive 但沒被刪除的次數 > 設定的閾值，就會被移到 DLQ
248. ★ SQS 訊息重複接收的常見原因
     1. **處理失敗**：Lambda 處理訊息時 throw exception，訊息回到 queue
     2. **Visibility timeout 太短**：處理還沒完成，訊息就又變 visible，被重複接收
     3. **程式 bug**：處理完但忘記呼叫 DeleteMessage
249. ★ SQS DLQ 設定範例與最佳實踐
     1. **典型設定**：maxReceiveCount = 3-5 次
     2. **觸發機制**：訊息被 receive 3 次後仍未刪除 → 送到 DLQ
250. ★★ AWS WAF 核心功能與部署位置
     1. **功能**：**Web 應用防火牆**，擋常見攻擊（**SQL injection**、**XSS** 等）
     2. **可部署位置**：CloudFront（CDN）、ALB（Application Load Balancer）、API Gateway
251. ★★ AWS WAF 部署位置的考試陷阱
     1. **限制**：❌ 不能放在 NLB（Network Load Balancer）、❌ 不能直接保護 EC2（要透過 ALB）
     2. **核心原理**：WAF 只保護 **Layer 7**（HTTP/HTTPS），所以只能接在處理 HTTP 的服務上
252. ★★ AWS WAF 保護場景的判斷
     1. **題目特徵**：提到「防止 **SQL injection** / **XSS**」或「**rate limiting** / **IP blocking**」
     2. **答案**：選 **WAF**
253. 👀 GuardDuty 與 AWS WAF 的功能差異
     1. **GuardDuty 定位**：威脅「**偵測**」服務，只負責發現可疑活動（monitoring）
     2. **核心限制**：**不會主動阻擋流量**（no blocking），只產出 findings 供你分析
     3. **應用場景**：需要主動阻擋攻擊時 → 選 **WAF**；只需要威脅監控時 → 選 **GuardDuty**
254. ★ CloudFront Origin 來源服務的支援限制
     1. **定義**：**Origin** = CloudFront 後面接的來源服務
     2. **支援服務**：只能接 **HTTP/HTTPS 相關服務**（ALB、S3、custom HTTP server）
     3. **考試陷阱**：❌ 不能接 **NLB**（NLB 是 Layer 4，不是 HTTP/HTTPS）
255. 😇 S3 + CloudFront 靜態網站的標準架構
     1. **S3 角色**：存放**靜態檔案**（HTML/CSS/JS/圖片），開啟 website hosting 功能
     2. **CloudFront 角色**：**CDN 服務**，快取內容到全球邊緣節點，加速訪問
256. 👀 CloudFront + S3 靜態網站架構的考試陷阱
     1. **錯誤做法**：CloudFront 直接指向 S3 website endpoint ❌（無法使用 OAI，安全性差）
     2. **正確做法**：CloudFront 指向 S3 REST endpoint + 使用 OAI ✅（安全且標準）
257. ★ CloudFront Origin Group 的 Failover 機制
     1. **定義**：**Origin Group** = 1 個 primary origin + 1 個 secondary origin
     2. **用途**：**高可用性設計**，不是負載分散
     3. **觸發條件**：當 primary 失敗（**5xx 錯誤**）時，自動切換到 secondary
258. S3 預設全私有 - 只有 owner 能存取
259. ★★ SSM Parameter Store 的組態管理應用
     1. **核心用途**：支援 **externally maintain**（外部維護）、**version history**（版本歷史）、**configuration values**（組態值）管理
     2. **特性**：專門處理應用組態的中央存儲和版本控制
260. ★ GuardDuty 的威脅偵測資料來源分析
     1. **掃描的資料來源**：
        1. **VPC Flow Logs** - 網路流量分析
        2. **CloudTrail Logs** - API 操作記錄監控
        3. **DNS Logs** - DNS 查詢記錄分析
     2. **不掃描的資料源**：**CloudWatch Logs**（內容過於雜亂，應用程式 logs 難以標準化分析）
261. ★★ Amazon Inspector 用於 EC2 主機漏洞掃描
     1. **核心用途**：檢測 **OS vulnerabilities**（作業系統漏洞）**within EC2 instances**（EC2 內部）
     2. **特性**：支援 **定期掃描**（weekly）、提供 **concrete recommendations**（具體建議）
262. ★ AWS 安全服務的角色分工
     1. **Inspector**：掃「漏洞」（系統有什麼弱點）
     2. **GuardDuty**：抓「入侵」（有人在搞鬼嗎）
     3. **Shield**：擋 DDoS 攻擊
263. ★★ Secrets Manager vs SSM Parameter Store 密碼存儲對比
     1. **SSM Parameter Store**：便宜甚至免費，但 **密碼輪換需自己實作**（要寫 Lambda）
     2. **Secrets Manager**：費用較高，但 **原生支援自動輪換**（automatic rotation）
     3. **KMS**：加密金鑰管理服務，**不適合存密碼**
264. ★★ Secrets Manager 自動輪換機制
     1. **核心功能**：支援 **automatic rotation**（自動輪換密碼）
     2. **內建整合**：原生支援 RDS、Aurora、DocumentDB 等資料庫
     3. **輪換流程**：自動產生新密碼 → 更新資料庫 → 更新 secret 值（**零停機時間**）
265. 😇 Parameter Store 與 Secrets Manager 的取捨
     1. **Parameter Store**：便宜甚至免費，但 **密碼輪換需自己實作**（要寫 Lambda）
     2. **Secrets Manager**：費用較高，但 **省事**（原生支援自動輪換）
266. ★★ Amazon Macie 敏感資料偵測服務
     1. **核心功能**：使用 ML **自動發現敏感資料**（PII、財務資料等）
     2. **典型應用**：掃描 S3 bucket 安全性問題、提供 bucket 清單（未加密、公開、跨帳號共享）
267. ★ Macie 與 GuardDuty 的功能定位
     1. **Macie**：專注 **S3 資料內容安全**（敏感資料偵測）
     2. **GuardDuty**：偵測 **整體威脅行為**（網路/API 層級威脅監控）
268. 😇 Macie 考試判斷標準
     1. 看到「S3 + 敏感資料偵測」→ **Macie**
     2. 看到「PII 掃描」→ **Macie**
     3. 看到「找出公開的 S3 bucket」→ **Macie**
269. ★★ 客戶端加密（Client-side Encryption）應用場景
     1. **判斷標準**：題目提到「**even company admins can't see**」→ 直接選 **Client-side Encryption**
     2. **運作原理**：在應用程式層面加密資料 → 寫入資料庫（密文形式）→ 資料庫無法看到明文 → 只有持有解密金鑰的客戶端能解密
270. ★★ KMS Multi-region Keys 跨域加密解決方案
     1. **問題場景**：Aurora Global Database 跨多區域，單區域 KMS key 無法在其他區域解密
     2. **解決方案**：使用 **Multi-region Keys**，每個區域都有對應的 key replica，能解密同一份資料
271. ★ Aurora Client-side Encryption + KMS Multi-region Keys 組合方案
     1. **應用場景**：資料庫跨域部署 + 需要強加密（連管理員都無法看到）
     2. **實作組合**：Client-side Encryption（應用層加密） + Multi-region Keys（跨域密鑰管理） = **唯一解**
272. S3 跨 KMS Key Replication = 解密 + 加密
     1. Source Key → `kms:Decrypt`（解開舊的）
     2. Target Key → `kms:Encrypt`（用新的加密）
     3. 授權對象 → S3 Replication Service Role
     4. 你只要記得：換 Key 複製 = 要給兩把 Key 的權限。
273. S3 Replication 在跨 KMS Key 複製時的流程：
     1. 從 Source Bucket 讀取加密物件 → 需要用 Source KMS Key 解密
     2. 用 Target KMS Key 加密
     3. 寫入 Target Bucket
274. 👀 S3 Replication Service 的權限需求
     1. 角色身份：**AWS 服務角色**（非 IAM User），需要明確授權
     2. 必要權限：Source Key 的 `kms:Decrypt` + Target Key 的 `kms:Encrypt`
275. ★★ ACM 證書到期通知方案（EventBridge + SNS）
     1. **完整流程**：ACM 發出證書到期事件 → EventBridge 設定規則 → 觸發 SNS → Email 通知
     2. **核心優勢**：ACM 原生支援 EventBridge（含 **DaysToExpiry** 事件）、支援「每天檢查」、能提前 30 天通知
276. 😇 ACM 與 EventBridge 整合的考試判斷
     1. 看到「證書到期提醒」+ 「email 通知」→ **EventBridge + SNS**
     2. 記住：EventBridge 可設定「每日檢查」規則，實現精確的提前通知
277. ★★ Edge-Optimized API Gateway 的 ACM 證書位置限制
     1. **必要條件**：Edge-Optimized API Gateway 的 ACM 證書 **必須在 us-east-1**
     2. **原因**：API Gateway 背後使用 CloudFront，而 CloudFront 的證書 **只能來自 us-east-1**（AWS 硬性規定）
     3. **核心記憶**：**CloudFront 或 Edge-Optimized 服務 → ACM 證書必須在 us-east-1**（與 API Gateway 所在 region 無關）
278. ★★ CloudFront Signed URLs/Cookies 付費內容授權方案
     1. **題目判斷**：看到「付費內容」、「限制存取」、「授權用戶」→ **Signed URLs/Cookies**
     2. **單一檔案下載**：使用 **Signed URLs**
     3. **整個網站會員區**：使用 **Signed Cookies**
279. ★ CloudFront 授權方案的常見陷阱
     1. ❌ **S3 Pre-signed URLs**：這是 S3 直連，不走 CloudFront（不符合要求）
     2. ❌ **WAF**：那是擋攻擊的，不是做授權（功能混淆）
     3. ✅ **CloudFront Signed URLs/Cookies**：正確方案
280. ★★ 強制透過 CloudFront 的核心原因
     1. **安全隱患**：若不強制，用戶可能直接訪問 S3 URL 繞過你的付費機制
     2. **解決方案**：設定 **S3 Bucket Policy 僅允許 CloudFront Origin Access Identity (OAI) 存取**，禁止直接公網存取
281. ★★ CloudFront 安全提供私密內容的兩大要求
     1. **要求 1：使用簽名授權**：要求用戶使用 **CloudFront Signed URLs 或 Signed Cookies** 存取私密內容（不是直接 S3 URL）
     2. **要求 2：禁止直接來源伺服器存取**：禁止用戶直接存取來源伺服器上的內容（例如 Amazon S3 或私有 HTTP 伺服器），改由 CloudFront URLs 提供
282. 😇 Signed URLs 和 Signed Cookies 的功能對比
     1. **基本功能相同**：都允許你控制誰可以存取你的內容
     2. **主要差異**：Signed URLs 用於單一檔案限期授權，Signed Cookies 用於多檔案/整站授權
283. ★★ 題目提到「不想改現有 URL 結構」時，使用 **Signed Cookies**
284. ★★ Pre-signed URLs vs Signed URLs 的核心區別
     1. **Pre-signed URLs**：**S3 原生功能**，用於上傳或下載檔案，**不走 CloudFront**
     2. **Signed URLs**：**CloudFront 功能**，用於分發內容，**必須走 CloudFront**
285. ★★ 為什麼要用 Origin Access Identity (OAI)？
     1. **沒設定 OAI**：用戶拿到 S3 原始 URL 就能繞過 CloudFront（**安全缺陷**）
     2. **有設定 OAI**：**S3 只允許 CloudFront 存取**，所有流量必須經過 CloudFront
286. ★★ 實務判斷規則
     1. **識別題型**：題目出現「付費內容」、「限時存取」、「地理限制」→ 想到 **CloudFront Signed URLs/Cookies**
     2. **防直接存取**：題目要求「防止直接存取 S3」→ **必須搭配 OAI/OAC**
287. ★ S3 Pre-signed URLs 的問題：
     1. **繞過 CloudFront**：直接從 S3 讀取
     2. **失去優勢**：失去 CDN 加速、快取、地理分佈等優勢
     3. **不符合要求**：題目要求「透過 CloudFront 分發」就直接不符合
288. ★★ 效能優化的層次與考試判斷
     1. **層次架構**：CloudFront（**CDN 加速**靜態內容）、ElastiCache（**動態資料快取**）、Multi-region（**只在 DR/合規需求**時）
     2. **考試判斷**：題目只提「improve performance」無「disaster recovery/compliance」→ **CloudFront + ElastiCache 就夠**；看到 multi-region 選項 → 問自己：**真的需要還是只為效能？**
289. ★★ CloudFront vs Global Accelerator 選擇決策
     1. **CloudFront 應用場景**：網站、圖片、影片、API、cache；關鍵需求為**降低延遲、減少源站負載**
     2. **Global Accelerator 應用場景**：遊戲、IoT、VoIP、UDP、TCP（非 HTTP）；關鍵需求為**靜態 IP、快速區域容錯、固定入口點**
290. 😇 CloudFront vs Global Accelerator 記憶技巧
     1. **CloudFront**：**內容為王** - 什麼內容都能加速，但就是 HTTP(S)
     2. **Global Accelerator**：**協定不限** - TCP/UDP 都行，關鍵是**固定 IP + 快速切換**
291. ★ CloudFront vs Global Accelerator 實戰判斷
     1. **快速決策**：題目同時提到**「需要固定 IP」或「非 HTTP 協定」** → **直接選 Global Accelerator**，別被 CloudFront 的萬能感騙了
292. 😇 AWS Cost Anomaly Detection 異常支出偵測服務
     1. **服務定位**：AWS 成本管理功能
     2. **核心功能**：使用 ML 模型**偵測異常支出模式**（Anomalous spend patterns）
     3. **應用場景**：自動告警異常支出，幫助控制成本
293. 👀 AWS 成本管理工具的考試陷阱與比較
     1. **CloudWatch Billing Alarm**：只能監控總花費超過閾值，無法做根因分析 ❌ 異常偵測
     2. **AWS Budgets**：適合預算控管，但不是「異常偵測」的最佳解 ❌
     3. **Cost Explorer**：適合事後分析，不是主動告警 ❌
294. ★ EKS 對外暴露服務的標準架構：Ingress + ALB
     1. **典型方案**：安裝 **AWS Load Balancer Controller** → 建立 Ingress 資源 → **自動建立 ALB**
     2. **流量路徑**：Internet → ALB → Kubernetes Service → Pod
295. ★ AWS Load Balancer Controller 的核心功能
     1. **資源監聽**：監聽 Kubernetes 的 **Ingress/Service** 資源
     2. **自動配置**：在 AWS 自動建立對應的 **ALB/NLB**
     3. **管理職責**：負責 Target Group、健康檢查等設定
296. ★★ AWS Control Tower 管理多帳號架構
     1. **適用場景**：大規模 AWS 帳號治理，需要統一基準線和自動化控制
     2. **核心價值**：
        1. **一鍵建立 Landing Zone**：自動設定 OU 結構、帳號基準線
        2. **內建 Drift Detection**：自動監控帳號是否偏離最佳實踐
        3. **整合式管理**：串接 Organizations、Service Catalog、IAM Identity Center
297. ★ AWS Control Tower Drift 偵測範圍
     1. **OU 結構變更**：帳號移入/移出
     2. **Guardrails 違規**：SCP、Config Rules 不合規
     3. **基準設定變更**：CloudTrail、Config 配置偏離
298. 😇 AWS Config 的定位與限制
     1. **適用場景**：評估資源配置是否符合規範（例如：S3 bucket 是否加密、EC2 是否有正確 tag）、跨帳號聚合資源合規狀態
     2. **不適合多帳號治理的原因**：監控對象錯誤（Config 看的是資源而非帳號結構變更）、需要額外設定（要寫 custom rule 才能監控 OU 變更）、不是設計用途（帳號治理不是 AWS Config 的主要場景）
299. 記憶技巧：
     1. **Control Tower** = 帳號治理層級
     2. **Config** = 資源合規層級
300. ★ Aurora Serverless V2 的彈性混搭架構
     1. **核心優勢**：在同一個 cluster 中**混用 provisioned 和 serverless 實例**，靈活應對多變工作負載
     2. **典型組合**：穩定讀取流量用 **provisioned reader** + 不規則寫入用 **serverless writer**
301. 😇 Aurora Serverless V2 應用場景分析
     1. **讀取流量**：穩定、可預測 → 用 provisioned reader（成本穩定）
     2. **寫入流量**：不規則、難預測 → 用 serverless writer（自動應對高峰）
302. ★ ACU (Aurora Capacity Unit) 容量單位定義
     1. **基本定義**：**1 ACU = 2 GiB RAM** + 對應的 CPU 和網路資源
     2. **設定方式**：使用**容量範圍**而非固定值（min 1 ACU / max 8 ACU = 記憶體 2-16 GiB）
303. 😇 Aurora Serverless V2 容量規劃的最佳實踐
     1. **未知負載時**：設定 **ACU 容量範圍**，讓 Aurora 根據實際負載自動調整
     2. **正確做法**：題目若說「只知道記憶體消耗範圍」→ 配置 **ACU 範圍**（系統自己調整）
304. 在同一個 Aurora cluster 裡，你可以自由組合四種實例類型：
     1. Provisioned Writer（固定規格的寫入節點）
     2. Provisioned Reader（固定規格的讀取節點）
     3. Serverless Writer（自動擴展的寫入節點）
     4. Serverless Reader（自動擴展的讀取節點）
305. 😇 成本優化：使用模式決定省錢方案
     1. **Reserved Instances / Savings Plans**：適合 24/7 持續運行
     2. **Instance Scheduler**：適合間歇性使用（例如只在上班時間）
306. ★ Instance Scheduler 架構和實現方式
     1. **本質**：**不是** AWS 服務，而是 AWS 提供的 **CloudFormation template**（IaC 模板）
     2. **部署後自動建立**：**Lambda functions**（執行開關邏輯）、**DynamoDB table**（存排程設定）、**CloudWatch Events**（觸發排程）
     3. **正確實現步驟**：Deploy CloudFormation template of Instance Scheduler → 設定 start/stop schedules
307. ★★ Lambda SnapStart 解決冷啟動問題
     1. **適用場景**：**Java Lambda + cold start 問題** → **低延遲啟動** → **減少初始化時間**
     2. **支援範圍**：**僅限 Java 11、17 以上**（考試陷阱：Python、Node.js 不支援）

308. ★★ Bastion Host vs NAT Instance 區別
     1. **共同點**：都是一台在公有子網的 EC2 執行個體
     2. **Bastion Host 用途**：給 **人** 使用的 **SSH/RDP 跳板**
     3. **NAT Instance 用途**：給 **機器** 使用的 **私網流量轉發器**
     4. **快速區分**：NAT Instance 是「出口代辦」，Bastion Host 是「入口守門」

309. ★★ Interface Endpoint （介面端點）
     1. **本質**：在 VPC 裡創建一個 **ENI (網卡)**，配上私有 IP
     2. **運作**：流量透過 ENI 走 **AWS PrivateLink** 到服務
     3. **支援服務**：大多數 AWS 服務（S3、DynamoDB、SNS、SQS 等）
     4. **計費**：按小時收費 + 數據傳輸費
     5. **DNS**：提供私有 DNS 名稱
310. ★★ Gateway Endpoint （閘道端點）— 只有 S3 和 DynamoDB
     1. **本質**：在 **Route Table** 加一條路由規則
     2. **運作**：修改路由表，讓流量直接導向 AWS 服務
     3. **支援服務**：**只有 S3 和 DynamoDB**（考試必考！）
     4. **計費**：**完全免費**
     5. **DNS**：使用服務的公開 DNS 名稱
311. ★ AWS Database Migration Service (DMS) 核心概念
     1. **用途**：遷移 **關聯式資料庫、資料倉庫、NoSQL 資料庫** 到 AWS Cloud（或混合部署）
     2. **遷移模式**：**一次性遷移** 或 **持續複製** 來保持來源和目標同步
     3. **跨平台遷移**：使用 **AWS Schema Conversion Tool (SCT)** 翻譯資料庫架構，再用 DMS 遷移資料
312. ★★ Elastic Beanstalk — PaaS 平台部署
     1. **本質**：**完全託管的 PaaS**，你只負責上傳程式碼，AWS 自動搞定基礎設施
     2. **核心優勢**：自動化部署、擴展、負載平衡、監控；保留底層 EC2/RDS 調整權限
     3. **支援語言**：Go、Java、.NET、Node.js、PHP、Python、Ruby
313. 😇 Elastic Beanstalk 使用場景評估
     1. **選擇 Beanstalk 的情境**：快速部署應用、開發團隊專注 code、無需 DevOps 管理、需要自動擴展 + 負載平衡
314. ★★ EventBridge 核心定位：Serverless 事件路由器
     1. **本質**：**事件驅動架構的中樞神經**，負責解耦 Event Producers 和 Consumers
     2. **運作模式**：Producer 發事件 → EventBridge → 路由到 Consumers，雙方獨立部署、擴展
     3. **支援事件來源**：AWS 服務（EC2、S3、RDS...）、自己的應用程式、SaaS 應用（Zendesk、Datadog...）
315. ★★ EventBridge vs SNS 核心差異
     1. **路由方式**：EventBridge 支援**複雜規則過濾**，SNS 只有**簡單訂閱**
     2. **應用場景**：EventBridge 用於**事件驅動架構**，SNS 主要用於**訊息廣播**
     3. **整合能力**：EventBridge 原生整合 **AWS 服務 + SaaS**，SNS 主要推送通知
316. ★ EventBridge 考試判斷邏輯
     1. **選 EventBridge**：「根據事件內容路由到不同目標」、「整合多個 AWS 服務事件」、「定時排程任務」（取代 CloudWatch Events）
     2. **選 SNS**：「單純推送通知」
317. **Amazon EventBridge (Amazon CloudWatch Events)** is a serverless event bus that makes it easy to connect applications together. → 注意名稱！
318. 👀 EventBridge 考試陷阱：避免過度複雜組合
    1. **警覺信號**：看到「CloudTrail + CloudWatch Alarm + EventBridge」的組合題 → 考慮是否過度複雜
    2. **核心能力提醒**：EventBridge **本身就能**監聽 AWS 服務事件（包含 S3）、直接觸發 ECS Task、Lambda、Step Functions，**不需要 CloudWatch Alarm 當中介**
319. ★ EventBridge Target 選項與應用
    1. **常見 Target 目標**：Lambda、ECS Task、Step Functions、SNS/SQS、API Gateway
    2. **選項豐富性**：EventBridge 的 Target 配置能力強大，可**直接連接**多數 AWS 服務
320. 👀 ElastiCache Redis 基本安全組合（冷門題）
    1. **傳輸層安全**：`--transit-encryption-enabled` 啟用 **TLS 傳輸加密**
    2. **認證機制**：`--auth-token` 設置 **Redis AUTH 密碼驗證**（長期有效）
321. 👀 ElastiCache IAM Authentication 限制
    1. **適用範圍**：只能用於 **ElastiCache 管理 API** 操作
    2. **不適用於**：**Redis 資料操作指令**（Get、Set、Incr 等）無法使用 IAM Authentication
322. ★ Target Tracking Policy (ASG) 是 AWS 推薦的預設方案。Simple Scaling 有 cooldown，不適合突發流量，目前已少用
323. 實務建議：別用 Simple Scaling，用 Target Tracking 或 Step Scaling。
324. ★★ FSx for Lustre 檔案系統應用場景
    1. **關鍵字**：高效能運算（HPC）、機器學習訓練、影片處理、基因體分析
    2. **特性**：極高 IOPS、低延遲、可與 S3 整合
    3. **考試判斷**：看到「平行處理大量資料」→ 選 Lustre（賽車級性能）
325. ★★ FSx for Windows File Server 檔案系統應用場景
    1. **關鍵字**：Windows 應用、**SMB 協定**、**Active Directory**、企業檔案共享
    2. **特性**：原生支援 Windows 生態系
    3. **考試判斷**：題目提到「現有 Windows 環境」或「需要 AD 整合」→ 選 FSx for Windows（微軟親兒子）
326. ★ EFS (Elastic File System) 檔案系統應用場景
    1. **關鍵字**：Linux、多個 EC2 同時掛載、自動擴展、**NFS 協定**
    2. **特性**：通用型、彈性、跨 AZ
    3. **考試判斷**：看到「多台 Linux EC2 需要共享檔案」→ 選 EFS（標準通用型）
327. 😇 AWS 檔案系統選型決策樹
    1. **超高速處理大數據**：FSx for Lustre（賽車級）
    2. **Windows 企業環境**：FSx for Windows（微軟親兒子）
    3. **Linux 多機共享檔案**：EFS（標準通用型）
328. ★ DynamoDB 對頻繁 Schema 變更的適應性
    1. **特性**：無 Schema 限制，支援動態屬性添加
    2. **應用場景**：需要頻繁改變資料結構的應用
329. ★★ AWS Storage Gateway File Gateway 考試應用場景
    1. **題目特徵**：
       - On-premises 有大量檔案要歸檔到 AWS
       - 需要「無痛」整合，不想改現有系統
       - 要自動轉冷儲存（Glacier）省錢
    2. **標準答案**：
       - 部署 **File Gateway**（VM）
       - 透過 **NFS/SMB** 上傳檔案到 S3
       - 設定 **Lifecycle Policy** 自動轉 Glacier
    3. **核心記憶**：File Gateway = **把 S3 偽裝成 NAS**，讓地端系統無感整合
330. ★★ AWS Shield Advanced 與 Shield Standard 的選擇判斷
    1. **選 Shield Advanced 的關鍵字**：
       - 題目明確提到「**sophisticated DDoS**」
       - 需要「**24/7 專家支援**」
       - 擔心「**DDoS 導致費用暴增**」
       - 需要「**應用層（Layer 7）防護**」
    2. **選 Shield Standard**：
       - 題目只問「**基本 DDoS 防護**」（這幾乎不會是正解，因為 Standard 是預設開啟的）

331. 😇 共享檔案系統服務的選擇判斷（EFS vs S3 vs EBS）
    1. **題目關鍵字**：
       - 「**多個 EC2 同時存取**」→ EFS
       - 「**跨 AZ 高可用**」→ EFS
       - 「**共享檔案系統**」→ EFS
    2. **常見陷阱**：
       - S3 是 object storage，**不適合**需要檔案系統的場景（無法直接 mount）
       - EBS Multi-Attach **只在同一 AZ 內**，不跨 AZ
    3. **記憶口訣**：需要「**共享 + 跨 AZ**」→ 選 **EFS**
332. ★ FSx for Windows 的選擇關鍵字
    1. 題目提到 **Active Directory**、**Windows 檔案權限（NTFS ACL）**、**SMB 協議**
    2. 需要 **end-user file restore** 或 **企業 Windows 環境移轉**
333. ★ FSx for Windows 的替代服務判斷
    1. Linux only 環境、需要 POSIX 權限 → **EFS**
    2. 高效能運算 (HPC) → **FSx for Lustre**
334. 😇 FSx 服務選擇口訣
    1. 「**Windows + AD + SMB = FSx for Windows**」
    2. 題目看到這三個關鍵字其中兩個，**90% 就是選它**
335. ★★ IAM DB Authentication 核心特性
    1. **運作**：用 **IAM token** 取代密碼，**15 分鐘有效**（AWS Signature V4）
    2. **優勢**：無需存密碼、IAM 集中管理、自動輪替、可混用傳統認證
336. ★★ IAM DB Authentication 考試判斷
    1. **選擇**：題目強調「不要在 DB 存密碼」或「集中管理 DB 存取」
    2. **不適用**：長時間連線（token 過期）、非 AWS 環境、不支援的 DB 引擎
337. ★★ AWS Direct Connect（DX）
    1. **功能**：**專線** 直接連接到 AWS，**繞過公網**
    2. **效能**：穩定、低延遲（< 10ms）、1/10/100 Gbps 選項
    3. **安全**：流量不走公網、可搭配 VPN 加密
338. ★ AWS Direct Connect 的成本與應用
    1. **計費**：按小時 + 傳輸量，月傳 10TB+ 較划算
    2. **缺點**：**建置時間長**（幾週到幾個月）
    3. **適合**：大量穩定資料傳輸、敏感資料、低延遲需求
339. ★ AWS Direct Connect Gateway (DXGW)
    1. **功能**：單一 DX 連接**多個 VPC 和帳號**，跨區域、跨帳號連接
    2. **限制**：VPC 間**不互通**（只是集線器）、最多 10 個 VGW、需互通用 Transit Gateway
340. 😇 DXGW vs Transit Gateway
    1. **DXGW**：連接 DX 與多 VPC，**不做 VPC 路由**
    2. **Transit Gateway**：VPC 間路由、跨帳號、支援多種連接方式
341. ★★ AWS Transit Gateway (TGW) 核心定義
    1. **定義**：雲端路由器，讓多個 VPC、VPN、DX 專線**互連**
    2. **優勢場景**：多個 VPC 互連時，使用星狀拓撲而非全 mesh Peering
342. 😇 Transit Gateway vs 傳統 VPC Peering
    1. **傳統做法**：5 個 VPC 互連 = 需要 10 條 VPC Peering（n(n-1)/2）、加上 DX、VPN = 每個都要連、架構複雜
    2. **Transit Gateway**：所有 VPC、VPN、DX 都連到 TGW、TGW 統一管理路由、星狀拓撲簡單清晰
343. 😇 AWS Egress-only Internet Gateway 核心定義
    1. **定義**：只出不進的 IGW，專為 IPv6 設計
    2. **適用場景**：VPC 內 IPv6 EC2 只主動對外連線，不被外部連入
    3. 使用時，要加入到 Route Table，指定 `::/0` 給 Egress-only IGW，不然網路不通
344. 👀 IPv6 與 Egress-only IGW 的必要性
    1. **IPv6 特性**：所有 IPv6 位址都是公開可路由的（沒有私有 IP 概念）、VPC 內 EC2 用 IPv6 = 直接暴露在網路上
    2. **解決方案**：Egress-only IGW 允許內部發起的連線出去，但擋掉外部發起的連入
345. NAT Gateway vs Egress-only IGW 的區別
    1. **NAT Gateway**：用於 IPv4，允許私有子網 EC2 對外連線，阻擋外部連入
    2. **Egress-only IGW**：用於 IPv6，允許 VPC 內 IPv6 EC2 對外連線，阻擋外部連入
346. Internet Gateway vs Egress-only IGW 的區別
    1. **Internet Gateway**：允許雙向流量（內外互通），且適用於 IPv4 和 IPv6
    2. **Egress-only IGW**：只允許單向流量（只出不進），只適用於 IPv6
    3. 兩者是同層級資源，綁定在 VPC 上，並透過 Route Table 控制流量方向，可同時存在於同一 VPC
347. ★ S3 靜態網站與跨 domain API 呼叫的 CORS 問題
    1. **問題本質**：瀏覽器**同源政策（Same-Origin Policy）**會擋掉跨 domain 的請求，網站從 s3-website-us-east-1 載入，但 JavaScript 要向 s3.amazonaws.com 發 API 請求時會被阻止
    2. **解決方案**：在 S3 bucket 設定 **CORS**，明確允許來自網站 endpoint 的請求
348. 😇 S3 CORS 的考試記憶技巧
    1. **必選規則**：只要看到「S3 靜態網站的 JavaScript 要呼叫 S3 API」，直接選 **CORS**
349. ★★ API Gateway 的金絲雀部署（Canary Deployment）
    1. **定義**：**漸進式發布策略**，先讓少量流量（例如 10%）導向新版本，監控表現，若穩定逐步增加流量比例（10% → 25% → 50% → 100%），出問題可快速回滾
    2. **實作流程**：建立 Canary Stage → 部署新版本 → 透過 `canarySettings.percentTraffic` 設定流量比例（0-100）→ CloudWatch 分開記錄 production 和 canary metrics → 確認無誤後將 canary 提升為 production
    3. 這是 API Gateway **原生支援**的部署策略，無需額外工具
350. ★ API Gateway Canary 的考試判斷標準
    1. **關鍵字識別**：看到「逐步發布」、「漸進式部署」、「降低風險」、「測試新版本但不影響所有使用者」、「API Gateway 版本更新」 → 優先選 Canary
    2. **對比其他策略**：**Blue/Green** 是一次性切換全部流量（風險較高）、**Rolling** 是逐批更新實例（適用 EC2/ECS，不適用 API Gateway）
