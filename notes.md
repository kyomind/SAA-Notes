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
19. ★★ Step Scaling vs Target Tracking Scaling — 關鍵判斷
    1. **Step Scaling**：**你自己指定** scaling metrics 和 threshold values
    2. **Target Tracking**：**AWS 幫你調**，只需設定目標值（如 CPU 維持 50%）
    3. **考試技巧**：看到「specifying the scaling metrics and threshold values」→ **Step Scaling**
20. ★★ EKS 自動擴展 — 兩層擴展架構
    1. **應用層（Pods）**：Horizontal Pod Autoscaler (HPA)，依 CPU/Memory 使用率觸發
    2. **基礎設施層（Nodes）**：Karpenter 或 Cluster Autoscaler，當 Pods 無法調度時觸發
    3. **協同運作**：流量增加 → HPA 增加 Pods → Node 資源不足 → Karpenter 增加 Nodes⭐️
21. ★★ EKS 自動擴展標準答案組合
    1. **Metrics Server + HPA**：Metrics Server 提供指標，HPA 根據負載調整 Pod 數量（應用層擴展）
    2. **Karpenter**：自動增減 EC2 nodes，根據 pod 需求選擇最適合的 instance type
    3. **考試記憶點**：EKS + 自動擴展 + least overhead → **HPA + Karpenter**
22. ★ Karpenter vs Cluster Autoscaler — 運維負擔比較
    1. **Karpenter**：宣告式設定、動態選擇 instance type、直接呼叫 EC2 API（速度快）、**運維負擔低**
    2. **Cluster Autoscaler**：需預先定義 ASG、固定 instance type、透過 ASG 間接整合、運維負擔較高
    3. **選擇邏輯**：問 least operational overhead → **Karpenter**；問 native K8s solution → Cluster Autoscaler
23. ★★ Aurora Failover 機制 — 有無 Replica 決定行為
    1. **有 Aurora Replica**：CNAME 翻轉指向健康 Replica，Replica 升級為 Primary（**約 30 秒內**）
    2. **沒有 Replica（單一 instance）**：在同一 AZ 嘗試**重建**新的 DB instance（較慢，best-effort）
    3. **考試陷阱**：看到「single Aurora instance」→ 沒有 Replica 可 promote，只能重建
24. ★ S3 Glacier 成本結構 — 三大費用組成
    1. **Storage**：存放費用（極低，Glacier 的核心賣點）
    2. **Retrieval**：取回費用（按 GB 計費 + 請求次數），**越快越貴**
    3. **Request**：API 請求費用
    4. **重點**：從 Glacier 取回資料**一定要付 retrieval 費用**，這是便宜儲存的代價
25. ★★ S3 Glacier 考試題型 — 關鍵字拆解
    1. **典型題目**：「合規資料長期保存，**偶爾**需在 **12 小時內**取回，**最低成本**」
    2. **答案**：Glacier Deep Archive + Standard retrieval
    3. **關鍵字解析**：「偶爾取回」→ Glacier 系列、「12 小時內」→ Deep Archive Standard 符合、「最低成本」→ Deep Archive 儲存費最低
    4. **考試陷阱**：Deep Archive **沒有 Expedited** 選項
26. ★ Glacier Instant Retrieval 定位與費用邏輯
    1. **定位**：「混血」storage class，**毫秒級取回**但仍收 retrieval 費用
    2. **儲存費用**：約 S3 Standard 的 68%（便宜）
    3. **Retrieval 費用**：每 GB 都收費，**比 S3 Standard-IA 還貴**
    4. **白話說**：存得便宜，但每次拿都要付「取貨費」
27. ★★ Glacier Instant Retrieval 考試判斷 — 關鍵字組合
    1. **選擇時機**：「**每季存取一次**」或「**一年幾次**」+「**需要立即取回**」或「**毫秒級延遲**」+「**長期保存**」
    2. **對比選擇**：可以等幾小時 → **Glacier Flexible Retrieval**（更便宜）
28. ★★ Placement Group：Spread vs Partition 核心差異
    1. **Spread**：隔離單位為**單一硬體（機器）**，每台硬體嚴格放 **1 個 instance**
    2. **Partition**：隔離單位為**機架（Rack）**，同一 partition 內可能共用硬體
    3. **關鍵限制**：Spread 每個 AZ **最多 7 個 instances**；Partition 每個 AZ **最多 7 個 partitions**
29. ★ Spread Placement Group — 極端隔離但規模受限
    1. **用途**：少量但極關鍵的 instances（如 7 台 database replica）
    2. **保證**：每個 instance 在**獨立實體硬體**上，任一硬體故障只影響 1 個 instance
    3. **代價**：每個 AZ 最多 7 個 instances（硬體完全隔離的數量有限）
