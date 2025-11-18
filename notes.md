# Speed Notes for SAA

1. ★★ **VPC 與 Availability Zone 關係**
   1. VPC **跨越整個 region 的所有 Availability Zone**⭐️
   2. **每個 subnet 必須完全位於單一 Availability Zone**，無法跨 zone
   3. VPC **只存在於單一 region**
   4. 跨 region 通訊要用 VPC Peering 或 Transit Gateway
2. ★★ **Subnet CIDR 配置原則**
   1. **Subnet CIDR 是 VPC CIDR 的子集**
   2. 在不同 Availability Zone 部署 instance 可**保護應用程式免於單點故障**
3. ★ **Subnet 預設網路設定**
   1. 每個 subnet 對應到**單一** Availability Zone
   2. 新建 subnet **預設連接到** VPC 的 main route table
4. 😇 **VPC 架構記憶技巧**
   1. **大樓比喻**：VPC = 整棟大樓（region）
   2. **樓層概念**：Availability Zone = 不同樓層（1F、2F、3F）
   3. **房間限制**：Subnet = 每層樓的房間，**房間不能跨樓層**
5. ★★ **VPC 與 Subnet 抽象層次差異**
   1. **VPC 是邏輯網路空間**：虛擬網路定義，只是「IP 範圍配置規則」
   2. **不需實體存在**：可覆蓋整個 region 的所有 AZ
   3. **Subnet 是實際資源部署位置**：真正放置 EC2、RDS 的地方
6. ★ **資源部署限制原理**
   1. **實體資源限制**：EC2 實例必須存在於具體的資料中心（AZ）
   2. **單一 AZ 綁定**：一台 EC2 不可能同時存在於兩個機房
   3. **Subnet 必須綁定單一 AZ**：因為承載實際的運算資源
7. 😇 **公司組織架構類比**
   1. **VPC (10.0.0.0/16)**：公司總部的「員工編號規則」
   2. **Subnet A/B/C**：台北/新竹/台中辦公室的員工（各自對應 AZ-1/2/3）
   3. **核心概念**：編號規則適用全公司，但員工只能實際坐在一個辦公室
8. ★ **考試重點記憶**
   1. **VPC 是規則，Subnet 是實體位置**
   2. **規則可涵蓋全 region，實體資源必須選定一個 AZ 部署**
9. ★★ **Private Subnet 核心定義**
   1. **Private subnet = 沒有 public IP 的 subnet**
   2. **EC2 在 private subnet 不能直接綁定 EIP**（綁定後就變成 public）
   3. **對外通訊途徑**：只能透過 NAT Gateway/Instance 做轉發
10. 👀 **Private Subnet 概念澄清**
    1. **正確定義**：Private subnet 就是 instance 沒有 public IP
    2. **常見誤解**：混淆 subnet 類型與 IP 分配的關係
11. ★ **Network ACL 配置錯誤分析**
    1. **為何錯誤**：題目已指出可連接其他 EC2 instances，表示網路基本連通性正常
    2. **Network ACL 特性**：適合控制**整個 VPC 進出流量**，而非單一 EC2 instance 的流量控制
12. ★ **AWS Glue Job Bookmark 核心概念**
    1. **定義**：AWS Glue 的**狀態追蹤機制**，記錄 ETL job 已處理過的資料
    2. **運作原理**：持久化儲存 job 執行狀態（獨立於 job 本身）、追蹤已處理的資料位置/時間戳記
    3. **下次執行機制**：只處理新增的資料，**避免重複處理**
13. ★★ **Job Bookmark 應用場景與優勢**
    1. **題目關鍵字**：出現 "reprocessed old data" + "operationally efficient" 場景
    2. **技術優勢**：一鍵啟用、無需修改程式碼、自動追蹤處理進度、節省時間與成本
    3. **考試記憶點**：遇到 "避免重複處理資料" + "Glue ETL" 場景 → 優先想到 **Job Bookmark**
14. ★★ **SNI (Server Name Indication) 核心概念**
    1. **定義**：讓**單一 IP 地址/Load Balancer 處理多個域名的 SSL 憑證**
    2. **運作原理**：透過 **TLS handshake** 時客戶端帶上 hostname 來選擇對應憑證
    3. **ALB 實作**：單一 listener (HTTPS:443) 綁定多張 SSL 憑證，自動選擇正確憑證
