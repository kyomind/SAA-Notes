# Speed Notes for SAA

1. ★★ **VPC 與 Availability Zone 關係**
   1. VPC **跨越整個 region 的所有 Availability Zone**⭐️
   2. **每個 subnet 必須完全位於單一 Availability Zone**⭐️，無法跨 zone
   3. VPC **只存在於單一 region**
   4. **跨 region 通訊**要用 VPC Peering 或 Transit Gateway
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
5. ★★ **VPC 與 Subnet 抽象層次差異**⭐️
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
10. (刪除)
11. ★ **Network ACL 配置錯誤分析**⭐️
    1. **為何錯誤**：題目已指出可連接其他 EC2 instances，表示網路基本連通性正常
    2. **Network ACL 特性**：適合控制**整個 VPC 進出流量**，而非單一 EC2 instance 的流量控制
12. ★ **AWS Glue Job Bookmark 核心概念**
    1. **定義**：AWS Glue 的**狀態追蹤機制**，記錄 ETL job 已處理過的資料
    2. **運作原理**：持久化儲存 job 執行狀態（獨立於 job 本身）、追蹤已處理的資料位置/時間戳記
    3. **下次執行機制**：只處理新增的資料，**避免重複處理**
13. ★★ **Job Bookmark 應用場景與優勢**⭐️
    1. **題目關鍵字**：出現 "reprocessed old data" + "operationally efficient" 場景
    2. **技術優勢**：一鍵啟用、無需修改程式碼、自動追蹤處理進度、節省時間與成本
    3. **考試記憶點**：遇到 "避免重複處理資料" + "Glue ETL" 場景 → 優先想到 **Job Bookmark**
14. ★★ **SNI (Server Name Indication) 核心概念**⭐️
    1. **定義**：讓**單一 IP 地址/Load Balancer 處理多個域名的 SSL 憑證**
    2. **運作原理**：透過 **TLS handshake** 時客戶端帶上 hostname 來選擇對應憑證
    3. **ALB 實作**：單一 listener (HTTPS:443) 綁定多張 SSL 憑證，自動選擇正確憑證
15. ★★ **SNI 應用場景與優勢分析**
    1. **題目關鍵字**：出現 "multiple domains" + "SSL traffic" + "most cost-effective" 場景
    2. **核心優勢**：ALB **原生免費功能**、單一 listener 綁定多張憑證、自動管理無需重新設定⭐️
    3. **考試記憶點**：**多域名 SSL + ALB** → 優先選 **SNI**
16. 😇 **SNI 方案比較與限制**
    1. **SNI 優勢**：免費、ALB 原生支援
    2. **替代方案缺點**：獨立 ALB（成本高）、Wildcard 憑證（限同父域名）、CloudFront + ACM（額外服務複雜度）
    3. **SNI 限制**：舊瀏覽器不支援（但 2024 年影響極小）
17. ★★ **ELB 跨 region 部署限制**
    1. **核心限制**：**ELB 只能在單一 region 內運作**，無法跨 multiple regions
    2. **錯誤配置範例**：多個 region 的 EC2 instances + Auto Scaling 共用一個 ELB
    3. **正確解決方案**：每個 region 部署獨立的 ELB，搭配 Route 53 做跨 region 負載均衡⭐️
18. ★ A **gateway endpoint** is a gateway that you specify in your **route table** to access Amazon S3 from your VPC over the AWS network.⭐️
19. ★★ **AWS Config 核心功能與職責**
    1. **定義**：持續監控資源配置的合規性檢查工具
    2. **功能**：記錄資源配置變更歷史、用 Config Rules 評估是否符合規範、偵測到違規就標記為 `NON_COMPLIANT`
    3. **限制**：只能「**發現問題**」，無法執行修復⭐️，要自動修復需要搭配其他服務（如 EventBridge + Lambda）
20. ★★ **EventBridge 在合規自動化中的角色**
    1. **定義**：事件驅動的膠水，連接 Config 和後續動作
    2. **功能**：監聽 AWS Config 發出的 `NON_COMPLIANT` 事件、用 custom event pattern 過濾特定規則、觸發下游目標(Lambda/SNS)
    3. **職責**：充當橋樑，讓 Config 觸發自動化
21. ★★ **AWS 合規自動修復架構模式**
    1. **標準三件套**：Config（監控員）→ EventBridge（派遣員）→ Lambda（執行員）
    2. **運作流程**：AWS Config Rule（檢查 key 年齡）→ 偵測到違規 → EventBridge Rule（監聽 NON_COMPLIANT 事件）→ 觸發 → Lambda Function（執行 deactivate + delete）
    3. **應用場景**：IAM access keys 超過 90 天未 rotate → 自動停用刪除
22. ★ **合規自動化架構設計原理分析**⭐️
    1. **各元件職責分工**：Config（監控 + 評估）、EventBridge（事件路由）、Lambda（執行修復）
    2. **為何需要三個元件**：Config 無法執行修復、Lambda 需要被觸發才能執行、EventBridge 作為事件橋樑
    3. **考試陷阱識別**：錯誤選項只用 Config + SNS 通知（只是發警報，沒有**自動修復**）
23. (刪除)
24. ★★ **合規自動化常見錯誤架構分析**
    1. **錯誤 1：自建 Config Rule**：AWS 已有 **managed rule** `access-keys-rotated` 可直接用，自己寫 custom rule 純粹浪費時間
    2. **錯誤 2：EventBridge Scheduler + Batch**：錯誤架構為 EventBridge Scheduler（每 24 小時）→ AWS Batch（容器化作業）→ 刪除 keys
    3. **用錯工具問題**：**Batch** 是跑**大規模批次運算**的（資料處理、ML training），不是用來執行簡單 API 呼叫⭐️
25. ★ **AWS Batch 誤用問題分析**
    1. **維護複雜度**：需要設定 compute environment、job queue、job definition → 維護成本高
    2. **時效性問題**：**定時執行** vs **事件驅動**，錯過即時性，可能讓違規 key 多存活 24 小時
    3. **成本考量**：Batch 需要 EC2/Fargate 資源，Lambda 只在觸發時計費
26. 😇 **正確架構的技術優勢**⭐️
    1. **即時性**：Config Rule 偵測違規 → EventBridge Rule 監聽事件（毫秒級）→ Lambda 執行刪除
    2. **維護成本**：**Serverless** 架構，Lambda 執行完就結束，無需維護基礎設施
    3. **成本效益**：事件驅動模式，只在需要時執行
27. (刪除)
28. ★★ SQL Server → Aurora PostgreSQL 資料庫遷移挑戰
    1. **Schema 結構差異**：不同資料庫引擎的表格設計需要轉換
    2. **SQL 語法差異**：T-SQL vs PostgreSQL 語法不相容
    3. **資料搬移需求**：實際資料需要從 A 搬到 B
29. ★★ 資料庫遷移三大工具組合（SCT + DMS + Babelfish）
    1. **AWS SCT（Schema Conversion Tool）**：轉換 schema，把 SQL Server 的表格設計「翻譯」成 PostgreSQL 版本
    2. **AWS DMS（Database Migration Service）**：搬移資料，實際把資料從 A 搬到 B，可持續同步
    3. **Babelfish for Aurora PostgreSQL**：即時翻譯 SQL 語法，讓程式繼續用 T-SQL(MS-SQL 交易方言)，自動轉成 PostgreSQL 能懂的語法⭐️
30. 😇 **資料庫遷移常見錯誤組合分析**
    1. **只用 SCT + DMS**：資料庫搬好了，但 app code 會因語法不相容而出錯
    2. **只用 Babelfish**：app 能連接，但缺少 schema 轉換和資料搬移
    3. **完整解法必要性**：三個工具各司其職，缺一不可
31. (刪除)
32. ★★ **資料庫遷移完整解決方案架構**
    1. **資料庫層面（SCT + DMS）**：SCT 轉換表格結構 + DMS 搬移資料
    2. **應用程式層面（Babelfish）**：app 原本的 T-SQL 語法無需修改，Babelfish 負責翻譯
    3. **最小化程式碼修改**：關鍵在於 Babelfish 讓既有程式碼保持不變
33. ★ **資料庫遷移錯誤選項分析 - AWS AppConfig**
    1. **實際用途**：管理 app 的 configuration（feature flags、參數設定）⭐️
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
36. (刪除)
37. ★★ **S3 Cross-Region Replication (CRR) 核心概念**
    1. **定義**：**自動**將 S3 物件複製到不同 Region 的 bucket⭐️
    2. **必備條件**：源和目標 bucket 都要開 **Versioning**、必須是不同 Region、需要 IAM role 授權
38. ★★ **S3 CRR 複製行為特性**⭐️
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
44. ★ **ALB redirect 考試記憶點與錯誤選項**⭐️
    1. **正確方案**：Configure listener redirect rule
    2. **錯誤選項**：不需要 EC2 跑 nginx redirect、不需要 Lambda 處理 redirect、不需要 CloudFront（雖然也能做，但題目已有 ALB）
45. ALB listener 配置陷阱與限制
    1. **考試陷阱**："Create a new HTTP listener on port 80" 聽起來正確，但如果已有 port 80 listener 就無法新建
    2. **技術限制**：**同一 port 不能有多個 listener**，需要修改現有 listener 而非新建⭐️
46. ★★ EKS Autoscaling 雙層架構概念⭐️
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
    1. **錯誤原因**：能用但 **operational overhead 太高**、需要手動設定 scaling policies⭐️
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
    2. **地理位置限制**：只能在**同一個 AZ** 內多個 Instance 共用，**不能跨 AZ**⭐
54. 😇 **EBS 儲存類型選擇記憶要點**
    1. **成本最低**：Magnetic（或新世代的 Cold HDD sc1 / Throughput Optimized HDD st1）
    2. **IOPS 要求高**：Provisioned IOPS
    3. **通用場景**：General Purpose SSD
55. ★★ **DynamoDB Global Table** 跨區域災難復原核心概念⭐️
    1. **定義**：跨區域 DR 的標準解決方案
    2. **多主架構 (multi-master)**：每個區域都能讀寫，變更會自動同步到所有其他區域
    3. **最終一致性**：各區域間的同步是異步的，會達到**最終一致**
    4. **衝突解決**：使用 "**last writer wins**" 策略解決衝突
    5. **技術特性**：毫秒級延遲、保持應用狀態
56. ★★ **Route 53 DNS Failover 自動切換機制**
    1. **核心功能**：主區域掛了，自動路由到次區域
    2. **技術優勢**：**不需手動干預**，完全自動化故障轉移
57. 😇 **Well-Architected Tool 最佳實踐檢查服務**
    1. **功能**：自動掃描你的架構、給出改善建議
    2. **成本**：**免費服務**
58. (刪除)
59. ★★ **DynamoDB Global Secondary Index (GSI) vs Global Table 考試陷阱**
    1. **GSI 誤用問題**：GSI = 同一張表的額外查詢索引，**只存在於同一個 region**
    2. **Global Table 正確用途**：跨 region 的資料複製，**多主架構同步**
    3. **考試陷阱**：看到 "global" 就以為能跨區域 → 錯誤判斷
