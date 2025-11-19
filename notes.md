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
75. ★ **AWS Application Discovery Service 錯誤選項分析**
    1. **實際用途**：評估與追蹤，**不做遷移**
    2. **功能範圍**：掃描 on-premises 環境，收集 VM、應用、相依性資料、在 Migration Hub 顯示遷移進度、不會複製資料、不會啟動 EC2
    3. **考試陷阱**：名稱有 "Migration" 相關字眼，但只是評估工具
76. ★ **AWS DataSync 錯誤選項分析**
    1. **實際用途**：資料傳輸，**不是 VM 遷移**
    2. **適用場景**：檔案系統、物件儲存的資料搬移（例如：NFS → EFS、S3，或 on-premises 檔案伺服器 → FSx）
    3. **功能限制**：不處理 VM、OS、應用程式
    4. **考試陷阱**：看到 "sync"、"replication" 就誤選
77. ★★ **AWS Replication Agent 在 MGN 中的核心地位**
    1. **必要步驟**：在每台 source VM 上安裝 **AWS Replication Agent** 是遷移的第一步
    2. **技術原理**：沒有 Agent 就無法同步資料，這是 AWS MGN 的核心架構
    3. **類比理解**：Agent 就像在你的 VM 裝一個「搬家工人」，負責持續把東西搬到 AWS
78. 😇 **AWS MGN 遷移流程三階段詳解**
    1. **安裝 Agent**：在每台 source VM 上裝 AWS Replication Agent
    2. **持續同步**：Agent 即時將資料複製到 AWS staging area
    3. **測試/Cutover**：在 AWS 啟動 EC2 instances，完成遷移切換
79. ★★ **Regional API Gateway 自訂網域設定三步驟**⭐️
    1. **ACM 憑證必須在同一個 Region**：API Gateway 在 us-east-2 → ACM 憑證也要在 us-east-2（Regional API 硬性規定）⭐️
    2. **建立 API Gateway Domain Name**：把自訂網域綁定 ACM 憑證，API Gateway 提供實際網域名稱（如 `d-xxx.execute-api.us-east-2.amazonaws.com`）
    3. **Route 53 建立 Alias Record**：讓自訂網域指向 API Gateway 的網域名稱
80. ★★ **Regional vs Edge-Optimized API Gateway 差異比較**
    1. **Regional API**：ACM 憑證同一個 Region、不使用 CloudFront、適合區域內客戶端（EC2、Lambda）
    2. **Edge-Optimized API**：ACM 憑證必須在 `us-east-1`、自動建立 CloudFront、適合全球客戶端⭐️
    3. **考試判斷關鍵**：題目說 "serve clients in the same AWS Region" → 選 Regional 設定
81. 😇 **API Gateway 憑證配置記憶技巧**
    1. **Regional = 就地取材**：API 在哪憑證就在哪、不繞 CloudFront 直達目標、如在台北開店向台北市政府申請執照
    2. **Edge-Optimized = 全球配送**：憑證統一放 us-east-1（CloudFront 總部）、透過 CloudFront 分發全球、如做全球生意去總公司辦證照
82. ★ **Regional API Gateway 考試重點與陷阱**
    1. **核心規則**：**Regional API 的 ACM 憑證必須同 Region**（死規則）
    2. **題目難點**：混合了 API Gateway、自訂網域、SSL 憑證、DNS 設定概念
    3. **判斷原則**：考的是「細節規定」而非「架構判斷」
83. ★★ **API Gateway Private Certificate 錯誤選項分析**
    1. **Private Certificate (ACM PCA) 錯誤原因**：只能用於內部網路，不能對外公開、題目要求 public internet 的 HTTPS 連線 → 必須用 **Public Certificate**
    2. **類比理解**：Private Certificate = 公司內部員工證（辦公室內使用）、Public Certificate = 政府核發營業執照（對外營業）
84. ★★ **EFA vs ENA 高效能運算網路選擇**
    1. **EFA (Elastic Fabric Adapter)**：**OS-bypass** 技術、直接與網路硬體溝通、適合 HPC、ML training、MPI、NCCL 應用
    2. **ENA (Elastic Network Adapter)**：一般高速網卡、需經過作業系統處理封包、適合標準網路應用
    3. **技術差異**：EFA = ENA + OS-bypass 功能，延遲更低、吞吐量更高
