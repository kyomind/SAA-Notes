# Speed Notes for SAA

1. ★ **TCP Health Check** 僅支援 Network Load Balancer 和 Classic Load Balancer，ALB 不支援 TCP health check
   1. ALB 只支援 HTTP/HTTPS health check
2. ★★ DataSync vs Storage Gateway — 「**遷移**」vs「**整合**」⭐️
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
   2. **Data Events**：資料平面操作（GetObject, PutObject 等），**預設不啟用，需手動開啟**⭐️
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
   1. **Lambda 硬限制**：最大執行 15 分鐘、記憶體 10 GB、最多 6 vCPU⭐️
   2. **結論**：Lambda 為「短暫、輕量、事件驅動」設計，不適合重型運算
8. ★★ AWS Batch 核心概念⭐️
   1. **本質**：批次運算任務的編排服務，自動開 EC2、分配任務、跑完關機
   2. **平行執行**：定義多個 jobs，Batch 自動分散到多台 EC2
   3. **資源彈性**：可指定 EC2 instance type（如 `r5.8xlarge` 有 32 vCPU + 256 GB RAM）
   4. **最少維運**：不用自己管 EC2 lifecycle、job queue、失敗重試
9. ★ AWS Batch 選擇時機 — 關鍵字組合⭐️
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
18. ★★ EBS Encryption 考題陷阱⭐️
    1. **題目問**：如何確保 EBS 資料在傳輸過程中的安全？
    2. **陷阱選項**：設定 Security Group、使用 VPN
    3. **正確答案**：啟用 EBS Encryption（**自動處理 in-transit 加密**）
    4. **記憶點**：EBS Encryption = **at-rest + in-transit 都包了**
19. ★★ Step Scaling vs Target Tracking Scaling — 關鍵判斷⭐️
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
23. ★★ Aurora Failover 機制 — **有無 Replica** 決定行為⭐️
    1. **有 Aurora Replica**：CNAME 翻轉指向健康 Replica，Replica **自動升級**為 Primary（**約 30 秒內**）
    2. **沒有 Replica（單一 instance）**：在同一 AZ 嘗試**重建**新的 DB instance（較慢，best-effort，通常需要幾分鐘甚至 10 分鐘）
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
28. (刪除)
29. ★ Spread Placement Group — 極端隔離但規模受限
    1. **用途**：少量但極關鍵的 instances（如 7 台 database replica）
    2. **保證**：每個 instance 在**獨立實體硬體**上，任一硬體故障只影響 1 個 instance
    3. **代價**：每個 AZ 最多 7 個 instances（硬體完全隔離的數量有限）
30. ★ Partition Placement Group — 機架級隔離支援大規模
    1. **用途**：大規模分散式系統（如 100 節點 Cassandra cluster）
    2. **運作方式**：節點分散到不同 partition，每個 partition 對應不同機架
    3. **故障影響**：整個機架斷電只損失約 1/7 節點，分散式系統的 replication 可處理
31. ★★ Placement Group 考試選擇技巧⭐️
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
55. 😇 RDS 底層架構 — 運算與儲存分離
    1. **本質**：RDS 跑在 **AWS 管理的 EC2 instance** 上，使用者看不到也摸不到
    2. **架構**：RDS Instance（運算層）↔ EBS Volume（儲存層），資料**本來就存在 EBS 上**
56. ★★ RDS Stop vs Terminate — 費用差異
    1. **Running**：Instance 收費 + EBS 收費
    2. **Stopped**：Instance **不收費**，EBS **繼續收費**
    3. **Terminated**：兩者都消失（除非有 snapshot）
    4. **關鍵點**：Stop 只是停止運算，**儲存費用持續計費**
57. ★★ RDS 省錢策略 — Snapshot + Terminate
    1. **操作流程**：建立 Snapshot（備份到 S3）→ Terminate RDS（刪除 instance + EBS）→ 需要時從 Snapshot 還原
    2. **費用邏輯**：只付 Snapshot 儲存費（通常比 EBS 便宜很多）
58. ★★ RDS 成本優化考試記憶點
    - **RDS Stop ≠ 省 storage 費**
    - **長期不用** → Snapshot + Terminate 最省
    - **短期不用（< 7 天）** → Stop 可接受
59. 😇 RDS 費用組成 — 你付的錢包含什麼
    - **EC2 運算費用**（instance 費用）
    - **EBS 儲存費用**（storage 費用）
    - **管理費**（AWS 幫你維運的成本）