60. ★★ **Well-Architected Tool vs Compute Optimizer 工具選擇**
    1. **Well-Architected Tool**：檢查架構設計、給出最佳實踐建議、**免費服務**
    2. **Compute Optimizer**：優化資源規格（EC2 type、EBS size、Lambda memory），**只管資源規格不管架構設計**
    3. **考試判斷技巧**：題目要求 "架構最佳實踐" → Well-Architected Tool；題目要求 "省錢、調規格" → Compute Optimizer
61. ★★ **Predictive Scaling** 核心機制與特性
    1. **運作原理**
       - 使用 ML 分析 CloudWatch 歷史數據
       - **提前啟動** capacity 而非被動反應
       - 持續學習預測更準確
    2. **適用場景**
       - **規律性**流量高峰（每天、每週固定時間）
       - 應用啟動慢（容器/服務需要時間 warm up）
       - 成本優化需求（避免 over-provision）
62. ★★ **Auto Scaling 策略考試判斷標準**
    1. **Predictive Scaling 關鍵字**：predictable/regular pattern、long initialization time
    2. **Dynamic Scaling 關鍵字**：sudden/unpredictable spike、threshold-based triggers
    3. **Scheduled Scaling 關鍵字**：specific time schedule、定時排程
63. ★★ **Dynamic vs Predictive Scaling 核心差異分析**
    1. **Dynamic Scaling（反應式）**：CPU 到 threshold → 才開始加機器 → 等機器啟動 → 流量已經來了 → 結果：用戶體驗差 + 可能過載⭐️
    2. **Predictive Scaling（預測式）**：看歷史數據 → 知道流量何時來 → **提前**準備好 capacity → 結果：流量來時已經 ready
64. ★ **Auto Scaling 考試陷阱與判斷技巧**
    1. **常見陷阱**：題目說 "based on **historical** data" → 容易誤以為是 dynamic scaling
    2. **關鍵差異**：Dynamic 用歷史算 threshold，**觸發後才反應**；Predictive 用歷史**預測未來**，提前準備
    3. **判斷口訣**：看到 "forecasted/in advance/before peak" → Predictive；看到 "threshold/when CPU reaches" → Dynamic
65. ★★ **SSL/TLS 憑證匯入位置選擇**
    1. **AWS Certificate Manager (ACM)**：首選方案，Region 支援時優先使用
    2. **IAM Certificate Store**：備用方案，Region 不支援 ACM 時使用⭐️這東西已經比較少見
    3. 用 CLI 操作時，兩者皆可使用
66. ★ **SSL/TLS 憑證管理考試重點**
    1. **憑證匯入特性**：**第三方憑證**可匯入 ACM/IAM、**ACM 自己簽發**的憑證不能匯出
    2. **服務整合**：ALB/CloudFront 都支援從 ACM/IAM 載入憑證⭐️
    3. **實務建議**：優先用 ACM，有自動 renewal 提醒、整合更好
67. ★★ **CloudFront vs ACM/IAM 憑證管理差異**
    1. **CloudFront 限制**：憑證綁定單一 distribution、**不能匯出**重用於 EC2/ELB、**不是**憑證「儲存」位置
    2. **ACM/IAM 定位**：憑證**管理/儲存**中心
    3. **CloudFront 定位**：憑證**使用**端點
68. ★ **SSL 憑證考試關鍵字判斷技巧**⭐️
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
    1. **服務定位**：**Lift-and-shift 遷移的標準解法**⭐️，用於將 VM workloads 搬到 AWS。記住「**MGN = 遷移 = lift-and-shift**」就夠了
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
    2. **適用場景**：**檔案系統、物件儲存**的資料搬移（例如：NFS → EFS、S3，或 on-premises 檔案伺服器 → FSx）
    3. **功能限制**：不處理 VM、OS、應用程式
    4. **考試陷阱**：看到 "sync"、"replication" 就誤選
77. ★★ **AWS Replication Agent 在 MGN 中的核心地位**
    1. **必要步驟**：在每台 source VM 上安裝 **AWS Replication Agent** 是遷移的第一步⭐️
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
80. ★★ **Regional vs Edge-Optimized API Gateway 差異比較**⭐️
    1. **Regional API**：ACM 憑證**同一個 Region**、可自行搭配 CloudFront（更有彈性）、適合區域內客戶端或需要自訂 CDN 設定的場景
    2. **Edge-Optimized API**：ACM 憑證必須在 `us-east-1`、自動建立 AWS 託管的 CloudFront（無法自訂）、適合快速部署全球 API
    3. **考試判斷關鍵**：題目說 "serve clients in the same AWS Region" → 選 Regional 設定
81. 😇 **API Gateway 憑證配置記憶技巧**
    1. **Regional = 就地取材**：API 在哪憑證就在哪、不繞 CloudFront 直達目標、如在台北開店向台北市政府申請執照
    2. **Edge-Optimized = 全球配送**：憑證統一放 us-east-1（CloudFront 總部）、透過 CloudFront 分發全球、如做全球生意去總公司辦證照
82. ★ **Regional API Gateway 考試重點與陷阱**
    1. **核心規則**：**Regional API 的 ACM 憑證必須同 Region**（死規則）
    2. **題目難點**：混合了 API Gateway、自訂網域、SSL 憑證、DNS 設定概念
    3. **判斷原則**：考的是「細節規定」而非「架構判斷」
83. ★★ **API Gateway Private Certificate 錯誤選項分析**
    1. **Private Certificate (ACM PCA) 錯誤原因**：只能用於內部網路，不能對外公開、題目要求 public internet 的 HTTPS 連線 → 必須用 **Public Certificate**⭐️
    2. **類比理解**：Private Certificate = 公司內部員工證（辦公室內使用）、Public Certificate = 政府核發營業執照（對外營業）
84. ★★ **EFA vs ENA 高效能運算網路選擇**⭐️
    1. **EFA (Elastic Fabric Adapter)**：**OS-bypass** 技術、直接與網路硬體溝通、適合 **HPC、ML training**、MPI、NCCL 應用
    2. **ENA (Elastic Network Adapter)**：一般高速網卡、需經過作業系統處理封包、適合標準網路應用
    3. **技術差異**：EFA = ENA + OS-bypass 功能，延遲更低、吞吐量更高
85. ★★ **EFA 應用場景與考試關鍵字**
    1. **核心關鍵字**：HPC（高效能運算）、Machine Learning training、OS-bypass、MPI、NCCL、Inter-instance communication performance
    2. **典型應用**：自動駕駛 AI 訓練、基因定序分析、氣象模擬、財務風險建模、大量 EC2 實例互傳資料場景
    3. **考試判斷**：看到 HPC + OS-bypass → 直接選 EFA
86. 😇 **EFA 技術特性與限制**
    1. **支援平台**：只支援 **Linux**，Windows 上 EFA 等於只有 ENA 功能⭐️Windows 上只能用 ENA
    2. **包含關係**：**EFA 包含 ENA**，可執行所有 ENA 功能
    3. **記憶技巧**：ENA = 高速公路、EFA = 高速公路 + 專用快車道（OS-bypass）
87. ★ **AWS Systems Manager Fleet Manager 核心功能**
    1. **定義**：**AWS Systems Manager 的統一 UI 管理介面**，用於遠端管理 EC2 實例
    2. **適用範圍**：支援 AWS 雲端和 on-premises 的 nodes/EC2 instances
    3. **功能定位**：Systems Manager 眾多功能中的其中一項管理工具
88. ★ **AWS RAM 錯誤選項分析 - 軟體授權管理**
    1. **實際用途**：**跨 AWS 帳戶安全共享資源**，在 AWS Organizations 內共享資源（如 VPC 子網、License Manager 授權）⭐️重點是**共享**，而不是管理
    2. **考試陷阱**：看到 "Resource Access Manager" 可能誤以為能管理所有類型資源
89. ★ AWS Certificate Manager：**配置、管理和部署 SSL 憑證**
90. ★★ **AWS License Manager 核心概念與 BYOL 場景**⭐️
    1. **定義**：專門處理 **BYOL (Bring Your Own License)** 場景，將現有授權帶到 AWS 雲端使用
    2. **核心功能**：追蹤授權使用（例如 50 個 Windows Server 授權，綁定 CPU 數量）、阻擋超額使用（授權用完就不能再開 EC2）、通知機制（授權快用完要發警告）
91. ★★ **DynamoDB Auto Scaling 自動容量調整機制**
    1. **核心服務**：使用 **AWS Application Auto Scaling** 根據實際流量模式動態調整 provisioned throughput capacity
    2. **運作機制**：流量突增時自動提高讀寫容量避免 throttling、工作負載降低時自動減少容量避免浪費
    3. **適用範圍**：支援 DynamoDB table 和 Global Secondary Index 的容量自動調整
92. ★★ **DynamoDB Streams + Lambda 觸發機制核心概念**
    1. **定義**：DynamoDB Streams 是變更日誌機制，記錄所有數據修改事件（新增/更新/刪除），可觸發 Lambda 自動執行特定動作
    2. **工作流程**：DynamoDB 表格更新 → DynamoDB Streams 記錄變更 → Lambda 被自動觸發 → 執行你定義的邏輯
93. ★★ **DynamoDB Streams + Lambda 考試重點機制**⭐️
    1. **觸發方式**：**同步輪詢** (Lambda 主動 poll Streams，不是 push 模式)
    2. **處理特性**：**批次處理**（可一次處理多筆變更記錄）、**保證順序**（同一個 partition key 的變更順序保證）
94. ★★ **DynamoDB Streams + Lambda 方案選擇考試判斷**⭐️
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
    1. **Envelope Encryption 支援**：只有 SSE-KMS 提供標準的 **envelope encryption** 實作
    2. **自動金鑰輪替**：KMS 支援**每年自動輪替**，SSE-S3 無法控制，SSE-C 需自行管理
    3. **完整審計追蹤**：KMS 完整整合 **CloudTrail**，可追蹤誰用了金鑰、什麼時候用、執行什麼操作
98. ★★ **Envelope Encryption 運作機制解析**
    1. **加密流程**：檔案用 Data Key 加密 → Data Key 用 Master Key(KMS) 加密 → 兩者都存在 S3
    2. **技術原理**：KMS 單次只能加密 4KB，Data Key 負責大檔案加密（效能好），KMS 只加密 Data Key（安全性高）
    3. **儲存結構**：S3 同時存放加密檔案（大）和加密的 Data Key（小）
99. ★★ **S3 加密方案選擇考試判斷標準**⭐️
    1. **選 SSE-KMS 關鍵字**：Envelope encryption、Key rotation、**Audit trail**、Compliance requirements、Customer managed keys
    2. **不選 SSE-KMS 場景**：
       - Lowest cost → SSE-S3 更便宜
       - Simplest solution → SSE-S3 更簡單
       - You manage keys outside AWS → SSE-C
    3. **核心記憶**：題目要求 **envelope encryption + 自動金鑰輪替 + 審計追蹤** 三條件 → 只有 SSE-KMS 符合