85. ★★ **EFA 應用場景與考試關鍵字**
    1. **核心關鍵字**：HPC（高效能運算）、Machine Learning training、OS-bypass、MPI、NCCL、Inter-instance communication performance
    2. **典型應用**：自動駕駛 AI 訓練、基因定序分析、氣象模擬、財務風險建模、大量 EC2 實例互傳資料場景
    3. **考試判斷**：看到 HPC + OS-bypass → 直接選 EFA
86. 😇 **EFA 技術特性與限制**
    1. **支援平台**：只支援 **Linux**，Windows 上 EFA 等於只有 ENA 功能
    2. **包含關係**：**EFA 包含 ENA**，可執行所有 ENA 功能
    3. **記憶技巧**：ENA = 高速公路、EFA = 高速公路 + 專用快車道（OS-bypass）
87. ★ **AWS Systems Manager Fleet Manager 核心功能**
    1. **定義**：**AWS Systems Manager 的統一 UI 管理介面**，用於遠端管理 EC2 實例
    2. **適用範圍**：支援 AWS 雲端和 on-premises 的 nodes/EC2 instances
    3. **功能定位**：Systems Manager 眾多功能中的其中一項管理工具
88. ★ **AWS RAM 錯誤選項分析 - 軟體授權管理**
    1. **實際用途**：**跨 AWS 帳戶安全共享資源**，在 AWS Organizations 內共享資源
    2. **考試陷阱**：看到 "Resource Access Manager" 可能誤以為能管理所有類型資源
89. ★ AWS Certificate Manager：**配置、管理和部署 SSL 憑證**
90. ★★ **AWS License Manager 核心概念與 BYOL 場景**
    1. **定義**：專門處理 **BYOL (Bring Your Own License)** 場景，將現有授權帶到 AWS 雲端使用
    2. **核心功能**：追蹤授權使用（例如 50 個 Windows Server 授權，綁定 CPU 數量）、阻擋超額使用（授權用完就不能再開 EC2）、通知機制（授權快用完要發警告）
91. ★★ **DynamoDB Auto Scaling 自動容量調整機制**
    1. **核心服務**：使用 **AWS Application Auto Scaling** 根據實際流量模式動態調整 provisioned throughput capacity
    2. **運作機制**：流量突增時自動提高讀寫容量避免 throttling、工作負載降低時自動減少容量避免浪費
    3. **適用範圍**：支援 DynamoDB table 和 Global Secondary Index 的容量自動調整
92. ★★ **DynamoDB Streams + Lambda 觸發機制核心概念**
    1. **定義**：DynamoDB Streams 是變更日誌機制，記錄所有數據修改事件（新增/更新/刪除），可觸發 Lambda 自動執行特定動作
    2. **工作流程**：DynamoDB 表格更新 → DynamoDB Streams 記錄變更 → Lambda 被自動觸發 → 執行你定義的邏輯
93. ★★ **DynamoDB Streams + Lambda 考試重點機制**
    1. **觸發方式**：**同步輪詢** (Lambda 主動 poll Streams，不是 push 模式)
    2. **處理特性**：**批次處理**（可一次處理多筆變更記錄）、**保證順序**（同一個 partition key 的變更順序保證）
94. ★★ **DynamoDB Streams + Lambda 方案選擇考試判斷**
    1. **關鍵字識別**：題目強調 "**minimal configuration change**" → 優先選擇 Streams + Lambda
    2. **技術優勢**：只需啟用 Streams + 綁定 Lambda（兩步驟）、無需額外基礎設施、Serverless 自動擴展
    3. **考試陷阱**：相比之下，用 CloudWatch Events 需要更多設定
95. ★★ **CloudFront 與 ElastiCache 網站效能優化組合**
    1. **CloudFront 定位**：**全球 CDN 服務**，將網站內容快取到全球各地 edge locations
    2. **ElastiCache 定位**：**網站 in-memory 資料儲存或快取**，適用於資料庫查詢結果快取
    3. **標準組合**：使用 CloudFront 以網站為 custom origin + 使用 ElastiCache 作為網站的 in-memory data store