60. ★ Virtual Private Gateway (VGW) 核心概念
    1. **定義**：AWS VPC 端用來建立 VPN 連線的閘道器，是 VPC 的 VPN 出口
    2. **架構角色**：On-Premises (Customer Gateway) ──VPN 連線── VGW ──你的 VPC
    3. **對照關係**：CGW (Customer Gateway) 代表公司端的 VPN 設備/路由器；VGW 代表 AWS 端的 VPN 進入點，attach 到 VPC 上
61. 😇 Virtual Private Gateway 的限制
    1. **單一綁定**：一個 VGW 只能 attach 到一個 VPC
    2. **不支援 transitive routing**：A↔VGW↔B 架構中，A 和 B 無法直接通訊
    3. **擴展性受限**：較早期設計，複雜網路拓撲需求應改用 Transit Gateway (TGW)
62. ★★ VGW vs Transit Gateway 考試場景選擇
    1. **選 VGW**：單一 VPC + 單一/少數 Site-to-Site VPN
    2. **選 Transit Gateway**：多 VPC 互連 + 多個 VPN
    3. **選 VPN CloudHub**：多分公司要互連、低成本（基於 VGW）
63. ★★ EIP 與 ENI 的關聯關係 — 技術架構
    1. **底層事實**：EIP 實際關聯到 **ENI (Elastic Network Interface)**，非直接關聯到 EC2 instance
    2. **架構層級**：EIP → ENI → EC2
    3. **Console 簡化**：每個 EC2 預設有 primary ENI，Console 讓你看起來像直接附到 EC2 上
64. ★ EIP + ENI 架構的應用場景
    1. **多 ENI 場景**：EC2 有多張 ENI 時，可對不同 ENI 分別綁不同 EIP
    2. **Failover 設計**：把 ENI（連同 EIP）從故障 EC2 detach，attach 到備援 EC2，**IP 不變**
    3. **高可用架構核心**：EIP 跟著 ENI 走，不是跟著 instance 走
65. 😇 EIP + ENI 考試關鍵字
    1. **題目特徵**：「maintain the same IP address after instance failure」、「move IP to another instance」
    2. **正確理解**：EIP 綁在 ENI 上，因此可以快速切換到另一台 EC2
66. ★★ FSx for Windows vs File Gateway — 核心差異對比
    1. **FSx for Windows**：雲端原生的 Windows 檔案系統，解決 EC2 需要高效能、AD 整合的共享儲存
    2. **File Gateway**：地端到雲端的橋接器，解決 On-premises 想無縫存取 S3
    3. **本質區別**：FSx 是純雲端儲存服務；File Gateway 是混合雲橋接方案
67. ★★ FSx for Windows vs File Gateway — 考試判斷關鍵字
    1. **選 FSx for Windows**：題目只提到 **EC2**、**Windows workloads**、需要 **Active Directory 整合**、強調**高效能** IOPS/throughput、**純雲端架構**
    2. **選 File Gateway**：題目出現 **on-premises**、**hybrid**、**data center**、需要讓地端應用「**看起來像本地檔案系統**」但實際存到 S3、強調**成本效益**（後端是 S3）、**遷移過渡期**場景
68. 😇 FSx for Windows vs File Gateway — 後端工程師視角
    1. **FSx for Windows**：在 AWS 開了一台專門跑 Windows File Server 的高效能 NAS
    2. **File Gateway**：在公司機房放了一個 proxy，讓舊系統以為在讀本地檔案，其實是讀 S3
    3. **競爭對手差異**：FSx 競爭的是 EFS（Linux）、自建 File Server；File Gateway 競爭的是 VPN + 直連 S3、DataSync
69. 😇 ALB Access Logs + CloudWatch Application Insights — 考試標準答案組合
    1. **實務常見度**：ALB Access Logs → S3（標配）；CloudWatch Application Insights（較少見，知名度低）
    2. **考試關鍵字**：題目強調 **"least operational overhead"**、**"minimal configuration"**、**"AWS-native solution"** → Application Insights 的自動配置特性成為正確答案
    3. **定位**：記住這個組合是為了考試，解決低維護的流量監控 + 自動異常分析