100. ★★ **Envelope Encryption 詳細概念與安全優勢**
     1. **雙層保護架構**：即使 Data Key 被竊取，沒有 KMS Master Key 也無法解密檔案；即使 KMS 被入侵，攻擊者也需要同時取得加密的 Data Key
     2. **效能與安全平衡**：大檔案用對稱加密（Data Key，速度快）；小金鑰用非對稱加密（KMS，安全性高）
     3. **考試記憶技巧**：**信封（Envelope）= 雙層包裝**，內層保護內容物，外層保護信封本身
101. ★ Amazon Redshift Spectrum 核心功能
     1. **定義**：**擴展 Redshift 查詢能力**，讓 Redshift cluster **直接查詢 S3 中的資料**⭐️
     2. **資料支援**：支援結構化和半結構化資料（JSON、Parquet、ORC、Avro等）
     3. **應用場景**：大規模資料分析，**無需將 S3 資料搬移到 Redshift** 即可執行複雜查詢⭐️
102. ★★ S3 資料傳輸費用最佳化策略
     1. **免費傳輸規則**：Internet → S3 (upload)、**S3 → EC2 (同 Region)**、S3 → CloudFront
     2. **成本判斷原則**：**跨 Region = 收費，同 Region = 免費**
     3. **考試公式**：minimize data transfer cost + 處理 S3 資料 → **EC2 same Region as S3**⭐️
103. ★ 處理專有格式檔案的服務選擇
     1. **必選 EC2 場景**：vendor's proprietary software → 需要運算環境執行 → **EC2 是唯一選擇**
     2. **不適用服務**：Lambda（15 分鐘限制 + 記憶體不足）、Glue（不支援 proprietary format）、本地處理（傳輸費用爆炸）
     3. **核心考點**：特殊軟體需求 + 成本最佳化 → 選擇同 Region 的 EC2
104. ★ AWS **Health Dashboard** 服務差異比較⭐️
     1. **Personal Health Dashboard**：監控**你的 AWS 資源狀態**，針對特定帳號/資源，適用於「監控你的 EC2」場景
     2. **Service Health Dashboard**：監控**AWS 服務整體狀態**，區域/全球性事件，適用於「看 AWS 是否當機」場景
     3. **記憶口訣**：
        - Personal = 個人的 = 你的資源
        - Service = 服務的 = AWS 整體
105. ★★ AWS DataSync 核心定位與功能
     1. **服務定位**：**資料搬運專用服務**，專門處理 on-premises ↔ AWS 的大量資料傳輸⭐️
     2. **技術優勢**：全託管服務（無需自己寫 script 或購買商業軟體）、自動加速線上資料傳輸到 AWS 儲存服務
     3. **架構組件**：在 on-premises 部署 **DataSync Agent**（加速資料傳輸）、透過 **Direct Connect service endpoint** 連接 AWS 服務
106. ★★ DataSync + Direct Connect 私有連線架構
     1. **標準架構流程**：On-premises DataSync Agent → AWS Direct Connect（專線）→ Direct Connect service endpoint → AWS 儲存服務（S3/EFS/FSx）
     2. **核心優勢**：**私有連線**（不經過公網）、自動化傳輸管理、加速大量資料搬移
     3. **程式化連接要求**：使用 **Direct Connect service endpoint** 進行 AWS 服務的程式化連接
107. ★★ DataSync 連接端點與傳輸方向限制⭐️
     1. **來源端支援**：
        - On-premises → NFS/SMB/自建物件儲存
        - AWS 內部 → S3/EFS/FSx
        - Snowcone → 內建 agent
     2. **目標端限制**：**只能**是 AWS **儲存服務**（**S3/EFS/FSx**）
     3. **傳輸方向**：**單向往 AWS** 推送，不支援 AWS → On-premises
     4. **不支援服務**：
        - RDS/DynamoDB → 非檔案系統
        - 直接連接 Glacier → 需先到 S3
108. ★★ EBS io1 Queue Length 配置策略
     1. **Transaction-intensive 場景**：低延遲需求 → **低 queue length** + 高 IOPS
     2. **Throughput-intensive 場景**：高吞吐量需求 → 高 queue length
     3. **考試判斷**：看到 "transaction-intensive" / "low-latency" → 選低 queue length
109. ★★ EBS io1 IOPS 計算公式
     1. **計算公式**：最大 IOPS = Volume 大小(GiB) × 50
     2. **範例計算**：10 GiB → 最多 500 IOPS
     3. **記憶技巧**：io1 = **50:1 固定比率**⭐️
110. ★★ ACM vs ACM Private CA 憑證類型選擇
     1. **ACM (AWS Certificate Manager)**：Public certificate（公開信任）、對外服務使用、免費且自動更新
     2. **ACM Private CA**：Private certificate（僅內部信任）、企業內部 PKI、需額外付費
     3. **考試判斷**："publicly trusted" → ACM；"internal authentication" / "private PKI" → ACM Private CA
111. ★★ **ACM 證書來源與管理方式**
     1. **ACM 簽發證書**：免費、自動更新、只用於 AWS 服務、**不能匯出私鑰**⭐️可以自簽！本身就是一個 CA
     2. **導入第三方證書**：需手動更新、可用於任何地方、保留原 ARN
     3. **考試判斷**："issue certificate" → ACM 簽發；"import from third-party CA" → 導入外部；"export private key" → 不能用 ACM 簽發的
112. ★★ CloudWatch Agent + Parameter Store 監控架構⭐️
     1. **CloudWatch 預設不監控 memory**：只監控 CPU、Network、Disk performance
     2. **監控 memory 需求**：安裝 CloudWatch Agent → 讀取 Parameter Store 配置 → 發送 custom metric
     3. **考試判斷**："memory usage" + Auto Scaling → 必須用 CloudWatch Agent + Parameter Store
113. ★★ EC2 Instance Metadata 服務核心概念
     1. **定義**：EC2 提供的特殊端點 `169.254.169.254`，讓 instance 查詢自己的資訊（IP、Instance ID、IAM credentials 等）⭐️
     2. **核心優勢**：無需安裝工具、無需設定權限、即時反映狀態、完全免費。網址：`http://169.254.169.254/latest/meta-data/`
     3. **考試判斷**："running instance 內取得自己的資訊" → Instance Metadata
114. 😇 **EC2 Instance Metadata 可查詢資訊類型**
     1. **網路資訊**：Public/Private IP
     2. **身份資訊**：Instance ID、Security Groups、IAM Role credentials
115. ★ **AWS 會計費的項目**
     1. **Running EC2 Instance**：只要在運行就計費（按小時）
     2. **EBS Volumes**：無論 EC2 是否運行都計費（按儲存容量）
116. ★ **AWS 不計費的項目**
     1. **Stopped EC2 Instance**：停止後不計算運算費用
     2. **VPC 本身**：建立和使用 VPC 免費（但 VPC 內的資源如 EC2、NAT Gateway 等仍照常計費）⭐️
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
120. ★ **Athena 考試判斷標準**⭐️
     1. **關鍵字識別**：看到「分析 S3 資料」+「SQL 查詢」→ **Athena**
     2. **核心記憶**：Athena = S3 + SQL
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
125. ★★ **Requester Pays 重要特性與技術要求**⭐️
     1. **自動禁用匿名存取**：啟用後必須驗證身份才能收費（這是 feature 不是 bug，因為要知道跟誰收錢）
     2. **API 要求**：必須在 header 加 `x-amz-request-payer`
     3. **計費對象**：若 requester 用 IAM role，該 role 所屬 account 被收費
126. ★★ **Requester Pays 考試判斷與關鍵字識別**⭐️
     1. **題目關鍵字組合**："share data" + "lower transfer costs" + "disable anonymous access" → **Requester Pays 一次滿足三個需求**
     2. **錯誤選項陷阱**：
        - Cross-Region Replication → 反而增加成本
        - S3 Transfer Acceleration → 加速但不降成本
        - Pre-signed URL → 無法轉嫁流量費用
127. ★ **Requester Pays 錯誤方案分析 - CRR**
     1. **為什麼錯**：複製本身產生 **request fee + inter-region transfer fee**、相當於你先付一次流量費把資料搬到歐洲、**反而更貴**
     2. **記憶技巧**：CRR = 主動搬家，你付搬家費
128. ★ **Requester Pays 錯誤方案分析 - Cross-Account Access**
     1. **為什麼錯**：只是開權限讓對方下載、**流量費還是 bucket owner 付**、沒解決成本問題
     2. **記憶技巧**：Cross-account = 開門讓人拿，你付運費
129. ★★ SQS **Standard** Queue **重複訊息**根本問題⭐️
     1. **核心問題**：Standard Queue 的 **at-least-once delivery** 特性會造成重複訊息，這跟 visibility timeout 無關
     2. **架構特性**：Standard Queue 就是會重複，這是設計特性，不是 bug
     3. **錯誤認知**：
        - 調整 Visibility Timeout 無法防止 Standard Queue 本身產生重複訊息
        - 無法保證訊息不會被發送兩次到 queue 裡
130. ★★ FIFO Queue **解決重複訊息**的核心機制⭐️
     1. **Message Deduplication（去重）**：
        - 5 分鐘內相同的 `MessageDeduplicationId` 只會被接受一次
        - 自動過濾重複訊息
     2. **Exactly-once Processing（恰好一次處理）**：確保每個訊息只被消費一次，這才是解決「訂單被處理兩次」的關鍵
     3. **從根本解決**：FIFO Queue 從根本上保證 exactly-once delivery
131. ★ **Visibility Timeout 功能定位與限制**⭐️
     1. **實際功能**：讓訊息在被某個 consumer 處理時「暫時隱藏」
     2. **類比理解**：Visibility Timeout = 「這個訂單我先處理，其他人別碰」，但如果系統本來就會產生兩張一模一樣的訂單，調整這個參數根本沒用
     3. **核心限制**：只是處理「同一訊息被多個 consumer 同時處理」的問題，不是解決「重複訊息」的問題
132. ★★ **SQS 重複訊息考試判斷標準**
     1. **必選 FIFO 場景**：看到「重複處理」、「duplicate messages」問題
     2. **判斷邏輯**：如果「順序」很重要 → **必選 FIFO Queue**、如果只是「不能重複」→ **優先選 FIFO Queue**
     3. **核心記憶**：Standard Queue 架構問題不是參數調整可以解決的，需要 FIFO Queue 從根本解決
133. ★★ ARN 結構解析與 Resource ID 識別
     1. **ARN 標準結構**：`arn:aws:service:region:account-id:resource-type/resource-id`
     2. **結構拆解範例**：`arn:aws:ds:us-east-1:123456789012:directory/d-1234567890`
        - Partition: `aws`
        - Service: `ds` (Directory Service)
        - Region: `us-east-1`
        - Account ID: `123456789012`
        - Resource Type: `directory`
        - Resource ID: `d-1234567890` (Directory ID)
     3. **核心記憶**：ARN **從左到右越來越具體**，最右邊就是 Resource ID⭐️
134. ★ ARN 考試常見陷阱與錯誤選項
     1. **Statement ID (Sid) 混淆**：`DirectoryTutorialsDojo1234` 是 Policy 內的自訂標籤，不是 Directory ID 或 Directory name
     2. **Account ID 誤選**：`987654321012` 是 12 位數字的帳號 ID，位於 ARN 中間位置
     3. **Directory ID 格式**：正確格式通常是 `d-xxxxxxxxxx`