96. 😇 **CloudFormation 基本計費原則**
    1. **服務本身免費**：CloudFormation 不收費
    2. **資源照常計費**：透過 CloudFormation 建立的資源按正常費率收費
97. ★★ **S3 + SSE-KMS 加密方案核心優勢**⭐️
    1. **Envelope Encryption 支援**：只有 SSE-KMS 提供標準的 envelope encryption 實作
    2. **自動金鑰輪替**：KMS 支援每年自動輪替，SSE-S3 無法控制，SSE-C 需自行管理
    3. **完整審計追蹤**：KMS 完整整合 CloudTrail，可追蹤誰用了金鑰、什麼時候用、執行什麼操作
98. ★★ **Envelope Encryption 運作機制解析**
    1. **加密流程**：檔案用 Data Key 加密 → Data Key 用 Master Key(KMS) 加密 → 兩者都存在 S3
    2. **技術原理**：KMS 單次只能加密 4KB，Data Key 負責大檔案加密（效能好），KMS 只加密 Data Key（安全性高）
    3. **儲存結構**：S3 同時存放加密檔案（大）和加密的 Data Key（小）
99. ★★ **S3 加密方案選擇考試判斷標準**
    1. **選 SSE-KMS 關鍵字**：Envelope encryption、Key rotation、**Audit trail**、Compliance requirements、Customer managed keys
    2. **不選 SSE-KMS 場景**：Lowest cost（SSE-S3 更便宜）、Simplest solution（SSE-S3 更簡單）、You manage keys outside AWS（SSE-C）
    3. **核心記憶**：題目要求 **envelope encryption + 自動金鑰輪替 + 審計追蹤** 三條件 → 只有 SSE-KMS 符合
100. ★★ **Envelope Encryption 詳細概念與安全優勢**
     1. **雙層保護架構**：即使 Data Key 被竊取，沒有 KMS Master Key 也無法解密檔案；即使 KMS 被入侵，攻擊者也需要同時取得加密的 Data Key
     2. **效能與安全平衡**：大檔案用對稱加密（Data Key，速度快）；小金鑰用非對稱加密（KMS，安全性高）
     3. **考試記憶技巧**：**信封（Envelope）= 雙層包裝**，內層保護內容物，外層保護信封本身
101. ★ **Amazon Redshift Spectrum 核心功能**
     1. **定義**：**擴展 Redshift 查詢能力**，讓 Redshift cluster **直接查詢 S3 中的資料**⭐️
     2. **資料支援**：支援結構化和半結構化資料（JSON、Parquet、ORC、Avro等）
     3. **應用場景**：大規模資料分析，**無需將 S3 資料搬移到 Redshift** 即可執行複雜查詢⭐️
102. ★★ **S3 資料傳輸費用最佳化策略**
     1. **免費傳輸規則**：Internet → S3 (upload)、**S3 → EC2 (同 Region)**、S3 → CloudFront
     2. **成本判斷原則**：**跨 Region = 收費，同 Region = 免費**
     3. **考試公式**：minimize data transfer cost + 處理 S3 資料 → **EC2 same Region as S3**
103. ★ **處理專有格式檔案的服務選擇**
     1. **必選 EC2 場景**：vendor's proprietary software → 需要運算環境執行 → **EC2 是唯一選擇**
     2. **不適用服務**：Lambda（15 分鐘限制 + 記憶體不足）、Glue（不支援 proprietary format）、本地處理（傳輸費用爆炸）
     3. **核心考點**：特殊軟體需求 + 成本最佳化 → 選擇同 Region 的 EC2
104. ★ **AWS Health Dashboard 服務差異比較**
     1. **Personal Health Dashboard**：監控**你的 AWS 資源狀態**，針對特定帳號/資源，適用於「監控你的 EC2」場景
     2. **Service Health Dashboard**：監控**AWS 服務整體狀態**，區域/全球性事件，適用於「看 AWS 是否當機」場景
     3. **記憶口訣**：
        - Personal = 個人的 = 你的資源
        - Service = 服務的 = AWS 整體