70. 😇 CloudWatch Application Insights 在實務上的替代方案
    1. **傳統三件套**：CloudWatch Metrics + Alarms + Dashboards（大多數工程師習慣用）
    2. **第三方工具**：Datadog、New Relic、Grafana（功能更強、生態系更好）
    3. **容器環境**：ECS/EKS 通常直接用 Container Insights（更針對容器場景）
    4. **實務常見**：ALB Logs + Athena 查詢、CloudWatch Metrics + Alarms、observability 平台
71. ★★ NAT Gateway 高可用架構 — 每個 AZ 獨立部署
    1. **核心原則**：NAT Gateway 是 **AZ-bound 資源**，只存在於一個 AZ 內，無法跨 AZ 服務
    2. **唯一正確做法**：每個 AZ 部署獨立的 NAT Gateway（不是選擇，是必須）
    3. **跨 AZ 使用的問題**：違反高可用原則（AZ-A 掛了，AZ-B 的 private subnet 也斷網）、跨 AZ 流量收費
72. ★★ IAM Role 本質 — 給任何需要臨時權限的實體
    1. **適用對象**：AWS 服務（EC2、Lambda 存取 S3）、外部使用者（透過 federation 登入的企業員工）、跨帳號存取（Account A 存取 Account B）、應用程式（使用 AssumeRole 取得臨時憑證）
    2. **核心概念**：IAM Role 是**權限容器**，不限於 AWS 服務專用⭐️
73. ★★ Federation 場景下的 IAM Role 運作機制
    1. **場景**：企業員工在 AD 裡，想登入 AWS Console，但沒有 IAM User
    2. **運作流程**：員工用 AD 帳號驗證身份（AD Connector 負責）→ AD Group 對應到特定 IAM Role（如 `AD-DevOps-Group` → `IAM-DevOps-Role`）→ 員工登入後 **Assume** 這個 Role，獲得臨時權限
    3. **核心解答**：AWS 透過 **Role** 知道員工權限，Role 是外部身份進入 AWS 後的權限載體
74. ★ IAM User vs IAM Role (Federation) — 管理成本對比
    1. **IAM User**：要在 AWS 另外建帳號、密碼要另外管理、人員異動要兩邊改
    2. **IAM Role (Federation)**：用現有 AD 帳號、Single Sign-On、AD 改了就生效
    3. **選擇邏輯**：企業內部員工 + 已有 AD/LDAP → Federation + Role；外部協力廠商、少量用戶 → IAM User
75. ★★ Lambda 同步調用服務 — HTTP 協議的本質需求
    1. **API Gateway**：使用者發 HTTP request，瀏覽器等待 response（request-response 模式）
    2. **ALB**：同上，HTTP 協議本身就是同步通訊
    3. **核心邏輯**：打 API 查訂單狀態，如果回應「之後再告訴你」是不合理的，使用者**需要立即答案**
76. ★ Lambda 非同步調用服務 — Fire and Forget 模式
    1. **S3**：檔案上傳完就結束，後續縮圖、轉檔是**背景工作**
    2. **SNS**：發通知本來就是「fire and forget」，發出去就不管
    3. **EventBridge**：事件驅動架構，觸發就走，不期待回應
    4. **核心邏輯**：呼叫者**不在乎即時結果**，Lambda 可以慢慢處理
77. ★★ ALB 原生支援 Lambda 作為 Target — Serverless HTTP 處理
    1. **推出時間**：AWS 在 2018 年加入此功能
    2. **架構優勢**：傳統 ALB → EC2（要管 server、流量低也付費）vs ALB → Lambda（Serverless、按需付費）
    3. **用途**：將部分 API endpoints 用 Lambda 處理，不用全部跑在 EC2 上
78. ★ ALB + Lambda 運作原理 — HTTP 與 Lambda Event 的自動轉換
    1. **流程**：User Request → ALB 收到 HTTP request → 轉成 JSON event 格式，invoke Lambda（同步）→ Lambda 回傳 response → ALB 轉成 HTTP response
    2. **ALB 的角色**：自動處理 **HTTP ↔ Lambda event 的轉換**
    3. **Lambda handler 格式**：收到的 event 包含 headers、body、path、method 等；回傳格式須含 statusCode 和 body
79. ★★ ALB vs API Gateway — Lambda HTTP 場景選擇
    1. **ALB + Lambda**：便宜（ALB 時數計費）、單純 HTTP 路由、適合高流量 + 簡單路由
    2. **API Gateway + Lambda**：較貴（per request）、有 throttling、API key、caching、適合需要 API 管理功能