135. 😇 **ARN 考試判斷技巧與記憶要點**
     1. **識別原則**：看 ARN 最後一段 = Resource ID
     2. **格式記憶**：Directory ID (`d-xxx`)、Account ID (12位數字)、Sid (自訂標籤)
     3. **考試重點**：題目在考你識別 ARN 各個部分的能力
136. ★★ IAM Policy 核心結構與組成元素
     1. **基本結構**：`Version` (Policy 語法版本，固定值 `2012-10-17`) + `Statement` (權限規則陣列)
     2. **Statement 核心元素**：`Sid` (Statement ID，可選識別標籤)、`Effect` (Allow/Deny)、`Action` (允許的操作)、`Resource` (資源範圍 ARN)
     3. **Policy 定義**：定義 AWS 資源的存取權限
137. ★★ **IAM Policy Statement 實例解析**
     1. **Statement 1 範例**：Effect: Allow、Action: `ds:*` (Directory Service 所有操作)、Resource: 特定 Directory ARN → **只能管理指定的 AD 目錄**
     2. **Statement 2 範例**：Effect: Allow、Action: `ec2:*` (EC2 所有操作)、Resource: `*` (所有資源) → **可對任何 EC2 實例做任何事**
     3. **萬用字元 `*`**：代表「全部」，用於 Action 或 Resource
138. ★★ **IAM Policy 考試重點與判斷原則**
     1. **Effect 優先權**：`Deny` 優先權高於 `Allow`⭐️
     2. **Action 格式**：服務前綴 + 操作（例如 `s3:GetObject`、`ec2:*`）
     3. **Resource 指定**：使用 ARN 格式指定資源範圍，或用 `*` 表示所有資源⭐️
139. ★ IAM Policy 實務判斷標準與最佳實踐⭐️
     1. **Action 配置**：寬鬆 (`*` 所有操作) vs 嚴謹 (明確列出操作)
     2. **Resource 配置**：寬鬆 (`*` 所有資源) vs 嚴謹 (指定特定 ARN)
     3. **Effect 配置**：Allow (允許) vs Deny (明確禁止)
140. 👀 IAM Policy 考試陷阱與特殊情況
     1. **`Resource: "*"` 警覺點**：通常不是最佳實踐
     2. **例外情況**：某些 EC2 API 不支援資源級權限，此時 `Resource: "*"` 是唯一選項
     3. **考試判斷**：需要判斷是否為服務限制導致的必要配置
141. ★★ S3 Bucket **公開讀取**權限設定方法⭐️
     1. **方法一：上傳時設定 ACL**：在 **S3 Console** 上傳物件時，直接授予 public read access（適合個別物件）
     2. **方法二：Bucket Policy**：設定 bucket policy 讓所有物件預設為 public read（適合整個 bucket）
     3. **考試判斷**：題目要求「所有物件都能被公開讀取」→ 兩種方法都正確
142. ★★ Glacier Vault Lock 核心概念與合規保護⭐️
     1. **定義**：提供 **immutable（不可變更）** 的刪除保護機制，鎖定後無法修改
     2. **用途**：合規要求（如保留期限）、強制保留期（例如保留 5 年才能刪除）
     3. **關鍵字識別**：題目出現「保留 X 年才能刪除」→ 需要 **Vault Lock**
143. ★★ Glacier Vault Lock vs Vault Access Policy 差異比較
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
146. ★★ **Instance Store 核心特性與資料持久性**
     1. 資料存在**實體主機的本地磁碟**
     2. Instance 終止 → 資料**永久消失**
     3. **無法 Stop**，只能 Terminate
     4. 適合暫存、可重建的資料（如 cache、buffer、臨時運算）
147. ★★ **Instance Store 終止後資料處理考試陷阱**
     1. ✅ **正確答案**：自動刪除（資料永久消失）
     2. ❌ **錯誤選項 1**：保留在 S3（錯，只有 AMI template 在 S3）
     3. ❌ **錯誤選項 2**：可恢復（錯，資料永久消失無法恢復）
148. 😇 **Instance Store 考試記憶口訣**
     1. **核心記憶**：Instance Store = 臨時儲存，關機就沒了
     2. **類比理解**：實體主機的硬碟，機器關了資料就不見
     3. **考試判斷**：題目問「終止後資料如何」→ 選「自動刪除」
149. ★★ **SSE-S3 加密方案識別與特徵**
     1. **判斷依據**：Header 是 `x-amz-server-side-encryption`（沒有指定 KMS）
     2. **管理特性**：S3 自動管理密鑰、自動輪替
     3. **方案定位**：最簡單的加密方式
150. ★ **SSE-KMS vs SSE-S3 header 差異比較**
     1. **SSE-KMS header**：`x-amz-server-side-encryption:aws:kms` 並需指定 KMS key ID
     2. **SSE-S3 header**：`x-amz-server-side-encryption`（無需額外參數）
     3. **考試判斷**：看 header 格式判斷加密方式
151. ★★ **Storage Gateway 類型選擇核心差異**
     1. **Volume Gateway (iSCSI)**：協定 iSCSI、作為**虛擬硬碟**掛載、適用於資料庫和需要格式化檔案系統的應用
     2. **File Gateway (NFS/SMB)**：協定 NFS/SMB、作為**網路共享資料夾**、適用於檔案伺服器和檔案存取應用
     3. **核心記憶**：Volume Gateway 給需要「硬碟」的程式，File Gateway 給需要「資料夾」的程式⭐️
152. ★ **Storage Gateway 應用場景判斷標準**
     1. **Volume Gateway 場景**：PostgreSQL 需要儲存空間（資料庫需要掛載虛擬硬碟）
     2. **File Gateway 場景**：多台伺服器共享文件（NFS 本來就是給多台共享用）
     3. **考試判斷關鍵字**：Database/Block Storage/iSCSI → Volume Gateway；File Share/NFS/SMB/共享資料夾 → File Gateway
153. ★★ **Elastic IP (EIP) 綁定對象核心概念**
     1. **可綁定對象**：只能綁定在 **EC2 的 ENI** 或 **NAT Gateway** 上
     2. **不能綁定對象**：Internet Gateway (IGW) 不需要也不能綁定 EIP
     3. **IGW 角色定位**：是路由器概念，負責 route table 中的 `0.0.0.0/0 → IGW` 轉送，不是 endpoint
154. 😇 **IGW vs NAT Gateway vs EIP 記憶比喻**
     1. **IGW**：像家社區的大門（本身沒有門牌地址，只是出口）
     2. **EIP**：像家門號（綁在 NAT 或 EC2）
     3. **NAT Gateway**：像家裡的 WiFi Router（對外只有一個公網 IP）
155. ★★ **NAT Gateway 連接 Internet 的完整架構流程**
     1. **核心流程**：Private Subnet EC2 (預設路由 0.0.0.0/0 → NAT Gateway) → NAT Gateway → Internet Gateway (IGW) → Internet
     2. **關鍵事實**：NAT Gateway **不會直接連 Internet**，必須透過 IGW 中繼⭐️
     3. **位置要求**：NAT Gateway 須位於 **公有子網（Public Subnet）**，才能透過 IGW 出站
156. ★ **NAT Gateway 的 Route Table 配置規則**
     1. **私有子網配置**：只需在 Private Subnet Route Table 填 `0.0.0.0/0 → NAT Gateway (nat-xxxx)`
     2. **NAT Gateway 自動轉送**：NAT Gateway 背後的「去 Internet 的路由」是 AWS 內建的，**不需手動設定**
     3. **內建行為**：NAT Gateway 會自動將出站流量遞送到 IGW
157. ★★ **NAT Gateway 考試陷阱與常見錯誤**
     1. **陷阱 1：混淆路由責任**：誤以為 NAT Gateway 直接 NAT 後就能出網（錯），實際上沒有 IGW 它根本出不了 VPC
     2. **陷阱 2：私有子網指向 IGW**：私有子網的 Route Table 指向 NAT Gateway，**不是指向 IGW**
     3. **考試公式**：**私有子網 → NAT Gateway → IGW → Internet**（題目會直接考這個順序）
158. 😇 **NAT Gateway 快速記憶法與實務理解**
     1. **記憶口訣**：NAT Gateway 是公有子網的服務，用 **EIP + IGW** 幫私有子網連外
     2. **簡化版**：NAT 站在公有子網，IGW 是它的門⭐️
     3. **架構理解**：NAT Gateway 只負責 NAT 轉換，不負責出 VPC，IGW 才負責對外連接
159. ★★ **Route Table 四種 Target 快速判別（必背）**
     1. **igw-xxxx → 公網**：Internet Gateway，對外連接
     2. **nat-xxxx → 私有網出公網**：NAT Gateway，私有子網存取公網
     3. **vpce-xxxx → S3/DynamoDB**：Gateway Endpoint，VPC 內安全存取 AWS 服務
     4. **tgw-xxxx → Transit Gateway**：跨 VPC / 混合網路連接
160. ★★ **VPC 與 Subnet 的層級劃分（Region vs AZ）**
     1. **VPC 層級**：**Region 級別**（建立時選定一個 region，無法跨 region）
     2. **Subnet 層級**：**AZ 級別**（建立時必須指定一個 AZ，無法跨 AZ）
161. ★ **Multi-AZ 架構的基礎設計**
     1. 一個 VPC 可以有**多個 subnet 分散在不同 AZ**（這就是 Multi-AZ 架構的基礎）
     2. 每個 subnet 只能屬於**一個 AZ**，但一個 AZ 可以有多個 subnet
162. ★★ **跨 AZ 高可用部署的考試陷阱**
     1. **常見錯誤**：建立多個 VPC 來實現跨 AZ 高可用
     2. **正確答案**：在多個 AZ 的 subnet 部署 EC2（在同一個 VPC 內）
     3. **記憶要點**：高可用需要的是跨 AZ 的 subnet，而不是多個 VPC
163. ★★ **Kinesis Data Streams 選擇原因：訊息佇列的順序性與可靠性**
     1. **不能遺失**：資料會**持久化儲存**（預設 24 小時，最長 365 天）⭐️
     2. **不能重複**：每筆資料有**唯一的 Sequence Number**⭐️
     3. **順序保證**：同一個 **shard 內的資料嚴格按順序處理**⭐️
164. ★ **Kinesis Data Streams 核心概念與架構**
     1. **基本流程**：訊息 → Kinesis Data Streams (多個 shards) → EMR 消費
     2. **Shard**：Kinesis 的基本單位，每個 shard 保證順序
     3. **Partition Key**：決定訊息分配到哪個 shard
     4. **Sequence Number**：Kinesis 自動分配，確保順序
165. ★ **Amazon Data Firehose 為何不適合順序保證場景**
     1. **設計目的**：Firehose 主要用於**將實時流數據傳遞到數據湖、數據倉庫、分析服務**⭐️
     2. **可靠性保證**：確保**可靠的數據傳遞**，但**不保證消息的順序性**
     3. **考試判別**：需要嚴格的順序保證 → Kinesis；只需可靠傳遞 → Firehose