105. ★★ **AWS DataSync 核心定位與功能**
     1. **服務定位**：**資料搬運專用服務**，專門處理 on-premises ↔ AWS 的大量資料傳輸
     2. **技術優勢**：全託管服務（無需自己寫 script 或購買商業軟體）、自動加速線上資料傳輸到 AWS 儲存服務
     3. **架構組件**：在 on-premises 部署 **DataSync Agent**（加速資料傳輸）、透過 **Direct Connect service endpoint** 連接 AWS 服務
106. ★★ **DataSync + Direct Connect 私有連線架構**
     1. **標準架構流程**：On-premises DataSync Agent → AWS Direct Connect（專線）→ Direct Connect service endpoint → AWS 儲存服務（S3/EFS/FSx）
     2. **核心優勢**：**私有連線**（不經過公網）、自動化傳輸管理、加速大量資料搬移
     3. **程式化連接要求**：使用 **Direct Connect service endpoint** 進行 AWS 服務的程式化連接
107. ★★ **DataSync 連接端點與傳輸方向限制**
     1. **來源端支援**：On-premises（NFS/SMB/自建物件儲存）、AWS 內部（S3/EFS/FSx）、Snowcone（內建 agent）
     2. **目標端限制**：只能是 AWS 儲存服務（**S3/EFS/FSx**）
     3. **傳輸方向**：**單向往 AWS 推送**，不支援 AWS → On-premises
     4. **不支援服務**：RDS/DynamoDB（非檔案系統）、直接連接 Glacier（需先到 S3）
108. ★★ **EBS io1 Queue Length 配置策略**
     1. **Transaction-intensive 場景**：低延遲需求 → **低 queue length** + 高 IOPS
     2. **Throughput-intensive 場景**：高吞吐量需求 → 高 queue length
     3. **考試判斷**：看到 "transaction-intensive" / "low-latency" → 選低 queue length
109. ★★ **EBS io1 IOPS 計算公式**
     1. **計算公式**：最大 IOPS = Volume 大小(GiB) × 50
     2. **範例計算**：10 GiB → 最多 500 IOPS
     3. **記憶技巧**：io1 = **50:1 固定比率**
110. ★★ **ACM vs ACM Private CA 憑證類型選擇**
     1. **ACM (AWS Certificate Manager)**：Public certificate（公開信任）、對外服務使用、免費且自動更新
     2. **ACM Private CA**：Private certificate（僅內部信任）、企業內部 PKI、需額外付費
     3. **考試判斷**："publicly trusted" → ACM；"internal authentication" / "private PKI" → ACM Private CA
111. ★★ **ACM 證書來源與管理方式**
     1. **ACM 簽發證書**：免費、自動更新、只用於 AWS 服務、**不能匯出私鑰**⭐️可以自簽！本身就是一個 CA
     2. **導入第三方證書**：需手動更新、可用於任何地方、保留原 ARN
     3. **考試判斷**："issue certificate" → ACM 簽發；"import from third-party CA" → 導入外部；"export private key" → 不能用 ACM 簽發的
112. ★★ **CloudWatch Agent + Parameter Store 監控架構**
     1. **CloudWatch 預設不監控 memory**：只監控 CPU、Network、Disk performance
     2. **監控 memory 需求**：安裝 CloudWatch Agent → 讀取 Parameter Store 配置 → 發送 custom metric
     3. **考試判斷**："memory usage" + Auto Scaling → 必須用 CloudWatch Agent + Parameter Store
113. ★★ **EC2 Instance Metadata 服務核心概念**
     1. **定義**：EC2 提供的特殊端點 `169.254.169.254`，讓 instance 查詢自己的資訊（IP、Instance ID、IAM credentials 等）
     2. **核心優勢**：無需安裝工具、無需設定權限、即時反映狀態、完全免費
     3. **考試判斷**："running instance 內取得自己的資訊" → Instance Metadata
114. 😇 **EC2 Instance Metadata 可查詢資訊類型**
     1. **網路資訊**：Public/Private IP
     2. **身份資訊**：Instance ID、Security Groups、IAM Role credentials