15. ★★ **SNI 應用場景與優勢分析**
    1. **題目關鍵字**：出現 "multiple domains" + "SSL traffic" + "most cost-effective" 場景
    2. **核心優勢**：ALB **原生免費功能**、單一 listener 綁定多張憑證、自動管理無需重新設定
    3. **考試記憶點**：**多域名 SSL + ALB** → 優先選 **SNI**
16. 😇 **SNI 方案比較與限制**
    1. **SNI 優勢**：免費、ALB 原生支援
    2. **替代方案缺點**：獨立 ALB（成本高）、Wildcard 憑證（限同父域名）、CloudFront + ACM（額外服務複雜度）
    3. **SNI 限制**：舊瀏覽器不支援（但 2024 年影響極小）
17. ★★ **ELB 跨 region 部署限制**
    1. **核心限制**：**ELB 只能在單一 region 內運作**，無法跨 multiple regions
    2. **錯誤配置範例**：多個 region 的 EC2 instances + Auto Scaling 共用一個 ELB
    3. **正確解決方案**：每個 region 部署獨立的 ELB，搭配 Route 53 做跨 region 負載均衡
18. ★ A **gateway endpoint** is a gateway that you specify in your **route table** to access Amazon S3 from your VPC over the AWS network.
19. ★★ **AWS Config 核心功能與職責**
    1. **定義**：持續監控資源配置的合規性檢查工具
    2. **功能**：記錄資源配置變更歷史、用 Config Rules 評估是否符合規範、偵測到違規就標記為 `NON_COMPLIANT`
    3. **限制**：只能「**發現問題**」，無法執行修復⭐️
20. ★★ **EventBridge 在合規自動化中的角色**
    1. **定義**：事件驅動的膠水，連接 Config 和後續動作
    2. **功能**：監聽 AWS Config 發出的 `NON_COMPLIANT` 事件、用 custom event pattern 過濾特定規則、觸發下游目標(Lambda/SNS)
    3. **職責**：充當橋樑，讓 Config 觸發自動化⭐️
21. ★★ **AWS 合規自動修復架構模式**
    1. **標準三件套**：Config（監控員）→ EventBridge（派遣員）→ Lambda（執行員）
    2. **運作流程**：AWS Config Rule（檢查 key 年齡）→ 偵測到違規 → EventBridge Rule（監聽 NON_COMPLIANT 事件）→ 觸發 → Lambda Function（執行 deactivate + delete）
    3. **應用場景**：IAM access keys 超過 90 天未 rotate → 自動停用刪除
22. ★ **合規自動化架構設計原理分析**
    1. **各元件職責分工**：Config（監控 + 評估）、EventBridge（事件路由）、Lambda（執行修復）
    2. **為何需要三個元件**：Config 無法執行修復、Lambda 需要被觸發才能執行、EventBridge 作為事件橋樑
    3. **考試陷阱識別**：錯誤選項只用 Config + SNS 通知（只是發警報，沒有**自動修復**）
23. 😇 **考試記憶技巧與關鍵思維**
    1. **關鍵識別**：題目說 "deactivate and delete" → 需要程式邏輯執行 IAM API → 必須有 Lambda
    2. **標準模式記憶**：遇到「自動化合規處理」的題目，直接套用**合規自動修復三件套**
    3. **正確思維**：自動修復 = 檢測（Config）+ 路由（EventBridge）+ 執行（Lambda）
24. ★★ **合規自動化常見錯誤架構分析**
    1. **錯誤 1：自建 Config Rule**：AWS 已有 **managed rule** `access-keys-rotated` 可直接用，自己寫 custom rule 純粹浪費時間
    2. **錯誤 2：EventBridge Scheduler + Batch**：錯誤架構為 EventBridge Scheduler（每 24 小時）→ AWS Batch（容器化作業）→ 刪除 keys
    3. **用錯工具問題**：**Batch** 是跑**大規模批次運算**的（資料處理、ML training），不是用來執行簡單 API 呼叫⭐️
25. ★ **AWS Batch 誤用問題分析**
    1. **維護複雜度**：需要設定 compute environment、job queue、job definition → 維護成本高
    2. **時效性問題**：**定時執行** vs **事件驅動⭐️**，錯過即時性，可能讓違規 key 多存活 24 小時⭐️
    3. **成本考量**：Batch 需要 EC2/Fargate 資源，Lambda 只在觸發時計費