166. ★★ **NAT Gateway 核心概念與架構需求**
     1. **核心目的**：讓 **Private Subnet 的 EC2 能出網但不被外界直接存取**
     2. **標準答案**：Deploy a NAT Gateway in a public subnet, and add a route from the private subnet to the NAT Gateway
     3. **安全原則**：Internet 只能看到 NAT Gateway 的 Public IP，Private EC2 只能主動連出去，不能被動被打進來
167. ★★ **NAT Gateway 完整架構流程與配置要求**
     1. **架構流程**：Private EC2 → NAT Gateway（Public Subnet）→ Internet Gateway → Internet
     2. **NAT Gateway 必要條件**：必須放在 **Public Subnet**、必須綁定 **Elastic IP (EIP)**
     3. **Private Subnet 路由配置**：Route Table 設定 `0.0.0.0/0 → NAT Gateway`
168. ★ **NAT Gateway 技術限制與費用考量**
     1. **協定支援**：只支援 **IPv4**（IPv6 場景需使用 **Egress-only Internet Gateway**）
     2. **費用結構**：每小時固定費用 + Data Processing 費用 + EC2 Data Transfer 費用
     3. **考試對比**：題目常拿費用來比較「NAT Instance vs NAT Gateway」
169. 😇 **NAT Gateway 考試記憶技巧與判斷要點**
     1. **記憶口訣**：要出網但不給人打進來 → **Private + NAT GW + IGW**
     2. **架構順序**：EC2 (Private) → NAT GW (Public) → IGW → Internet
     3. **關鍵字識別**：看到「主動連外」+「不被外界存取」→ NAT Gateway 架構
170. ★★ **VPC Subnet 類型配置與路由設定**
     1. **Public Subnet 配置**
        - Route Table 設定 `0.0.0.0/0 → Internet Gateway`
        - 放置 **NAT Gateway**（綁定 Elastic IP）
     2. **Private Subnet 配置**
        - **不直接指向 IGW**
        - Route Table 設定 `0.0.0.0/0 → NAT Gateway`
171. ★ **NACL Subnet 層級防禦策略**
     1. **粗暴防禦**：擋整個 IP 段（例如封鎖某國家 IP range）
     2. **DDoS 攻擊時快速封鎖來源網段**
172. ★★ **NACL vs Security Group 規則差異**
     1. **Security Group 限制**：只能 **allow**，無法明確 **deny**
     2. **NACL 優勢**：可設定 **explicit deny** 規則優先於 allow
     3. **考試重點**：題目強調「必須 deny」或「subnet 層級防護」→ **NACL**
173. 😇 **NACL 合規需求與實務場景**
     1. **合規需求**：某些產業要求「**網路層防禦**」
     2. **Defense in Depth**：NACL 作為多層防禦的一環
     3. **實務範例**：NACL 擋 `123.45.0.0/16`（整個惡意網段），Security Group 只允許 `10.0.1.5/32`（特定後端服務）
174. ★★ **AWS 多帳戶治理關鍵字識別**
     1. **題目關鍵字**：「multi-account AWS environment」→ 需要管理多個 AWS 帳戶
     2. **監控需求**：「dashboard for continuous detection」→ 要有集中監控面板
     3. **合規檢測**：「policy non-conformance」→ 要能檢測政策違規
175. ★★ **AWS Control Tower 核心功能**
     1. **Landing Zone**：自動建立符合最佳實踐的多帳戶環境架構
     2. **Account Factory**：用標準化流程快速開新帳戶（工廠式生產）
     3. **Guardrails 護欄**：兩種類型的治理機制
176. ★★ **Guardrails 護欄機制詳解**
     1. **Preventive**：直接**阻止違規操作**（例如禁止關閉 CloudTrail）
     2. **Detective**：持續**監控並標記違規行為**
     3. **核心概念**：如「公司規章制度」，套用在 **OU 層級**，OU 底下所有帳戶都受約束
177. ★ **Control Tower vs Organizations 考試對比**
     1. **AWS Organizations**：僅為「帳戶容器」，缺乏完整治理機制和儀表板
     2. **AWS Control Tower**：完整的多帳戶治理解決方案，包含監控面板
     3. **考試判別**：多帳戶管理 + 集中治理 + 合規監控 → **Control Tower**
178. ★★ **AWS Config 配置合規檢查核心功能**
     1. **IAM 密碼政策檢查**：使用 `IAM_PASSWORD_POLICY` 規則自動檢測密碼政策是否符合要求
     2. **持續記錄**：資源配置變更的持續監控
     3. **自動評估**：配置是否符合預設規則的自動判斷
179. ★★ **AWS Config 三大核心能力**
     1. **持續記錄**：資源配置變更的完整追蹤
     2. **自動評估**：配置是否符合規則的自動檢查
     3. **合規報告**：集中呈現違規項目的儀表板
180. ★ **Config vs 其他服務考試對比**
     1. **AWS Config**：檢查資源配置是否合規 ✅
     2. **CloudWatch**：監控指標、日誌 ❌（不檢查配置合規）
     3. **IAM Policy**：控制權限 ❌（無法強制密碼長度）
181. 😇 **Config 企業實務應用場景**
     1. **多帳戶整合**：與 Organizations 整合，跨多帳戶收集合規資料
     2. **集中管理**：統一在 master account 查看儀表板
     3. **記憶點**：看到「**自動檢查配置是否合規**」→ **AWS Config**
182. ★★ **AWS Config 限制：無法阻止不合規操作**
     1. **Config 性質**：**Detective（偵測型）** 控制，只能事後檢查並標記違規
     2. **無法即時阻止**：不能在用戶改密碼時阻止不合規的密碼
     3. **功能定位**：持續監控和報告，非預防性控制
183. ★ **預防 vs 偵測型控制對比**
     1. **IAM Password Policy**：**Preventive（預防型）**，在帳戶層級強制密碼規則
     2. **AWS Config**：**Detective（偵測型）**，持續驗證政策沒被改掉
     3. **最佳實務**：兩者搭配使用，預防 + 持續監控
184. ★★ **Redshift Cross-Region Snapshot Copy** 核心機制與災難復原
     1. 啟用後，所有新的 manual + automatic snapshots 都會自動複製到指定 region
     2. 需要對每個 cluster 個別啟用，可設定 destination region 保留天數
     3. **DR 場景標準問法**：如何在另一個 region 快速恢復 Redshift？ → **Cross-region snapshot copy**
185. ★★ **Redshift vs 其他服務跨 Region 機制比較**
     1. **RDS**：Automated backup 只能同 region，需用 read replica 或手動 copy snapshot
     2. **Redshift**：內建 cross-region snapshot copy 功能
     3. **S3**：Cross-Region Replication (CRR)
186. ★ **Redshift Cross-Region Snapshot Copy 考試關鍵字與限制**
     1. **關鍵字識別**："copy Redshift data to another region"、"disaster recovery for data warehouse" → Cross-region snapshot
     2. **技術限制**：只複製 snapshot（不是即時同步），要從 snapshot restore 才能用（不是 active-active），會產生跨 region 數據傳輸費用
187. ★★ **RDS Stop vs Snapshot + Terminate 成本策略差異**
     1. **Stop instance 問題**：仍收 **storage 費用**（EBS volumes 持續計費）、7 天自動重啟限制（AWS 政策）、省不了多少（storage 是大頭成本之一）
     2. **Snapshot 循環優勢**：Snapshot 通常比原始 volume 小（壓縮 + incremental）、Terminated 後不付 instance 也不付 storage、只在使用日付完整費用
188. ★ **RDS 成本優化考試陷阱與判斷要點**
     1. **常見陷阱**：題目用「stop」當干擾選項，測試是否知道 **Stop ≠ 完全不收費**
     2. **正確判斷**：間歇性工作負載用 **snapshot pattern** 更划算

189. ★★ **CloudWatch Logs 觸發 EC2 Action 自動化流程**
     1. **核心流程**：CloudWatch Logs (文字) → Metric Filter (關鍵字匹配) → Custom Metric (數值) → Alarm (觸發) → EC2 Action (執行)
     2. **關鍵角色**：Logs 本身是「啞巴」，必須透過 **Metric Filter** 轉換成 Metric 才能觸發 Alarm
     3. **應用場景**：監控 Application Error（如 "Database connection timeout"）並自動重啟 EC2
190. ★ **CloudWatch Alarm 的 EC2 Action 功能**⭐️
     1. **內建動作**：支援 **Reboot**、Stop、Terminate、Recover 四種動作
     2. **運作機制**：Alarm 進入 `ALARM` 狀態時，自動呼叫 EC2 API（如 `RebootInstances`），**無需 Lambda 或 SNS**⭐️
     3. **考試判斷**：題目問「最簡單/原生」的自動重啟解法 → **CloudWatch Alarm EC2 Action**
191. 😇 **CloudWatch EC2 Action 限制與實務**
     1. **帳號限制**：只能操作 **同一個 AWS 帳號** 的 EC2
     2. **實務建議**：這是 **Reactive (被動)** 作法，治標不治本；更可靠的做法是 Auto Scaling + Health Check
     3. **多重動作**：一個 Alarm 可以設定多個 Action（例如：重啟 EC2 + 發送 SNS 通知）
192. ★★ **Textract + Comprehend Medical 醫療文件處理組合**
     1. **解題流程**：Amazon Textract（從 PDF 提取文字 OCR）→ Amazon Comprehend Medical（識別醫療敏感資訊 PHI）
     2. **技術整合**：S3 存 PDF 報告 → Textract 轉文字 → Comprehend Medical 偵測 PHI → HIPAA 合規處理
     3. **方案優勢**：兩者都是 API call（Lambda 直接呼叫無需重構）、Comprehend Medical 原生支援 HIPAA、最小改動實現合規
193. 😇 **Textract + Comprehend Medical 考試重點**
     1. **服務定位**：Textract = PDF/圖片文字提取標準答案、Comprehend Medical = AWS 唯一針對醫療文本的 NLP 服務
     2. **關鍵字識別**：看到 HIPAA + PHI + 醫療文件 → 選 Comprehend Medical
     3. **考試記憶**：醫療場景的文件處理必然是這兩個服務的組合
194. ★★ **AWS 託管服務整合核心概念：Function 中呼叫兩個 API**
     1. **整合實作**：在 Lambda function 中使用 AWS SDK 直接呼叫 **Textract API** 和 **Comprehend Medical API**
     2. **「minimal changes」原理**：AWS 託管服務整合就是 API call，只需在現有程式碼中加幾行呼叫
     3. **考試判斷**：看到「integrate」+ 託管服務 → 選擇 API 整合而非複雜部署方案
195. ★★ **IAM Identity Center 多帳戶統一身分管理核心概念**
     1. **考試重點**：**統一身分管理 + 最小權限原則**的實作方式
     2. **標準答案**：**IAM Identity Center + Permission Sets** 是現代企業多帳戶架構的正確解法
     3. **核心優勢**：集中管理（一個地方管理所有團隊權限）、Permission Sets（預先定義的政策集合）、最小操作負擔（無需逐一設定 IAM policies）
196. ★★ **IAM Identity Center 架構設計與權限指派機制**
     1. **Groups 組織結構**：依團隊建立（Data Science Team、Engineering Team、Compliance Team）
     2. **Permission Sets 權限模板**：依職責建立（AuroraReadOnly、EFSDataAccess、ComplianceAudit）
     3. **指派機制**：Team Groups 綁定對應 Permission Sets，系統自動產生對應 IAM roles