30. ★ Partition Placement Group — 機架級隔離支援大規模
    1. **用途**：大規模分散式系統（如 100 節點 Cassandra cluster）
    2. **運作方式**：節點分散到不同 partition，每個 partition 對應不同機架
    3. **故障影響**：整個機架斷電只損失約 1/7 節點，分散式系統的 replication 可處理
31. ★★ Placement Group 考試選擇技巧
    1. **選 Spread**：少量關鍵 instances、需要**最高隔離等級**
    2. **選 Partition**：看到 **HDFS、Cassandra、Kafka** 等分散式系統關鍵字
    3. **選擇邏輯**：這些系統本身有 partition/replication 概念，天生適合 Partition Placement Group
32. ★★ ASG Cooldown vs Warm-up — 核心差異
    1. **Cooldown**：scaling 動作後，**整個 ASG 暫停決策**（預設 300 秒），等指標穩定
    2. **Warm-up**：新 instance 在熱身期間內，**其指標不納入 ASG 平均計算**
    3. **作用範圍**：Cooldown 作用於 **ASG 層級**；Warm-up 作用於**單一 instance 層級**
33. ★ ASG Cooldown — 防止 Scaling 震盪
    1. **問題場景**：CPU 飆高觸發 scale-out，但 CloudWatch 指標有延遲，尚未反映新容量，又觸發加機器
    2. **解法**：scaling 動作後暫停判斷一段時間，等指標穩定再做決策
34. ★ ASG Warm-up — 新 Instance 的熱身期
    1. **問題場景**：新 instance 啟動時跑 bootstrap script、載入 cache，CPU 飆高但非真正負載
    2. **解法**：warm-up 期間內該 instance 的指標不計入 ASG 平均，避免誤判需要更多資源
35. ★★ ASG Cooldown vs Warm-up 考試判斷
    1. **調 Cooldown**：題目說「scaling 太頻繁」「震盪」「oscillation」
    2. **調 Warm-up**：題目說「新 instance 啟動時指標異常」「bootstrap 期間」
36. ★★ Aurora Reader Endpoint 行為 — 有無 Replica 的差異
    1. **有 Replicas**：自動負載均衡到所有 Replicas
    2. **沒有 Replicas**：**退回連接到 Primary instance**（技術上不會報錯，但無讀寫分離效果）
37. 😇 Aurora Reader Endpoint 最佳實踐
    1. **架構擴展性**：一開始就把讀取邏輯指向 Reader Endpoint，日後加 Replicas 時**零程式碼修改**
    2. **考試重點**：問「如何實現 Aurora 讀取負載均衡」→ 答案永遠是 **Reader Endpoint**
38. ★★ IAM 外部身分與 Assume Role — 核心概念⭐️
    1. **外部身分定義**：不在你 AWS 帳戶內建立的 IAM User（跨帳戶用戶、Federated Users、Web Identity、SAML 2.0）
    2. **為何用 Role**：無法在你帳戶幫外部人建立 IAM User，改用 **Assume Role + STS** 取得臨時憑證
    3. **運作流程**：外部身分 → Assume Role → 獲得臨時憑證 → 存取 AWS 資源
39. ★ IAM 內部 vs 外部身分 — 權限指派差異
    1. **內部身分**（你帳戶的 IAM User）：直接指派 Policy 或加入 Group
    2. **外部身分**（不在你帳戶內）：透過 Assume Role 取得臨時權限
    3. **考試關鍵字**：看到「跨帳戶」「Federation」「SSO」「第三方登入」→ 想到 **IAM Role + STS**
40. ★ S3 Object Lambda — 即時攔截處理 S3 GET 請求
    1. **本質**：在 GET 請求和回傳之間插入 Lambda 做即時處理（**攔截器模式**）
    2. **運作流程**：Client → S3 Object Lambda Access Point → Lambda 處理 → 轉換後的檔案
    3. **典型場景**：同一份資料不同角色看不同內容（遮蔽 PII）、動態調整圖片大小、動態加浮水印
41. ★★ S3 Object Lambda 考試關鍵字
    - 「**不想存多份副本**」+「**動態轉換**」→ S3 Object Lambda
    - 「**不同使用者看到不同內容**」→ S3 Object Lambda
    - 「**GET 時即時處理**」→ S3 Object Lambda