80. ★ Storage Gateway VM 部署位置 — On-Premises 虛擬化架構
    1. **部署位置**：VM 部署在**你自己的 on-premises 伺服器上**（不是在 AWS）
    2. **架構層級**：實體伺服器 → hypervisor（VMware ESXi、Hyper-V、KVM）→ VM（安裝 Storage Gateway 軟體）
    3. **簡單類比**：就像在公司機房的實體機上開一台 VM 跑 application，只是這台 VM 跑的是 AWS Storage Gateway
81. 😇 Storage Gateway 部署方式對比 — VM vs Hardware Appliance
    1. **VM 方式**：自己的實體機 + hypervisor + 自己建 VM
    2. **Hardware Appliance**：直接跟 AWS 買一台預裝好的實體機，插電就用
    3. **選擇邏輯**：Hardware Appliance 是給「沒有虛擬化基礎設施」或「不想自己搞」的客戶的懶人方案
82. ★★ Route Table 與 NACL 的層級歸屬 — VPC 資源但關聯 Subnet
    1. **定義層級**：Route Table 和 NACL 都是 **VPC 層級的資源**（建立時屬於某個 VPC）
    2. **作用層級**：它們的**作用對象是 Subnet**（綁定到 Subnet 才生效）
    3. **強制關聯**：每個 Subnet **必須**關聯一個 Route Table（沒指定就用 Main Route Table）和一個 NACL（沒指定就用 Default NACL）
83. ★ Route Table 與 NACL 的精確描述 — 考試陷阱
    1. **不精確說法**：「Subnet 功能：定義路由規則、NACL」
    2. **精確說法**：Subnet **關聯** Route Table 和 NACL，這些規則決定了 Subnet 內資源的網路行為
    3. **考試應用**：題目問「在哪裡設定 Route Table」→ 答案是 **VPC 層級建立，然後關聯到 Subnet**
84. ★★ VGW (Virtual Private Gateway) 核心定位與用途⭐️
    1. **定義**：VPC 的對外連接閘道，**專門用來連接 AWS 以外的網路（地端機房或其他雲端）**
    2. **連接方式**：Site-to-Site VPN（透過 IPsec VPN 連接地端到 AWS VPC）、Direct Connect（透過專線連接，更穩定、更低延遲、更貴）
85. ★ VGW 常見應用場景
    1. **Hybrid Cloud 架構**：公司有自己的機房，部分服務上 AWS，兩邊網路需要互通
    2. **資料庫遷移過渡期**：地端 DB 還沒搬完，AWS 上的應用需要連回去
    3. **合規需求**：敏感資料必須留在地端，但其他服務使用 AWS
86. ★★ VGW 考試重點與限制⭐️
    1. **觸發關鍵字**：題目出現 **Site-to-Site VPN** 或 **Direct Connect** → 一定會有 VGW
    2. **架構限制**：VGW **attach 到 VPC**，一個 VPC 只能 attach 一個 VGW
    3. **擴展方案**：要連接**多個 VPC 到地端**，考慮用 **Transit Gateway** 取代多個 VGW（更好管理）
87. ★★ RDS 自動備份 vs AWS Backup — 定位與職責分離
    1. **RDS 自動備份定位**：短期 operational recovery（誤刪資料、回滾錯誤部署、point-in-time recovery），保留期最長 **35 天**
    2. **AWS Backup 定位**：長期 archive / compliance（法規合規如金融業 7 年、災難復原、審計需求），保留期**無上限**
    3. **設計理念**：RDS 保持簡單專注資料庫本身；AWS Backup 統一管理所有 AWS 資源的備份策略
88. ★★ RDS 35 天硬性限制的架構原因
    1. **非成本考量**：若是成本問題，AWS 可以讓你付更多錢延長天數，但選擇**硬性限制 35 天**是刻意的架構決策
    2. **職責分離原則**：RDS 不該承擔長期備份管理的複雜度（跨區域複製策略、合規性報告、多服務統一管理、生命週期自動化）
    3. **正確分工**：這些長期備份需求是 **AWS Backup 的專長**，讓 RDS 處理反而是錯誤的職責分配
89. ★★ RDS 備份考試判斷 — 35 天是關鍵門檻
    1. **觸發關鍵字**：題目出現「**超過 35 天**」的備份需求 → 答案必定指向 **AWS Backup**
    2. **記憶點**：35 天是 RDS 自動備份的硬性上限，無法突破