197. 😇 **傳統 IAM Users/Groups 方式的管理成本問題**
     1. **重複設定問題**：每個 AWS Account 都要重複設定相同權限
     2. **維護複雜度**：修改權限要改多個地方，無法跨 Account 統一管理
     3. **管理成本**：人力成本高，容易出錯
198. ★★ **IAM Identity Center 三大核心優勢與現代架構最佳實務**
     1. **Single Sign-On**：一次登入，存取所有 AWS accounts
     2. **Permission Sets**：預先打包好的權限集合，直接套用
     3. **Central Management**：跨 Organizations 集中管理
     4. **考試判別**：現代 AWS 多帳號架構最佳實務，傳統單一 Account 的 IAM 管理已不適用企業級場景
199. ★★ **AWS Transit Gateway 集中式網路管理核心概念**
     1. **考試重點**：**集中式網路管理**，解決大量 VPC 互連的管理複雜度
     2. **傳統痛點**：VPC Peering（N 個 VPC 需要 N(N-1)/2 條連線管理地獄）、VPN（每個 VPC 都要建立獨立 VPN 重複工作）、跨 Region 無法統一管理
     3. **核心架構**：**Hub-and-Spoke 模型**，每個 Region 建立一個 TGW，用 **TGW Peering** 串接跨 Region
200. ★★ **Transit Gateway 四大核心優勢**
     1. **單點連接**：每個 VPC/VPN 只需連到 TGW，不用管其他 VPC
     2. **自動路由**：新增 VPC 時自動可達所有網路
     3. **跨 Region 支援**：TGW Peering 支援跨 Region 互連
     4. **高擴展性**：支援數千個 VPC 連接
201. ★ **Transit Gateway Regional Service 限制與配置要求**
     1. **Regional 限制**：每個 Region 需要獨立的 TGW
     2. **跨 Region 方案**：必須用 **TGW Peering Connection**
     3. **Peering 規則**：只能連接不同 Region 的 TGW
202. ★★ **Transit Gateway vs VPC Peering 考試判斷標準**
     1. **選 Transit Gateway 關鍵字**：大量 VPC 互連、VPN 集中管理、跨 Region 網路、"centrally manage"、"hub and spoke"
     2. **VPC Peering 適用場景**：點對點連接，適合少量 VPC
     3. **Transit Gateway 適用場景**：中心化管理，適合大規模網路架構
203. ★★ **Direct Connect Gateway 錯誤選項分析與考試陷阱**
     1. **錯誤選項設計**：巧妙混合三個概念混淆考生（Direct Connect Gateway + Public Virtual Interface + LAG）
     2. **核心錯誤**：Direct Connect Gateway **不支援 Public Virtual Interface**，只能建立 **Private Virtual Interface**
     3. **Public VIF 用途**：存取 AWS 公開服務（S3、DynamoDB），**不適合 VPC 互連**
204. 😇 **LAG (Link Aggregation Group) 功能定位與場景無關性**
     1. **LAG 實際用途**：把多條 Direct Connect 連線綁成一條，**增加頻寬**
     2. **場景不符**：題目要解決「**連接拓撲**」問題，不是頻寬問題
     3. **考試陷阱**：LAG 在此場景完全無關，純屬混淆選項
205. ★ **Direct Connect Gateway 限制與適用場景**
     1. **主要用途**：**on-premises → AWS** 的私有連線
     2. **跨 Region 能力**：可連多個 Region 的 VPC，但不適合大規模 VPC 間互連
     3. **VPN 整合限制**：無法處理 VPN connections 的集中管理
206. ★★ **Direct Connect vs Transit Gateway 考試對比表**
     1. **用途對比**：Direct Connect（On-premises 專線）vs Transit Gateway（VPC/VPN 集中管理）
     2. **跨 Region 支援**：Direct Connect（透過 DX Gateway）vs Transit Gateway（透過 TGW Peering）
     3. **整合能力**：Direct Connect（不支援 VPN 整合、間接支援 VPC 互連）vs Transit Gateway（原生支援 VPN、原生支援 VPC 互連）
207. 😇 **考試陷阱識別技巧與判斷原則**
     1. **混淆選項特徵**：提到不相關的技術細節（如本題的 LAG）通常是混淆選項
     2. **核心判斷**：題目問「**網路拓撲架構**」，不是「**連線頻寬優化**」
     3. **記憶要點**：Focus on 題目真正要解決的問題類型
208. ★★ **AWS Direct Connect 專用私有網路專線核心特性**
     1. **不走公網**：從企業機房直接拉專線到 AWS
     2. **固定頻寬**：1Gbps 或 10Gbps 固定頻寬選項
     3. **穩定延遲**：比 VPN over Internet 延遲更穩定
     4. **安全性**：流量不經過公共網路，完全私有連線
209. ★★ **AWS VPN CloudHub 多站點互連核心概念**
     1. **服務定位**：讓多個 on-premises 站點透過 **AWS 當中繼站互連**
     2. **架構特性**：基於 VPN 的 **hub-and-spoke** 架構，使用同一個 **Virtual Private Gateway**
     3. **流量路徑**：Branch A ↔ Branch B 流量會經過 AWS（AWS 作為中繼點）
     4. **成本優勢**：低成本的多站點互連方案
210. ★ **VPN CloudHub 適用場景與限制**
     1. **適用場景**：多個分公司/辦公室需要互連、預算有限不需要 Direct Connect、流量不大的分支機構網路
     2. **技術限制**：只能連 VPN，基於 VGW（功能相對有限）
     3. **考試關鍵字**：「multiple remote offices」+「low cost」→ **VPN CloudHub**
211. 😇 **VPN CloudHub vs Transit Gateway 對比**
     1. **VPN CloudHub**：只能連 VPN，基於 VGW，功能較簡單
     2. **Transit Gateway**：可連 VPC + VPN，功能更強大，支援更複雜網路拓撲
     3. **選擇依據**：簡單多站點 VPN 互連選 CloudHub，複雜混合網路選 Transit Gateway
212. ★★ **S3 強制加密的 Bucket Policy 實作機制**
     1. **核心問題**：S3 加密是**物件層級**設定，每個物件上傳時可選擇是否加密
     2. **強制方案**：用 **Bucket Policy 擋掉不符合條件的上傳請求**
     3. **實作方式**：Policy 設定 `"Effect": "Deny"` + `"StringNotEquals": {"s3:x-amz-server-side-encryption": "AES256"}` 拒絕沒帶正確 header 的上傳
213. ★★ **S3 加密方案選擇與金鑰自動輪替對比**
     1. **SSE-S3**：AWS 管理、內建自動輪替（不可關閉）、**免費**
     2. **SSE-KMS (AWS managed)**：AWS 管理、每年自動輪替、部分免費
     3. **SSE-KMS (Customer managed)**：你管理、預設關閉需手動啟用、**付費**
     4. **考試判斷**：題目要求**金鑰每年自動輪替**且未提及精細控制 → **SSE-S3 最佳解**
214. ★ **SSE-S3 vs SSE-KMS 考試選擇標準**
     1. **選 SSE-KMS 關鍵字**：自訂金鑰權限、精細控制、Customer managed keys、Compliance requirements
     2. **選 SSE-S3 場景**：最低成本、最簡單方案、自動輪替需求、**無特殊控制要求**
     3. **核心記憶**：沒明確要求複雜功能時，SSE-S3 是預設最佳解
215. 😇 **S3 加密 Bucket Policy 實務開發建議**
     1. **SDK 實作**：PUT 請求帶 `ServerSideEncryption: 'AES256'` 參數
     2. **Fail-safe 設計**：設定 Bucket Policy 後，忘記帶參數的上傳直接失敗（403 Forbidden）
     3. **開發原則**：寧願上傳失敗，也不存不加密的檔案
216. ★★ **SSE-S3 金鑰輪替機制與限制**
     1. **管理方式**：金鑰由 **AWS 完全管理**，用戶無法介入
     2. **輪替特性**：**自動輪替是內建機制**，無法調整頻率或關閉
     3. **控制需求**：想**控制輪替頻率**必須使用 **Customer-managed KMS key**⭐️
217. ★★ **Custom Identity Broker 核心概念與應用場景**
     1. **應用場景**：企業用 **LDAP 管理員工身份**，但 **LDAP 不支援 SAML 2.0**（無法直接跟 AWS IAM 整合）、員工需要存取 AWS 資源、不想在 AWS 重建一套身份系統
     2. **解決方案**：寫一個中介程式（**Identity Broker**）當翻譯機
     3. **考試判斷標準**：遇到「既有身份系統不支援 SAML，但要整合 AWS」→ 想到 **Custom Identity Broker + STS**
218. ★★ **Custom Identity Broker 運作流程與技術架構**
     1. **標準流程**：員工 → LDAP 驗證 → Identity Broker → STS API → 取得臨時憑證 → 存取 AWS
     2. **詳細步驟**：員工透過公司內網登入（LDAP 驗證）、Identity Broker 確認員工身份後呼叫 STS 的 `AssumeRole` 或 `GetFederationToken`、STS 回傳臨時憑證（Access Key + Secret Key + Session Token）、員工拿這組臨時憑證存取 AWS 資源
     3. **核心服務**：**STS (Security Token Service)** 專門發放有時效性的臨時憑證（15 分鐘到 12 小時）
219. ★ **Custom Identity Broker 錯誤選項分析 - IAM Policy 引用 LDAP**
     1. **錯誤原因**：IAM Policy 只能定義「誰可以做什麼」，**無法解決「如何驗證 LDAP 使用者身份」的問題**
     2. **技術限制**：需要驗證機制（SAML/STS/Custom Broker）才能把 LDAP 身份轉成 AWS 認得的憑證
     3. **核心問題**：缺乏身份驗證橋接機制
220. ★ **Custom Identity Broker 錯誤選項分析 - IAM Identity Center 整合 LDAP**
     1. **錯誤原因**：IAM Identity Center 主要支援 **SAML 2.0**，題目明確說 LDAP **不相容 SAML**
     2. **技術衝突**：LDAP 不支援 SAML 協定，無法透過 IAM Identity Center 整合
     3. **考試陷阱**：混淆 Identity Center 的支援協定範圍
221. ★ **Custom Identity Broker 錯誤選項分析 - IAM Roles 手動輪替憑證**
     1. **運維成本問題**：手動管理每個員工的 IAM 憑證，運維成本太高
     2. **同步問題**：LDAP 更新 → 手動更新 AWS 憑證，容易出錯且不即時
     3. **根本問題**：根本沒解決「驗證 LDAP 身份」的核心問題
222. ★★ **SAML 支援與否的架構選擇差異**
     1. **支援 SAML 2.0 場景**：如果支援 SAML 2.0，就用 **IAM Identity Center**（前身是 AWS SSO）直接整合，不需要寫 Custom Identity Broker
     2. **不支援 SAML 場景**：必須使用 **Custom Identity Broker + STS** 的自行開發方案
     3. **考試判斷核心**：有 SAML → IAM Identity Center；無 SAML → Custom Identity Broker + STS