26. 😇 **正確架構的技術優勢**
    1. **即時性**：Config Rule 偵測違規 → EventBridge Rule 監聽事件（毫秒級）→ Lambda 執行刪除
    2. **維護成本**：**Serverless** 架構，Lambda 執行完就結束，無需維護基礎設施
    3. **成本效益**：事件驅動模式，只在需要時執行
27. 👀 **考試判斷標準與記憶要點**
    1. **判斷原則**：題目沒說「每天批次處理大量資料」→ **不需要 Batch**；題目說「自動修復」→ 用**事件驅動 + Lambda**，不是定時輪詢
    2. **AWS Batch 適用場景**：只在**大量運算作業**才划算，簡單任務別碰它
    3. **架構選擇記憶**：過度設計 + 用錯工具 = 考試陷阱
28. ★★ SQL Server → Aurora PostgreSQL 資料庫遷移挑戰
    1. **Schema 結構差異**：不同資料庫引擎的表格設計需要轉換
    2. **SQL 語法差異**：T-SQL vs PostgreSQL 語法不相容
    3. **資料搬移需求**：實際資料需要從 A 搬到 B
29. ★★ 資料庫遷移三大工具組合（SCT + DMS + Babelfish）
    1. **AWS SCT（Schema Conversion Tool）**：轉換 schema，把 SQL Server 的表格設計「翻譯」成 PostgreSQL 版本
    2. **AWS DMS（Database Migration Service）**：搬移資料，實際把資料從 A 搬到 B，可持續同步
    3. **Babelfish for Aurora PostgreSQL**：即時翻譯 SQL 語法，讓程式繼續用 T-SQL，自動轉成 PostgreSQL 能懂的語法⭐️
30. 😇 **資料庫遷移常見錯誤組合分析**
    1. **只用 SCT + DMS**：資料庫搬好了，但 app code 會因語法不相容而出錯
    2. **只用 Babelfish**：app 能連接，但缺少 schema 轉換和資料搬移
    3. **完整解法必要性**：三個工具各司其職，缺一不可
31. 😇 **資料庫遷移記憶技巧（搬家比喻）**
    1. **SCT**：看新房子格局，規劃東西要怎麼放（schema 規劃）
    2. **DMS**：搬家公司，實際搬東西（資料搬移）
    3. **Babelfish**：翻譯機，讓你繼續說中文，對方也能聽懂（語法相容性）
32. ★★ **資料庫遷移完整解決方案架構**
    1. **資料庫層面（SCT + DMS）**：SCT 轉換表格結構 + DMS 搬移資料
    2. **應用程式層面（Babelfish）**：app 原本的 T-SQL 語法無需修改，Babelfish 負責翻譯
    3. **最小化程式碼修改**：關鍵在於 Babelfish 讓既有程式碼保持不變
33. ★ **資料庫遷移錯誤選項分析 - AWS AppConfig**
    1. **實際用途**：管理 app 的 configuration（feature flags、參數設定）
    2. **錯誤原因**：完全不處理資料庫遷移的核心問題（schema、資料、SQL 相容性）
    3. **考試陷阱**：看到 "configuration" 就以為跟設定有關
34. ★ **資料庫遷移錯誤選項分析 - Kinesis Data Streams**
    1. **實際用途**：即時資料串流處理（logs、clickstream、IoT）
    2. **錯誤原因**：確實能做資料複製，但那是「串流資料」不是「資料庫遷移」、不處理 schema 轉換、不處理 SQL 語法相容性
    3. **考試陷阱**：看到 "real-time replication" 就以為是答案
35. ★ **資料庫遷移錯誤選項分析 - AWS Glue**
    1. **實際用途**：ETL service，抽取、轉換、載入資料（通常用於 data lake、分析）
    2. **錯誤原因**：是轉換「資料」不是轉換「SQL 語法」、不是用來做 query 翻譯的
    3. **考試陷阱**：看到 "transform" 就以為能轉換 SQL
36. ★★ **資料庫遷移考試策略與判斷原則**
    1. **關鍵字識別**："minimize application code modifications" → 必須使用 Babelfish
    2. **錯誤方案特徵**：需要 code 改動的方案、不處理 SQL 相容性的方案、只處理資料不處理語法的方案
    3. **標準答案記憶**：資料庫遷移 = **SCT + DMS + Babelfish**（最小化 code 改動）
37. ★★ **S3 Cross-Region Replication (CRR) 核心概念**
    1. **定義**：自動將 S3 物件複製到不同 Region 的 bucket
    2. **必備條件**：源和目標 bucket 都要開 **Versioning**、必須是不同 Region、需要 IAM role 授權