**VPC 陷阱**: VPC 本身免費,但別被誤導 — VPC 內的服務該收多少還是收多少。
115. ★ **AWS 會計費的項目**
     1. **Running EC2 Instance**：只要在運行就計費（按小時）
     2. **EBS Volumes**：無論 EC2 是否運行都計費（按儲存容量）
116. ★ **AWS 不計費的項目**
     1. **Stopped EC2 Instance**：停止後不計算運算費用
     2. **VPC 本身**：建立和使用 VPC 免費（但 VPC 內的資源如 EC2、NAT Gateway 等仍照常計費）
     3. **Public Data Set**：AWS 提供的公開資料集免費
117. ★★ **EC2 計費核心原則與陷阱**
     1. **計費區間**：**Boot 開始 → Terminate 結束**
     2. **Stop 狀態**：Stop 不計算運算費用，但 **EBS 持續計費**
     3. **Terminate 狀態**：才完全停止計費（EBS 也會刪除）
     4. **VPC 計費陷阱**：VPC 本身免費，但別被誤導 — **VPC 內的服務該收多少還是收多少**
118. ★ **Amazon Athena 核心概念與特性**
     1. **定義**：直接對 **S3 資料用 SQL 查詢**，無需載入或轉換
     2. **技術特性**：**Serverless**（零基礎設施管理）、按查詢計費（掃描多少資料付多少錢）、支援格式（CSV、JSON、Parquet、ORC 等）、標準 SQL（ANSI SQL 語法）
119. 😇 **Athena 與其他服務選擇比較**
     1. **Athena**：**ad-hoc 查詢**，直接掃 S3，最省事
     2. **Redshift**：需要載入資料，適合持續性分析
     3. **EMR**：需要管理叢集，適合複雜大數據處理
120. ★ **Athena 考試判斷標準**
     1. **關鍵字識別**：看到「分析 S3 資料」+「SQL 查詢」→ **Athena**
     2. **核心記憶**：**Athena = S3 + SQL**
121. ★★ **CloudWatch Agent + Logs Insights** 日誌收集分析架構
     1. **標準流程**：⭐️
        - 在所有 EC2 安裝 **unified CloudWatch agent** 收集 logs
        - Logs 自動送到 **CloudWatch Logs**
        - 用 **CloudWatch Logs Insights** 查詢分析
        - 結果匯出到 S3
     2. **Unified CloudWatch Agent 優勢**：⭐️
        - 同時支援 **Windows + Linux**
        - 單一 agent 收集 logs 和 metrics
        - 效能更好
        - AWS 原生整合（零額外設定）
     3. **CloudWatch Logs Insights 特性**：
        - 內建 **SQL-like 查詢語言**⭐️
        - 即時搜尋分析
        - 有預設範本查詢
        - 直接對接 CloudWatch Logs
122. ★ **CloudWatch 日誌方案錯誤選項分析**
     1. **手動 SSH 收集**：100+ instances 不可行
     2. **自建 ELK Stack**：需管理基礎設施，工作量大
     3. **SSM Run Command**：適合一次性指令，不適合持續收集 logs
     4. **舊版 CloudWatch Logs agent**：不支援 Windows，效能較差
123. ★ **CloudWatch 日誌收集考試判斷標準**
     1. **關鍵字識別**："collect logs from instances" → **CloudWatch Agent**、"analyze logs" → **CloudWatch Logs Insights**、"minimal effort" → AWS 原生服務
     2. **核心考點**：知道 AWS 有原生的 log 收集分析方案，別想著自己建
124. ★★ **S3 Requester Pays 核心概念與計費轉嫁**
     1. **定義**：下載者付費，而非 bucket owner 付費
     2. **計費分工**：Bucket owner 只付 **storage** 費用、Requester 付 **request + data transfer** 費用
     3. **適用場景**：分享大型 dataset（zip codes、地理資料、爬蟲數據等），避免 owner 被下載流量吃垮