223. ★ **支援 SAML 2.0 的標準架構流程**
     1. **架構流程**：員工 → LDAP (SAML IdP) → IAM Identity Center → AWS 資源
     2. **技術特性**：透過 **SAML 聯合身份驗證 (Federation)**、IAM Identity Center 自動處理 **SAML 斷言 (Assertion)**
     3. **實作優勢**：**零程式碼，純設定**即可完成整合
224. ★★ **Security Group Tier-to-Tier 通訊最佳實務**
     1. **核心原則**：Database tier inbound 必須**限制在 Application tier 的 Security Group**
     2. **安全問題**：開放 `0.0.0.0/0` 讓任何人都能打到資料庫埠（如 1433）完全違反 least privilege
     3. **AWS 最佳實務**：允許來自「某個 Security Group」的流量，而不是特定 IP 或任何來源
225. ★★ **Security Group 引用機制核心優勢**
     1. **動態 IP 適應**：Auto Scaling Group 的 EC2 會動態換 IP，無法寫死特定 IP
     2. **SG 引用最佳實作**：`DB-SG inbound: MS SQL 1433 → source = App-SG`
     3. **精準安全控制**：完全限制只有 App tier 可以連 DB，實現 **tier-to-tier communication**
226. ★ **Security Group 錯誤來源類型分析**
     1. **不正確選項**：`0.0.0.0/0`（任何來源）、固定 IP、NACL ID、EC2 instance ID
     2. **正確作法**：使用 **Security Group ID** 作為 source
     3. **考試標準答案**："change the Source to the security group ID attached to the application tier"
227. ★★ **Security Group vs NACL 基礎知識定位**
     1. **考試陷阱**：題目會提供大量 SG/NACL 基礎概念作為背景知識
     2. **實際考點**：真正考的是 **Database SG inbound 限制到 App tier SG** 這個最佳實務
     3. **核心記憶**：Database SG inbound = Application SG，永遠不要開 IP 尤其不要 `0.0.0.0/0`
228. ★★ **Security Group 流量方向快速判斷法**
     1. **核心問題**：「**誰要連誰？**」（最重要的判斷依據）
     2. **流量分析範例**：App tier（EC2 ASG）→ DB（MS SQL 1433）
     3. **規則設定原則**：流入誰，就改誰的 inbound
229. 😇 **Security Group 規則配置實務要點**
     1. **目標識別**：DB 接收 1433 埠流量 → 改 **DB Security Group inbound**
     2. **來源設定**：Source 填入 **App tier 的 Security Group ID**
     3. **記憶技巧**：接收方設定 inbound，來源方提供 Security Group ID
230. ★ **Security Group 錯誤選項分析：AnyCast IP 陷阱**
     1. **錯誤選項**："change the Source to the static AnyCast IP address attached to the application tier"
     2. **錯誤原因**：AnyCast IP 主要用於 **AWS Global Accelerator**，不適用於 Security Group 配置⭐️
     3. **考試陷阱**：混淆不同 AWS 服務的 IP 類型用途
231. ★★ **VPC DNS 設定核心概念與必要條件**
     1. **EC2 獲得 public DNS hostname 的雙重條件**
        - VPC 層級：開啟 DNS resolution 和 DNS hostnames
        - Instance 層級：有 public IPv4 address
     2. **Default VPC vs 自建 VPC 差異**
        - Default VPC：預設開啟兩項 DNS 設定
        - 自建 VPC：預設關閉（需手動啟用）
232. ★ **VPC DNS 設定功能解析與啟用方法**
     1. **DNS resolution 功能**：讓 EC2 可以透過 AWS DNS server 解析域名
     2. **DNS hostnames 功能**：讓 EC2 獲得 public DNS hostname
     3. **啟用方法**：到 VPC 設定中手動啟用這兩個選項
233. 😇 **VPC DNS 設定考試判斷技巧與常見陷阱**
     1. **考試關鍵字組合**：「新建的 VPC」+ 「沒有 DNS hostname」→ 直接想到 DNS 設定問題
     2. **實務陷阱**：很多人第一次自建 VPC 會踩的坑
     3. **最佳實務**：生產環境建 VPC 時，在一開始就啟用這兩個設定
234. ★★ **Amazon EFS 作業系統支援限制**
     1. **核心限制**：EFS 只能處理 **Linux 工作負載**，不支援 Windows
     2. **考試判斷**：看到 **Windows EC2 instance** → 直接排除 EFS 選項
     3. **Windows 儲存方案**：應選擇 **EBS volumes** 或 **FSx for Windows File Server**
235. ★★ **Windows EC2 共享檔案系統最佳選擇核心考點**
     1. **決定性因素**：題目出現 **Active Directory 整合需求** → 只能選 **FSx for Windows File Server**
     2. **技術要求組合**：Windows EC2 + SMB 協定 + 高效能 + AD 整合 → **FSx Windows 是標準解**
     3. **考試記憶**：看到 `.NET`、`Windows EC2`、`Active Directory` → 直接選 **FSx for Windows File Server**
236. ★★ **FSx for Windows File Server vs File Gateway 差異比較**
     1. **AD 整合支援**：FSx Windows（**原生支援 AD**）vs File Gateway（**不支援 AD 整合**）
     2. **效能規格**：FSx Windows（tens of GB/s + millions IOPS）vs File Gateway（混合雲導向，效能有限）
     3. **適用場景**：FSx Windows（純 EC2 高效能）vs File Gateway（on-premises ↔ S3 混合雲）
237. ★ **Windows 共享檔案系統服務選擇判斷表**
     1. **Windows EC2 + AD 整合**：**FSx for Windows File Server**（唯一選項）
     2. **Windows EC2 + 基本共享**：FSx Windows 或 File Gateway
     3. **混合雲檔案同步**：File Gateway（主要用途）
238. 😇 **FSx for Windows File Server 技術特性與優勢**
     1. **協定支援**：原生 **SMB 協定**，Windows 檔案共享標準
     2. **管理模式**：**完全託管服務**，符合雲端最佳實踐
     3. **效能保證**：可達 **tens of GB/s throughput** + **millions IOPS**，滿足高效能需求
239. ★★ **VPC Peering 跨 Region 連線核心機制**
     1. **關鍵特性**：**VPC Peering 在跨 Region 時自動加密**，流量走 AWS 內網，不經公網
     2. **標準實作**
        - 建立 VPC Peering Connection (Ohio ↔ N.Virginia)
        - 修改 Route Table 加路由規則指向對方 VPC CIDR
240. ★★ **VPC Peering 路由設定必要性與技術原理**
     1. **常見錯誤**：以為建立 Peering 就結束了，實際上只是「開通道路」，EC2 不知道怎麼走
     2. **路由配置要求**
        - 在各自 subnet 的 route table 加上目的地對方 VPC CIDR
        - Target 設為 Peering Connection ID (pcx-xxxxx)
     3. **核心記憶**：**Peering = 開道路，Route Table = 裝導航**，兩個都要設定才能通
241. ★ **VPC Peering 考試錯誤選項分析**
     - **用 VPN**：多此一舉，Peering 更簡單
     - **用 Transit Gateway**：功能過剩，成本較高
     - **只改 Security Group**：沒路由還是不通
242. ★★ **VPC Endpoint 使用限制與適用範圍**
     1. **核心限制**：VPC Endpoint **只能用於同 Region 內連接 AWS 服務**（如 S3、DynamoDB），不支援跨 Region 或 VPC 間通訊
     2. **真正用途**
        - 讓 EC2 私密連接 AWS 服務（不經 Internet Gateway）
        - 分兩類：Gateway Endpoint（S3/DynamoDB）和 Interface Endpoint（其他服務）
     3. **技術原理**：Endpoint 本質是在你的 VPC 內建立一個「服務入口」，只作用於該 VPC 所在 Region 的 AWS 服務
243. ★ **VPC 連線方案考試判斷技巧**
     1. **考試陷阱**：題目說「private connection」時，容易誤選 VPC Endpoint
     2. **正確判斷標準**
        - EC2 ↔ EC2 → 用 **Peering**
        - EC2 ↔ AWS 服務 → 用 **Endpoint**
244. ★★ **VPC Endpoint 成本差異與選擇策略**
     1. **Gateway VPC Endpoint**：**免費**，適用於 S3 和 DynamoDB
     2. **Interface VPC Endpoint**：**付費**（hourly usage + data processing charges），適用於其他 AWS 服務
     3. **考試判斷**：題目要求「most cost-effective solution」且涉及 S3 → 選 **Gateway VPC Endpoint**
245. 😇 **Route 53 Weighted Routing 權重設定機制**
     1. **權重指定方式**：**完全人為手動指定**，在建立 record 時手動設定 weight 數值（**0-255**）
     2. **設定位置**：Route 53 Console → Record → Routing policy 選 Weighted → 輸入 Weight 值
     3. **動態調整**：可隨時修改 weight 改變流量分配比例，**無需停機**
246. 😇 Route 53 Weighted Routing 等比例分配設定
     1. **1:1 分配方法**：兩台都設 **weight=1** 即可實現等比例
     2. **數值彈性**：50/50、100/100 都可以，**只要兩個數字相同就是 1:1**

247. ★★ **IAM Policy + Resource Tags 精細權限控管機制**
     1. **核心做法**：打 Tag 區分環境（UAT 機器 `Environment=UAT`、Production 機器 `Environment=Production`）
     2. **IAM Policy 條件設定**：使用 `StringEquals` 條件限制只能操作特定 tag 的資源
     3. **權限隔離優勢**：**Tag + IAM Condition** 最靈活，一個 Account 就能精細控管
248. ★★ **AWS RAM 誤區澄清與正確用途定位**
     1. **AWS RAM 真正用途**：**跨 Account 或 Organization 內共享資源**（如 Transit Gateway、Subnets、License Manager）
     2. **不適用原因**：RAM 是跨帳號共享工具，**不是權限控管工具**，無法直接限制 EC2 操作權限
     3. **考試陷阱識別**：看到「access control」就選 RAM → **錯誤**，正確解法是 **Tags + IAM Policy Condition**
249. ★★ **Private Subnet EC2 透過 NAT Gateway 存取 S3 的流量路徑**
    1. **標準流程**：EC2 (private subnet) → NAT Gateway (public subnet) → Internet Gateway → **公網** → S3 (公開端點 s3.amazonaws.com)
    2. **重要觀念**：即使 EC2 和 S3 在**同一個 region**，透過 NAT Gateway 存取 S3 時流量會**離開你的 VPC** 經過公網
    3. **費用影響**：**依然收費**，因為經過 NAT Gateway 的 data processing 費用
250. ★★ **VPC Endpoint 最佳化 S3 存取的核心優勢**
    1. **流量路徑**：EC2 (private subnet) → S3 VPC Endpoint → S3 (直接透過 AWS 內網)
    2. **核心優勢**：流量**完全不離開 AWS 網路**、不經過公網、**免費且更安全**
    3. **考試重點**：這就是題目要求**成本最佳化**的核心原因
251. ★★ **ALB Access Logs vs CloudWatch Application Insights 流量監控組合**
     1. **ALB Access Logs**：捕捉 **HTTP 請求詳細資訊**（IP、latency、request path）、自動寫入 S3、零維護負擔
     2. **CloudWatch Application Insights**：分析流量模式、**troubleshooting ECS 應用**、自動配置監控、自動分析異常
     3. **核心優勢**：**LEAST amount of overhead** 的監控解決方案組合