38. ★★ **S3 CRR 複製行為特性**
    1. **會複製**：啟用後的新物件、Metadata、tags、ACL
    2. **不會複製**：舊物件（需手動用 S3 Batch Operations）、Delete markers（預設關閉，可開啟）、複製不會串連（A→B→C，A 的物件不會到 C）
39. ★★ **S3 CRR 考試判斷標準**
    1. **必選 CRR 場景**：Regional failure、Disaster recovery (跨 Region)、Compliance (異地儲存)、Lower latency (多地區用戶)
    2. **不需要 CRR**：AZ failure（S3 預設跨 AZ）
40. ★ **S3 CRR 記憶口訣與策略**
    1. **核心記憶**：**S3 天生跨 AZ，想跨 Region 要 CRR**
    2. **關鍵字識別**：看到「regional」、「disaster」、「compliance」→ 選 CRR
41. 😇 **S3 CRR 成本考量因素**
    1. **主要費用**：跨 Region 傳輸費（最貴）、目標 bucket 儲存費、每個物件的 PUT request 費用
42. ★★ **ALB HTTP → HTTPS redirect 核心功能**
    1. **定義**：**ALB listener 可以直接設定 redirect rule**，把 port 80 (HTTP) 流量自動轉到 port 443 (HTTPS)
    2. **設定概念**：ALB Listener (Port 80) → Rule: Redirect to https://#{host}:443/#{path}?#{query}
43. ★★ **ALB redirect 方案優勢分析**
    1. **技術優勢**：不需額外資源（直接在 ALB 設定）、零程式碼（listener rule 設定即可）、最少維運成本
    2. **考試重點**：符合 "least operational overhead" 要求
44. ★ **ALB redirect 考試記憶點與錯誤選項**
    1. **正確方案**：Configure listener redirect rule
    2. **錯誤選項**：不需要 EC2 跑 nginx redirect、不需要 Lambda 處理 redirect、不需要 CloudFront（雖然也能做，但題目已有 ALB）
45. 👀 **ALB listener 配置陷阱與限制**
    1. **考試陷阱**："Create a new HTTP listener on port 80" 聽起來正確，但如果已有 port 80 listener 就無法新建
    2. **技術限制**：**同一 port 不能有多個 listener**，需要修改現有 listener 而非新建
46. ★★ **EKS Autoscaling 雙層架構概念**
    1. **Pod 層**：擴展 Pod 數量，使用 **HPA (Horizontal Pod Autoscaler)**
    2. **Node 層**：擴展 EC2 node 數量，使用 **Cluster Autoscaler** 或 **Karpenter**
47. ★★ **Cluster Autoscaler 核心功能與整合**
    1. **題目判斷關鍵**：infrastructure 自動擴展 → 指的是 **node 層級**
    2. **技術組合**：**Cluster Autoscaler** 搭配 **Metrics Server** 監控 pod 資源需求、自動增減 node 數量、與 Auto Scaling Groups 整合
48. ★ **Karpenter vs Cluster Autoscaler 比較分析**
    1. **Karpenter 優勢**：更快更靈活、直接選 instance type、較新技術
    2. **Cluster Autoscaler 優勢**：傳統標準方案、需預先定義 ASG、Kubernetes 標準配置
    3. **考試選擇**：通常選 **Cluster Autoscaler**，因為它是標準解法
49. 😇 **EKS Autoscaling 考試記憶要點**
    1. **功能分工**：**HPA** = 水平擴展 **Pods**、**Cluster Autoscaler** = 擴展 **Nodes**
    2. **關鍵字識別**：題目說 "infrastructure scales" → 選 node 層級解法
50. ★ EKS Autoscaling 錯誤選項分析 - EC2 Auto Scaling Groups
    1. **錯誤原因**：能用但 **operational overhead 太高**、需要手動設定 scaling policies
    2. **正確方案優勢**：Cluster Autoscaler 已與 Kubernetes 原生整合，更自動化
51. ★ **EKS Autoscaling 錯誤選項分析 - CloudWatch alarms alone**
    1. **致命缺陷**：**只監控，不執行 scaling**
    2. **功能限制**：CloudWatch 可以觸發告警，但不會自動擴展 EKS nodes、需要搭配其他 autoscaling 機制才有用