90. ★★ Glacier Provisioned Capacity — 只為 Expedited 存在
    1. **核心答案**：**Standard 不需要買 Provisioned Capacity**，AWS 保證 capacity 充足不會失敗
    2. **Expedited 需求場景**：資源有限，高峰期可能「搶不到」導致請求失敗，Provisioned Capacity 等於「預約專屬插隊通道」
    3. **Bulk 不需要**：本來就是低優先級排隊處理，不存在「搶不到」的問題
91. ★ Glacier Provisioned Capacity 運作邏輯
    1. **Expedited 的插隊特性**：1-5 分鐘取回需要 AWS 立刻調動資源，當很多人同時插隊時資源不夠就會拒絕請求
    2. **Provisioned Capacity 保證**：預約後不管多少人在搶，你的 Expedited 請求一定會被處理
    3. **Standard/Bulk 保證**：排隊處理機制，AWS 會在 3-5 小時或 5-12 小時內消化完
92. ★★ Glacier Provisioned Capacity 考試判斷
    1. **觸發關鍵字**：「**保證**緊急取回」、「**確保** Expedited 可用」→ **Provisioned Capacity**
    2. **排除情境**：單純問 Standard/Bulk 取回 → 不會出現 Provisioned Capacity 選項
93. ★★ CloudTrail Management Events vs Data Events — 粒度差異
    1. **Management Events**：記錄「誰動了 bucket **本身**」（如門禁系統記錄「誰進出大樓」）
    2. **Data Events**：記錄「誰動了 bucket **裡的檔案**」（如監視器記錄「誰拿了哪個包裹」）
    3. **核心差異**：粒度不同，一個管控制平面、一個管資料平面
94. ★ CloudTrail Data Events 預設不啟用的原因
    1. **日誌量爆炸**：一個 bucket 可能每秒幾千次 GetObject/PutObject
    2. **費用爆炸**：CloudTrail 按事件數收費，全記錄成本極高
    3. **設計邏輯**：AWS 讓你自己決定是否承擔這個成本
95. ★★ CloudTrail Events 考試關鍵字辨識
    1. **Management Events 觸發詞**：「誰刪了 bucket」「誰改了 bucket policy」→ **預設就有**
    2. **Data Events 觸發詞**：「誰下載了某個檔案」「追蹤物件存取」→ **要手動開啟**
96. ★ IAM Database Authentication 完整設定流程 — 三層配置
    1. **RDS 端設定**：啟用 IAM Database Authentication 功能（建立或修改 DB instance 時勾選），讓 RDS 接受 token 認證
    2. **IAM 端設定**：建立 Policy 授權哪些 user/role 可以連哪個 DB instance 的哪個 DB user，定義「誰」可以對「哪個 RDS」產生 token
    3. **DB 內部設定**：還是要建立對應的 database user，只是認證方式改成 IAM（不是給密碼）
    4. **記憶點**：三層都要到位才能用
97. ★★ EKS 雙層擴展觸發機制 — HPA 與 Karpenter 協同運作⭐️
    1. **觸發順序**：流量增加 → HPA 偵測 CPU/Memory 超過閾值 → HPA 增加 Pod replicas → Kubernetes Scheduler 嘗試調度新 Pods
    2. **分岔判斷**：現有 Nodes 資源足夠 → Pods 成功調度，結束；資源不足 → Pods 進入 **Pending 狀態** → Karpenter 偵測並啟動新 Nodes
    3. **關鍵原則**：現有 Nodes 資源還撐得住時，**只會觸發 HPA**，Karpenter 不會動作
98. ★ HPA vs Karpenter 觸發條件精確對比
    1. **HPA 觸發條件**：Pod 的 CPU/Memory 超過設定閾值（負載在閾值內則不觸發）
    2. **Karpenter 觸發條件**：有 Pods 處於 **Pending 狀態**（因資源不足無法調度）（所有 Pods 都能順利調度則不觸發）
    3. **簡單記法**：HPA 管「要不要加 Pod」，Karpenter 管「有沒有地方放 Pod」
99. 😇 EKS 自動擴展架構特性 — 按需觸發的兩層設計
    1. **運作模式**：兩層架構按需觸發，不是每次都兩層一起動
    2. **常見情況**：大部分時候只有 HPA 在運作，Karpenter 只在真正需要更多運算資源時才介入