125. ★★ **Requester Pays 重要特性與技術要求**
     1. **自動禁用匿名存取**：啟用後必須驗證身份才能收費（這是 feature 不是 bug，因為要知道跟誰收錢）
     2. **API 要求**：必須在 header 加 `x-amz-request-payer`
     3. **計費對象**：若 requester 用 IAM role，該 role 所屬 account 被收費
126. ★★ **Requester Pays 考試判斷與關鍵字識別**
     1. **題目關鍵字組合**："share data" + "lower transfer costs" + "disable anonymous access" → **Requester Pays 一次滿足三個需求**
     2. **錯誤選項陷阱**：Cross-Region Replication（反而增加成本）、S3 Transfer Acceleration（加速但不降成本）、Pre-signed URL（無法轉嫁流量費用）
127. ★ **Requester Pays 錯誤方案分析 - CRR**
     1. **為什麼錯**：複製本身產生 **request fee + inter-region transfer fee**、相當於你先付一次流量費把資料搬到歐洲、**反而更貴**
     2. **記憶技巧**：CRR = 主動搬家，你付搬家費
128. ★ **Requester Pays 錯誤方案分析 - Cross-Account Access**
     1. **為什麼錯**：只是開權限讓對方下載、**流量費還是 bucket owner 付**、沒解決成本問題
     2. **記憶技巧**：Cross-account = 開門讓人拿，你付運費
129. ★★ **SQS Standard Queue 重複訊息根本問題**
     1. **核心問題**：Standard Queue 的 **at-least-once delivery** 特性會造成重複訊息，這跟 visibility timeout 無關
     2. **架構特性**：Standard Queue 就是會重複，這是設計特性，不是 bug
     3. **錯誤認知**：調整 Visibility Timeout 無法防止 Standard Queue 本身產生重複訊息、無法保證訊息不會被發送兩次到 queue 裡
130. ★★ **FIFO Queue 解決重複訊息的核心機制**
     1. **Message Deduplication（去重）**：5 分鐘內相同的 `MessageDeduplicationId` 只會被接受一次、自動過濾重複訊息
     2. **Exactly-once Processing（恰好一次處理）**：確保每個訊息只被消費一次，這才是解決「訂單被處理兩次」的關鍵
     3. **從根本解決**：FIFO Queue 從根本上保證 exactly-once delivery
131. ★ **Visibility Timeout 功能定位與限制**
     1. **實際功能**：讓訊息在被某個 consumer 處理時「暫時隱藏」
     2. **類比理解**：Visibility Timeout = 「這個訂單我先處理，其他人別碰」，但如果系統本來就會產生兩張一模一樣的訂單，調整這個參數根本沒用
     3. **核心限制**：只是處理「同一訊息被多個 consumer 同時處理」的問題，不是解決「重複訊息」的問題
132. ★★ **SQS 重複訊息考試判斷標準**
     1. **必選 FIFO 場景**：看到「重複處理」、「duplicate messages」問題
     2. **判斷邏輯**：如果「順序」很重要 → **必選 FIFO Queue**、如果只是「不能重複」→ **優先選 FIFO Queue**
     3. **核心記憶**：Standard Queue 架構問題不是參數調整可以解決的，需要 FIFO Queue 從根本解決
133. ★★ **ARN 結構解析與 Resource ID 識別**
     1. **ARN 標準結構**：`arn:aws:service:region:account-id:resource-type/resource-id`
     2. **結構拆解範例**：`arn:aws:ds:us-east-1:123456789012:directory/d-1234567890`
        - Partition: `aws`
        - Service: `ds` (Directory Service)
        - Region: `us-east-1`
        - Account ID: `123456789012`
        - Resource ID: `d-1234567890` (Directory ID)
     3. **核心記憶**：**ARN 從左到右越來越具體，最右邊就是 Resource ID**
134. ★ **ARN 考試常見陷阱與錯誤選項**
     1. **Statement ID (Sid) 混淆**：`DirectoryTutorialsDojo1234` 是 Policy 內的自訂標籤，不是 Directory ID 或 Directory name
     2. **Account ID 誤選**：`987654321012` 是 12 位數字的帳號 ID，位於 ARN 中間位置
     3. **Directory ID 格式**：正確格式通常是 `d-xxxxxxxxxx`