42. ★★ Glacier **Flexible Retrieval** 專屬特性 — Expedited & Provisioned Capacity
    1. **Expedited retrieval（1-5 分鐘）**：Glacier Flexible Retrieval **獨有**的快速取回選項
    2. **Provisioned Capacity**：專門搭配 Expedited 使用，保證取回容量
    3. **區分其他 Glacier**：Instant Retrieval 是毫秒級（不需 Expedited）、Deep Archive 最快 12 小時（無 Expedited）
43. ★★ NACL vs Security Group — 作用層級區分（考試陷阱）
    1. **NACL**：作用於 **Subnet 層級**，控制進出該 subnet 的所有流量
    2. **Security Group**：作用於 **Instance 層級**，控制進出該 instance 的流量
    3. **Route Table + IGW**：作用於 VPC 層級，控制**路由方向**，非直接過濾流量
44. ★ NACL 關聯規則
    1. **強制關聯**：每個 subnet **必須**關聯一個 NACL（預設使用 default NACL）
    2. **多對多關係**：不同 subnet 可用不同 NACL；同一 NACL 可關聯多個 subnet
45. ★★ DynamoDB Global Table 核心定位 — Active-Active 全球部署
    1. **本質**：**active-active 多區域架構**，DR 只是附帶效果
    2. **主要用途**：降低全球用戶延遲（歐洲用戶打歐洲 table）、流量分散（各區域獨立承載）
    3. **流量承載力**：線性擴展，開 3 個區域 ≈ 3 倍寫入吞吐上限（假設流量分散）
46. ★★ DynamoDB Global Table 考試關鍵字與陷阱
    1. **典型題目**：「服務全球用戶 + 低延遲存取 + 災難復原能力」→ **DynamoDB Global Table**
    2. **排除情境**：題目強調 **strong consistency**（強一致性）→ Global Table 不適合（它是最終一致）
    3. **對比選擇**：需要 **relational data + 跨區域** → **Aurora Global Database**
47. 😇 ElastiCache 核心概念與引擎選擇
    1. **核心用途**：資料庫查詢結果快取、Session 存儲
    2. **Redis**：需要**持久化**、複雜資料結構、Pub/Sub → 選它
    3. **Memcached**：純快取、**多執行緒**、簡單 key-value → 選它
48. 😇 ElastiCache 考試判斷
    1. **典型關鍵字**：「減少資料庫負載」「加速讀取」「Session 共享」→ **ElastiCache**
    2. **考試深度**：不考配置細節、不考 Redis Cluster 分片機制，主要考「什麼時候該用」
49. ★ Glacier Vault Lock 考試關鍵字觸發
    - **「Glacier Vault」** 直接提到
    - **「archive」**（歸檔）+ **合規保留 N 年**
    - **「WORM compliance」** + **Glacier**
    - **「legal hold」** + **長期歸檔資料**
50. 😇 Glacier Vault Lock 典型題目句型
    - "The company must ensure that archived data in Glacier **cannot be deleted for 7 years** to meet regulatory compliance..."
    - "...need to implement a **write-once-read-many (WORM)** policy on their **Glacier vault**..."
51. ★ Vault Lock vs Object Lock 判斷 — 何時不是 Vault Lock
    1. **選 Object Lock**：資料在 S3（只是 storage class 選 Glacier）、保護特定幾個檔案、題目沒提 Vault
    2. **選 Vault Lock**：題目明確出現「Vault」或強調「**原生 Glacier 服務**」
    3. **記憶點**：Vault 是原生 Glacier 的東西，這題型不高頻但出現就是送分題
52. ★★ Security Group vs NACL — 來源/目標表示法差異
    1. **共同支援**：CIDR block（`10.0.1.0/24`）、單一 IP（`10.0.1.5/32`）
    2. **Security Group 獨有**：可引用**另一個 Security Group ID**（`sg-0abc1234`）、**Prefix List**（`pl-xxx`）
    3. **NACL 限制**：純網路層（Layer 3/4），**只認 CIDR**，不認識 Security Group
53. ★★ Security Group 引用 SG — 動態環境的最佳實踐
    1. **用法**：Inbound Rule 的 Source 填 `sg-frontend-12345`，代表該 SG 內的所有資源都放行
    2. **優勢**：Auto Scaling 環境中 EC2 IP 動態變化，**只要屬於該 SG 就能通過**，無需維護 IP 清單
54. ★ Security Group vs NACL — 考試判斷點
    1. **動態擴展的 EC2 互相通訊**：答案用 **Security Group 引用 Security Group**
    2. **阻擋特定 IP 網段**：兩者都行，但 **NACL 可以做 Deny**（SG 只能 Allow）