52. ★★ **EKS Autoscaling 考試判斷邏輯與方案比較**
    1. **關鍵字識別**："LEAST operational overhead" → 優先選 Kubernetes 原生工具
    2. **維運成本分析**：Cluster Autoscaler（低，Kubernetes 原生）vs EC2 ASG + custom policies（高，需手動管理）vs CloudWatch alone（無效，不會觸發 scaling）
    3. **考試記憶**：**Kubernetes 原生工具（Cluster Autoscaler, HPA）永遠是最低維運成本的選擇**
53. ★ **EBS Multi-Attach 功能限制**
    1. **支援類型限制**：只支援 **Provisioned IOPS**（io1/io2 block express）
    2. **地理位置限制**：只能在**同一個 AZ** 內多個 Instance 共用，不是跨 AZ⭐️
54. 😇 **EBS 儲存類型選擇記憶要點**
    1. **成本最低**：Magnetic（或新世代的 Cold HDD sc1 / Throughput Optimized HDD st1）
    2. **IOPS 要求高**：Provisioned IOPS
    3. **通用場景**：General Purpose SSD
55. ★★ **DynamoDB Global Table 跨區域災難復原核心概念**
    1. **定義**：跨區域 DR 的標準解決方案
    2. **多主架構 (multi-master)**：每個區域都能讀寫，變更會自動同步到所有其他區域
    3. **最終一致性**：各區域間的同步是異步的，會達到最終一致
    4. **衝突解決**：使用 "**last writer wins**" 策略解決衝突
    5. **技術特性**：毫秒級延遲、保持應用狀態
56. ★★ **Route 53 DNS Failover 自動切換機制**
    1. **核心功能**：主區域掛了，自動路由到次區域
    2. **技術優勢**：**不需手動干預**，完全自動化故障轉移
57. 😇 **Well-Architected Tool 最佳實踐檢查服務**
    1. **功能**：自動掃描你的架構、給出改善建議
    2. **成本**：**免費服務**
58. ★★ **跨區域 DR 三要素核心架構**
    1. **資料同步**：DynamoDB Global Table / S3 CRR / RDS Read Replica
    2. **架構複製**：ASG + ALB 複製到次區域
    3. **流量切換**：Route 53 Failover
59. ★★ **DynamoDB Global Secondary Index (GSI) vs Global Table 考試陷阱**
    1. **GSI 誤用問題**：GSI = 同一張表的額外查詢索引，**只存在於同一個 region**
    2. **Global Table 正確用途**：跨 region 的資料複製，**多主架構同步**
    3. **考試陷阱**：看到 "global" 就以為能跨區域 → 錯誤判斷
60. ★★ **Well-Architected Tool vs Compute Optimizer 工具選擇**
    1. **Well-Architected Tool**：檢查架構設計、給出最佳實踐建議、**免費服務**
    2. **Compute Optimizer**：優化資源規格（EC2 type、EBS size、Lambda memory），**只管資源規格不管架構設計**
    3. **考試判斷技巧**：題目要求 "架構最佳實踐" → Well-Architected Tool；題目要求 "省錢、調規格" → Compute Optimizer
61. ★★ **Predictive Scaling 核心機制與特性**
    1. **運作原理**：使用 ML 分析 CloudWatch 歷史數據、**提前啟動** capacity 而非被動反應、持續學習預測更準確
    2. **適用場景**：規律性流量高峰（每天、每週固定時間）、應用啟動慢（容器/服務需要時間 warm up）、成本優化需求（避免 over-provision）
62. ★★ **Auto Scaling 策略考試判斷標準**
    1. **Predictive Scaling 關鍵字**：predictable/regular pattern、long initialization time
    2. **Dynamic Scaling 關鍵字**：sudden/unpredictable spike、threshold-based triggers
    3. **Scheduled Scaling 關鍵字**：specific time schedule、定時排程
63. ★★ **Dynamic vs Predictive Scaling 核心差異分析**
    1. **Dynamic Scaling（反應式）**：CPU 到 threshold → 才開始加機器 → 等機器啟動 → 流量已經來了 → 結果：用戶體驗差 + 可能過載
    2. **Predictive Scaling（預測式）**：看歷史數據 → 知道流量何時來 → **提前**準備好 capacity → 結果：流量來時已經 ready