135. 😇 **ARN 考試判斷技巧與記憶要點**
     1. **識別原則**：看 ARN 最後一段 = Resource ID
     2. **格式記憶**：Directory ID (`d-xxx`)、Account ID (12位數字)、Sid (自訂標籤)
     3. **考試重點**：題目在考你識別 ARN 各個部分的能力
136. ★★ **IAM Policy 核心結構與組成元素**
     1. **基本結構**：`Version` (Policy 語法版本，固定值 `2012-10-17`) + `Statement` (權限規則陣列)
     2. **Statement 核心元素**：`Sid` (Statement ID，可選識別標籤)、`Effect` (Allow/Deny)、`Action` (允許的操作)、`Resource` (資源範圍 ARN)
     3. **Policy 定義**：定義 AWS 資源的存取權限
137. ★★ **IAM Policy Statement 實例解析**
     1. **Statement 1 範例**：Effect: Allow、Action: `ds:*` (Directory Service 所有操作)、Resource: 特定 Directory ARN → **只能管理指定的 AD 目錄**
     2. **Statement 2 範例**：Effect: Allow、Action: `ec2:*` (EC2 所有操作)、Resource: `*` (所有資源) → **可對任何 EC2 實例做任何事**
     3. **萬用字元 `*`**：代表「全部」，用於 Action 或 Resource
138. ★★ **IAM Policy 考試重點與判斷原則**
     1. **Effect 優先權**：`Deny` 優先權**高於** `Allow`
     2. **Action 格式**：服務前綴 + 操作（例如 `s3:GetObject`、`ec2:*`）
     3. **Resource 指定**：使用 ARN 格式指定資源範圍，`*` 表示所有資源
139. ★ **IAM Policy 實務判斷標準與最佳實踐**
     1. **Action 配置**：寬鬆 (`*` 所有操作) vs 嚴謹 (明確列出操作)
     2. **Resource 配置**：寬鬆 (`*` 所有資源) vs 嚴謹 (指定特定 ARN)
     3. **Effect 配置**：Allow (允許) vs Deny (明確禁止)
140. 👀 **IAM Policy 考試陷阱與特殊情況**
     1. **`Resource: "*"` 警覺點**：通常不是最佳實踐
     2. **例外情況**：某些 EC2 API 不支援資源級權限，此時 `Resource: "*"` 是唯一選項
     3. **考試判斷**：需要判斷是否為服務限制導致的必要配置
141. ★★ **S3 Bucket 公開讀取權限設定方法**
     1. **方法一：上傳時設定 ACL**：在 **S3 Console** 上傳物件時，直接授予 public read access（適合個別物件）
     2. **方法二：Bucket Policy**：設定 bucket policy 讓所有物件預設為 public read（適合整個 bucket）
     3. **考試判斷**：題目要求「所有物件都能被公開讀取」→ 兩種方法都正確
142. ★★ **Glacier Vault Lock 核心概念與合規保護**
     1. **定義**：提供 **immutable（不可變更）** 的刪除保護機制，鎖定後無法修改
     2. **用途**：合規要求（如保留期限）、強制保留期（例如保留 5 年才能刪除）
     3. **關鍵字識別**：題目出現「保留 X 年才能刪除」→ 需要 **Vault Lock**
143. ★★ **Glacier Vault Lock vs Vault Access Policy 差異比較**
     1. **Vault Lock**：immutable（鎖定後不可改）、可強制保留期、用於合規要求
     2. **Vault Access Policy**：可隨時修改、無法強制保留期、用於一般權限控制
     3. **考試判斷**：**合規 + 保留期限** = Vault Lock
144. 😇 **Glacier 特性與操作限制**
     1. **Archive 特性**：上傳後 **immutable**（不可修改）
     2. **支援操作**：只能 Upload、Download、Delete
     3. **適用場景**：長期封存、不需修改的資料
145. 😇 **Glacier Vault Lock 考試記憶口訣**
     1. **核心記憶**：Lock = 鎖死 = 合規保證
     2. **判斷標準**：看到保留期限要求 → 選 Vault Lock