252. ★★ **CloudTrail vs ALB Access Logs 監控對象差異**⭐️
     1. **CloudTrail 監控對象**：**AWS API 操作**（誰改了 ALB 設定），記錄資源變更（CreateLoadBalancer、ModifyListener）
     2. **ALB Access Logs 監控對象**：**實際 HTTP 流量**（哪些請求打到 ALB），記錄 Client IP、latency、request path、response
     3. **用途區分**：CloudTrail（稽核、合規、安全分析）vs ALB Access Logs（流量分析、應用 troubleshooting）
253. ★ **CloudTrail 不適合 HTTP 流量分析的考試陷阱**
     1. **題目要求**：「HTTP 請求詳細資訊、Client IP、網路延遲」
     2. **錯誤選項**：CloudTrail → 只記錄「有人修改 ALB 設定」這類**管理操作**⭐️
     3. **正確選項**：ALB Access Logs → 記錄每個 **HTTP request 的完整資訊**⭐️
254. 😇 **CloudTrail vs Access Logs 記憶技巧與考試判斷**
     1. **記憶口訣**：CloudTrail = 管理層面的「誰做了什麼」、Access Logs = 應用層面的「用戶請求了什麼」
     2. **考試判斷**：CloudTrail Lake 也只能查 **API events**，跟應用流量無關
     3. **核心區分**：管理操作監控 vs 應用流量監控
255. ★ **AWS 三大監控服務快速區分法**
     1. **CloudTrail**：「誰」對 AWS 資源做了「什麼操作」（**管理員視角**）
     2. **CloudWatch**：監控資源運作狀態、metrics、logs（**營運視角**）
     3. **Access Logs**：記錄終端用戶的實際請求（**用戶視角**）
256. 😇 **監控服務記憶口訣與視角區分**
     1. **CloudTrail**：**內部人員的管理操作**（AWS 資源變更稽核）
     2. **Access Logs**：**外部用戶的應用請求**（實際流量分析）
     3. **理解技巧**：從不同角色視角記憶各服務用途

257. ★★ S3 Transfer Acceleration + Multipart Upload 組合應用核心概念——僅用於大檔案加速**上傳** S3
     1. **Transfer Acceleration 特性**
        - 利用 CloudFront edge locations 加速遠距離傳輸
        - 適合跨國跨大陸資料傳輸
        - 透過 **AWS 骨幹網路傳輸比公網快 50-500%**
     2. **Multipart Upload 特性**
        - 將大檔案切分成多個部分平行上傳
        - **單一檔案超過 100MB 建議使用，超過 5GB 必須使用**
        - 失敗可重傳單一部分不用整個檔案重來
258. ★★ **Egress-only IGW 路由設定必要性與配置要求**
     1. **核心要求**：雖然 **Egress-only IGW 掛在 VPC level**，但必須在 **Private Subnet 的 route table** 手動加路由規則
     2. **路由配置**：目的地 `::/0`（所有 IPv6）→ Target: `eigw-xxxxx`（Egress-only IGW）
     3. **架構理解**：掛在 VPC ≠ 自動生效，**route table 一定要設定**
        - VPC 層級掛載只是建立 Gateway
        - Route Table 設定才是實際生效的關鍵
        - 兩步驟缺一不可
259. 😇 **IGW vs Egress-only IGW 路由設定類比**
     1. **Internet Gateway**：掛 VPC level，但 **Public Subnet** 要加路由 `0.0.0.0/0 → igw-xxx`
     2. **Egress-only IGW**：掛 VPC level，但 **Private Subnet** 要加路由 `::/0 → eigw-xxx`
     3. **記憶技巧**：兩者都需要在對應 subnet 的 route table 手動設定路由
        - Gateway 掛載 = 提供能力
        - Route Table = 實際使用
        - 類比：裝了門但不裝把手，還是推不開門
260. ★★ **NAT Gateway Multi-AZ 部署的路由設定陷阱**
     1. **常見錯誤**：誤將路由設定在 **Public Subnet**（沒意義，Public Subnet 本來就能直接上網）⭐️
     2. **正確原則**：**NAT Gateway 放置位置**在 Public Subnet，**Route Table 設定位置**在 Private Subnet⭐️
        - NAT Gateway 本身需要公網 IP（Elastic IP）才能對外連線
        - Private Subnet 透過路由指向 NAT Gateway 來實現出網
        - 架構邏輯：Private → NAT GW → IGW → Internet
     3. **核心記憶**：Private 要上網 → 查自己 subnet 的 route table → 找到同 AZ 的 NAT Gateway
        - 位置：NAT GW 在 Public，路由在 Private
        - 邏輯：誰要出網，誰設定路由
        - 錯誤：在 Public 設路由完全沒用
261. ★★ **Multi-AZ NAT Gateway 標準架構與高可用設計**
     1. **AZ-A 配置**：Public Subnet A → NAT Gateway A，Private Subnet A route: `0.0.0.0/0 → NAT Gateway A`
     2. **AZ-B 配置**：Public Subnet B → NAT Gateway B，Private Subnet B route: `0.0.0.0/0 → NAT Gateway B`
     3. **高可用優勢**：每個 AZ 獨立運作，一個掛了不影響其他 AZ
        - 每個 AZ 自成一套完整架構
        - 故障隔離：AZ-A 掛了不影響 AZ-B
        - 成本考量：需要建立多個 NAT Gateway
262. ★★ RDS Event Subscription 主要監控 db 實例層級事件，無法監控資料變化
     1. **RDS Event Subscription 限制**：只能監控 **DB 實例層級的事件**（實例啟動/停止/重啟、備份完成、故障轉移、參數群組變更）⭐️
     2. **核心問題**：**完全無法監控資料變化**（row-level changes），無法得知「哪一筆車輛資料被刪除」
     3. **考試關鍵字識別**：看到「資料庫資料變化觸發後續處理」→ 排除 RDS event subscription
263. ★★ **Aurora Native Function 直接觸發 Lambda 核心優勢**
     1. **即時資料捕捉**：刪除發生時立即觸發（row-level trigger）
     2. 可監控特定操作：`INSERT`、`UPDATE`、`DELETE`⭐️
     3. **完整資料傳遞**：可把被刪除的車輛完整資訊傳給 Lambda
     4. **精確控制**：可只在特定操作（DELETE）時觸發
264. ★★ **Aurora Native Function 服務定位與技術實作**
     1. **服務定位**：**Aurora MySQL/PostgreSQL 的原生功能**，不是獨立服務
     2. **呼叫方式**：提供 Native Function 和 Stored Procedure 兩種方式
     3. **技術整合**：直接在 SQL 語法中呼叫 Lambda 函式
     4. **考試關鍵字**：「database 資料變化要觸發處理」→ 首選 **Aurora + Lambda integration**
     5. **服務支援範圍**：只有 **Aurora MySQL/PostgreSQL** 支援，一般 RDS 沒有此功能
     6. **架構優勢**：資料變化即時觸發，無需額外的 polling 機制
265. ★★ **SAML Federation 核心架構與流程**
     1. **問題解決**：on-premises AD 帳號直接存取 AWS 資源，無需在 AWS 重建帳號
     2. **技術組合**：AD FS + SAML 2.0 + AWS STS，透過信任關係建立身份聯邦
     3. **運作流程**：使用者 → AD FS 登入 → 取得 SAML assertion → 換取 AWS temporary credentials → 存取 AWS 資源
266. ★★ **DynamoDB Streams 資料變化監控核心機制**
     1. **監控範圍**：記錄 **INSERT、MODIFY、REMOVE** 三種資料變更事件
     2. **資料保留**：Stream 資料保留 **24 小時**
     3. **觸發用途**：Lambda 後續處理、稽核紀錄、跨 region 複製
267. ★ **DynamoDB Streams View Type 選擇**
     1. **KEYS_ONLY**：只記錄 primary key（最小資料量）
     2. **NEW_AND_OLD_IMAGES**：記錄變更前後完整資料（稽核常用）
     3. **考試重點**：依據後續處理需求選擇適當的 view type
268. ★★ **Lambda vs Glue 大檔案處理選擇標準**
     1. **Lambda 限制**：執行時間最多 15 分鐘、記憶體最多 10GB，**不適合 ETL 工作負載**⭐️
     2. **AWS Glue 優勢**：專為 ETL 設計、TB 級資料處理、自動擴展、無時間限制
     3. **考試判斷**：看到「大量資料轉換」或「ETL」→ **AWS Glue**
269. 😇 **Lambda vs Glue 服務定位差異**
     1. **Lambda**：輕量快速事件處理、微服務架構、API 後端
     2. **AWS Glue**：大規模資料轉換、ETL pipeline、資料湖處理
     3. **核心記憶**：Lambda = 事件處理，Glue = 資料轉換
270. ★★ **ElastiCache Redis AUTH 認證機制核心要求**
     1. **必要參數組合**：`--auth-token` + `--transit-encryption-enabled` **兩個參數缺一不可**
     2. **安全設計邏輯**：AWS 強制 AUTH token 必須搭配 TLS 加密，避免密碼明文傳輸
     3. **考試關鍵字識別**：看到「require password before Redis commands」→ **Redis AUTH**
271. ★ **Redis AUTH vs IAM Authentication 差異對比**
     1. **Redis AUTH**：**long-lived credentials**、Redis 內建機制、適用於 Redis protocol
     2. **IAM Authentication**：短期憑證（12 小時過期）、適用於 AWS API、**不適用於 Redis 命令執行**
     3. **考試判斷**：Redis 命令認證 → AUTH token，AWS API 操作 → IAM
272. 😇 **ElastiCache Redis AUTH 實務應用**
     1. **應用連線**：所有 client 連線都須帶 auth-token
     2. **命令執行**：包括 MULTI/EXEC 等 Redis 命令都需先通過認證
     3. **核心記憶**：類似 PostgreSQL 設密碼，只是 Redis 的實作方式
273. ★★ **EKS etcd 資料庫層級加密核心概念**
     1. **核心問題**：Kubernetes Secrets 在 etcd 預設是**明文儲存**，有安全風險
     2. **解決方案**：**EKS + KMS encryption** 在資料寫入 etcd **之前**就加密
     3. **考試關鍵字**：看到「encrypt data within etcd」→ **KMS encryption for EKS**
274. ★ **EKS KMS vs Secrets Manager 用途差異**
     1. **KMS encryption**：加密 **etcd 資料庫本身**，防止 etcd backup 洩露
     2. **Secrets Manager**：**外部密碼管理服務**，無法加密 etcd 內部資料
     3. **核心區分**：etcd 加密 vs 密碼管理，兩者可搭配使用但目的不同
275. 😇 **EKS etcd 加密架構理解**
     1. **資料流程**：應用程式 → Kubernetes Secret → etcd 資料庫（需加密此階段）
     2. **實務意義**：即使攻擊者取得 etcd backup 也無法讀取 secrets
     3. **記憶技巧**：etcd = K8s 後端資料庫，KMS = 資料庫檔案加密