64. ★ **Auto Scaling 考試陷阱與判斷技巧**
    1. **常見陷阱**：題目說 "based on **historical** data" → 容易誤以為是 dynamic scaling
    2. **關鍵差異**：Dynamic 用歷史算 threshold，**觸發後才反應**；Predictive 用歷史**預測未來**，提前準備
    3. **判斷口訣**：看到 "forecasted/in advance/before peak" → Predictive；看到 "threshold/when CPU reaches" → Dynamic
65. ★★ **SSL/TLS 憑證匯入位置選擇**
    1. **AWS Certificate Manager (ACM)**：首選方案，Region 支援時優先使用
    2. **IAM Certificate Store**：備用方案，Region 不支援 ACM 時使用
    3. **CLI 操作**：兩者皆可使用
66. ★ **SSL/TLS 憑證管理考試重點**
    1. **憑證匯入特性**：**第三方憑證**可匯入 ACM/IAM、**ACM 自己簽發**的憑證不能匯出
    2. **服務整合**：ALB/CloudFront 都支援從 ACM/IAM 載入憑證
    3. **實務建議**：優先用 ACM，有自動 renewal 提醒、整合更好
67. ★★ **CloudFront vs ACM/IAM 憑證管理差異**
    1. **CloudFront 限制**：憑證綁定單一 distribution、**不能匯出**重用於 EC2/ELB、不是憑證「儲存」位置
    2. **ACM/IAM 定位**：憑證**管理/儲存**中心
    3. **CloudFront 定位**：憑證**使用**端點
68. ★ **SSL 憑證考試關鍵字判斷技巧**
    1. **儲存管理類**：題目問 "import/store" → 選 ACM/IAM
    2. **使用部署類**：題目問 "use/attach" → 選 ALB/CloudFront
    3. **記憶類比**：ACM 是倉庫，CloudFront 是使用憑證的服務
69. ★★ **Multi-AZ 高可用性容錯計算公式**
    1. **題目要求**：最少 6 台持續運行 + 容許 1 個 AZ 掛掉 + 最經濟配置
    2. **計算邏輯**：任一 AZ 掛掉後仍需 ≥ 6 台 → 剩餘 2 個 AZ 要能提供 6 台 → 每個 AZ 至少 3 台 → 3 個 AZ × 3 台 = **9 台**
    3. **記憶公式**：每個 AZ 的數量 = 最少需求 ÷ (AZ總數 - 1) = 6 ÷ 2 = 3
70. ★ **Multi-AZ 配置方案比較分析**
    1. **2-2-2 配置（6台）**：正常運行 6台，1 AZ 掛掉剩 4台 → ❌ 不符合要求
    2. **3-3-3 配置（9台）**：正常運行 9台，1 AZ 掛掉剩 6台 → ✅ 符合且最省
    3. **4-4-4 配置（12台）**：正常運行 12台，1 AZ 掛掉剩 8台 → ✅ 符合但浪費錢
71. 😇 **容錯架構設計核心概念與實務**
    1. **DevOps 實務**：Auto Scaling Group 設定 MinSize=9，分散到 3 個 AZ，每個 AZ 最少 3 台
    2. **設計本質**：**過度配置 (over-provisioning)** 來換取高可用性
    3. **考試重點**：能算出「最剛好」的數字，平衡成本與容錯能力
72. ★★ **AWS MGN (Application Migration Service) 核心定義**
    1. **服務定位**：**Lift-and-shift 遷移的標準解法**，用於將 VM workloads 搬到 AWS
    2. **關鍵字識別**：
       - lift-and-shift（不改應用直接搬）
       - minimize downtime（需要持續同步）
       - legacy VM workloads（從 VM 搬到 AWS）
73. ★★ **AWS MGN 運作機制與優勢**
    1. **運作流程**：
       - 在 on-premises VM 裝 **AWS Replication Agent**⭐️
       - Agent 持續同步資料到 AWS (類似 real-time backup)
       - 在 AWS 測試 (Test instances)
       - 正式 Cutover（切換流量，停掉 on-premises）
    2. **核心優勢**：
       - **Continuous replication**：downtime 極小化，只有最後 cutover 那刻
       - **自動轉換**：自動處理 OS、driver 等相容性問題
       - **可以測試**：先測試再正式遷移，降低風險
74. ★ **AWS 遷移服務選擇判斷標準**
    1. **VM lift-and-shift 遷移**：**AWS MGN**
    2. **Disaster Recovery (DR)**：AWS Elastic Disaster Recovery
    3. **資料庫遷移**：AWS DMS
    4. **Server 端應用遷移**：AWS Server Migration Service（舊，已被 MGN 取代）
