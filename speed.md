# Super Speed Mode

- ★★ DataSync vs Storage Gateway — 「**遷移**」vs「**整合**」⭐️
    1. **DataSync**：一次性或定期的資料**遷移/搬遷**（關鍵詞：migrate, move, transfer, accelerate）
    2. **Storage Gateway**：混合雲**持續整合**，讓地端像在用雲端（關鍵詞：hybrid, integrate, on-premises access）
    3. **判斷技巧**：題目說 "immediately move" → DataSync；題目說「地端需持續存取雲端」→ Storage Gateway
- ★★ CloudTrail Data Events vs Management Events
    1. **Management Events**：控制平面操作（CreateBucket, DeleteBucket 等），**預設啟用**
    2. **Data Events**：資料平面操作（GetObject, PutObject 等），**預設不啟用，需手動開啟**⭐️
    3. **考試重點**：題目要「物件層級稽核」→ 必須啟用 Data Events
- ★★ AWS Batch vs Lambda — 運算密集型任務的正確選擇
    1. **Lambda 硬限制**：最大執行 15 分鐘、記憶體 10 GB、最多 6 vCPU⭐️
    2. **結論**：Lambda 為「短暫、輕量、事件驅動」設計，不適合重型運算
- ★★ AWS Batch 核心概念⭐️
    1. **本質**：批次運算任務的編排服務，自動開 EC2、分配任務、跑完關機
    2. **平行執行**：定義多個 jobs，Batch 自動分散到多台 EC2
    3. **資源彈性**：可指定 EC2 instance type（如 `r5.8xlarge` 有 32 vCPU + 256 GB RAM）
    4. **最少維運**：不用自己管 EC2 lifecycle、job queue、失敗重試
- ★ AWS Batch 選擇時機 — 關鍵字組合⭐️
  - **Batch processing / 批次處理**
  - **HPC / 高效能運算 / simulation**
  - **長時間運算**（超過 15 分鐘）
  - **大量平行任務 + 需要大資源**
  - **「least operational overhead」+ 運算密集**
- ★ IAM DB Authentication 限制與特性
    1. **支援引擎**：僅 MySQL、PostgreSQL（**不支援 SQL Server、Oracle**）⭐️
    2. **Token 有效期**：15 分鐘
    3. **強制加密**：必須使用 SSL/TLS 連線
- ★★ EBS Encryption 考題陷阱⭐️
    1. **題目問**：如何確保 EBS 資料在傳輸過程中的安全？
    2. **陷阱選項**：設定 Security Group、使用 VPN
    3. **正確答案**：啟用 EBS Encryption（**自動處理 in-transit 加密**）
    4. **記憶點**：EBS Encryption = **at-rest + in-transit 都包了**
- ★★ Step Scaling vs Target Tracking Scaling — 關鍵判斷⭐️
    1. **Step Scaling**：**你自己指定** scaling metrics 和 threshold values
    2. **Target Tracking**：**AWS 幫你調**，只需設定目標值（如 CPU 維持 50%）
    3. **考試技巧**：看到「specifying the scaling metrics and threshold values」→ **Step Scaling**
- ★★ EKS 自動擴展 — 兩層擴展架構
    1. **應用層（Pods）**：Horizontal Pod Autoscaler (HPA)，依 CPU/Memory 使用率觸發
    2. **基礎設施層（Nodes）**：Karpenter 或 Cluster Autoscaler，當 Pods 無法調度時觸發
    3. **協同運作**：流量增加 → HPA 增加 Pods → Node 資源不足 → Karpenter 增加 Nodes⭐️
- ★★ Aurora Failover 機制 — **有無 Replica** 決定行為⭐️
    1. **有 Aurora Replica**：CNAME 翻轉指向健康 Replica，Replica **自動升級**為 Primary（**約 30 秒內**）
    2. **沒有 Replica（單一 instance）**：在同一 AZ 嘗試**重建**新的 DB instance（較慢，best-effort，通常需要幾分鐘甚至 10 分鐘）
    3. **考試陷阱**：看到「single Aurora instance」→ 沒有 Replica 可 promote，只能重建
- ★★ Placement Group 考試選擇技巧⭐️
    1. **Cluster PG**：需要超低延遲、高頻寬連線（HPC、高效能運算）
    2. **Spread PG**：少量關鍵 instance 需極端隔離（DB replica）
    3. **Partition PG**：大規模分散式系統需機架級隔離（Cassandra、Hadoop）
- ★★ Aurora Endpoints — Cluster 自動建立，應用程式決定怎麼用
    1. **這兩種 Endpoint（全部自動建立）**：
       - **Cluster Endpoint (Writer)**：永遠指向 Primary，用於寫入
       - **Reader Endpoint**：自動負載均衡到所有 Replicas，用於讀取
    2. **Reader Endpoint 行為差異**：
       - 有 Replicas → 流量分散到 Replicas
       - 沒 Replicas → 退回連接 Primary（不會報錯，但無讀寫分離效果）
    3. **實務重點**：Endpoints 自動存在，但**應用程式要主動區分讀寫連線**才有效果⭐️
- ★★ IAM 外部身分與 Assume Role — 核心概念⭐️
    1. **外部身分定義**：不在你 AWS 帳戶內建立的 IAM User（跨帳戶用戶、Federated Users、Web Identity、SAML 2.0）
    2. **為何用 Role**：無法在你帳戶幫外部人建立 IAM User，改用 **Assume Role + STS** 取得臨時憑證
    3. **運作流程**：外部身分 → Assume Role → 獲得臨時憑證 → 存取 AWS 資源
- ★ S3 Object Lambda — 即時攔截處理 S3 GET 請求⭐️
    1. **本質**：在 GET 請求和回傳之間插入 Lambda 做即時處理（**攔截器模式**）
    2. **運作流程**：Client → S3 Object Lambda Access Point → Lambda 處理 → 轉換後的檔案
    3. **典型場景**：同一份資料不同角色看不同內容（遮蔽 PII）、動態調整圖片大小、動態加浮水印
- ★★ NACL vs Security Group vs Route Table — 作用層級區分⭐️
    1. **Route Table**：關聯於 **Subnet 層級**，決定流量「往哪走」（路由方向）
    2. **NACL**：作用於 **Subnet 層級**，決定流量「能不能進出」（Allow/Deny）
    3. **Security Group**：作用於 **Instance 層級**（ENI），決定流量「能不能到達 instance」
    4. **IGW**：存在於 **VPC 層級**，但要透過 Route Table 設定才會被使用
- ★★ DynamoDB Global Table 核心定位 — Active-Active 全球部署⭐️
    1. **本質**：**active-active 多區域架構**，DR 只是附帶效果
    2. **主要用途**：**降低**全球用戶**延遲**（歐洲用戶打歐洲 table）、**流量分散**（各區域獨立承載）⭐️
    3. **流量承載力**：線性擴展，開 3 個區域 ≈ 3 倍寫入吞吐上限（假設流量分散）
- ★★ DynamoDB Global Table 考試關鍵字與陷阱
    1. **典型題目**：「服務全球用戶 + 低延遲存取 + 災難復原能力」→ **DynamoDB Global Table**
    2. **排除情境**：題目強調 **strong consistency**（強一致性）→ Global Table 不適合（它是**最終一致**）⭐️
    3. **對比選擇**：需要 **relational data + 跨區域** → **Aurora Global Database**
- ★★ Security Group vs NACL — 來源/目標表示法差異
    1. **共同支援**：CIDR block（`10.0.1.0/24`）、單一 IP（`10.0.1.5/32`）
    2. **Security Group 獨有**：可引用**另一個 Security Group ID**（`sg-0abc1234`）、**Prefix List**（`pl-xxx`）⭐️
    3. **NACL 限制**：純網路層（Layer 3/4），**只認 CIDR**，不認識 Security Group
- ★★ RDS 底層架構 — 運算與儲存分離⭐️
    1. **本質**：RDS 跑在 **AWS 管理的 EC2 instance** 上，使用者看不到也摸不到
    2. **架構**：RDS Instance（運算層）↔ EBS Volume（儲存層），資料**本來就存在 EBS 上**
- ★★ **RDS** Stop vs Terminate — 費用差異
    1. **Running**：Instance 收費 + EBS 收費
    2. **Stopped**：Instance **不收費**，EBS **繼續收費**⭐️而且只能停 7 天
    3. **Terminated**：兩者都消失（除非有 snapshot）
    4. **關鍵點**：Stop 只是停止運算，**儲存費用持續計費**
- ★ Virtual Private Gateway (VGW) 核心概念
    1. **定義**：AWS VPC 端用來建立 VPN 連線的閘道器，是 VPC 的 VPN 出口
    2. **架構角色**：On-Premises (**Customer Gateway**) ──VPN 連線── VGW ──你的 VPC
    3. **對照關係**：
       - **CGW (Customer Gateway)**：代表公司端的 VPN 設備/路由器
       - **VGW**：代表 AWS 端的 VPN 進入點，**attach 到 VPC 上**⭐️
- ★★ VGW vs Transit Gateway 考試場景選擇⭐️
    1. **選 VGW**：單一 VPC + 單一/少數 Site-to-Site VPN
    2. **選 Transit Gateway**：多 VPC 互連 + 多個 VPN
    3. **選 VPN CloudHub**：多分公司要互連、低成本（**基於 VGW**）
- ★★ EIP 與 ENI 的關聯關係 — 技術架構
    1. **底層事實**：EIP 實際關聯到 **ENI (Elastic Network Interface)**，非直接關聯到 EC2 instance⭐️
    2. **架構層級**：EIP → ENI → EC2
    3. **Console 簡化**：每個 EC2 預設有 **primary ENI**，Console 讓你看起來像直接附到 EC2 上
- ★★ NAT Gateway 高可用架構 — 每個 AZ 獨立部署⭐️
    1. **核心原則**：NAT Gateway 是 **AZ-bound 資源**，只存在於一個 AZ 內，無法跨 AZ 服務
    2. **唯一正確做法**：每個 AZ 部署獨立的 NAT Gateway，並在該 AZ 的 private subnet Route Table 指向本 AZ 的 NAT Gateway
    3. **跨 AZ 使用的問題**：違反高可用原則（AZ-A 掛了，AZ-B 的 private subnet 也斷網）、跨 AZ 流量收費
- ★★ IAM Role 本質 — 給任何需要臨時權限的實體
    1. **適用對象**：
       - AWS 服務（EC2、Lambda 存取 S3）
       - 外部使用者（透過 federation 登入的企業員工）
       - 跨帳號存取（Account A 存取 Account B）
       - 應用程式（使用 AssumeRole 取得臨時憑證）
    2. **核心概念**：IAM Role 是**權限容器**，不限於 AWS 服務專用⭐️
- ★★ Federation 場景下的 IAM Role 運作機制⭐️
    1. **場景**：企業員工在 AD 裡，想登入 AWS Console，但沒有 IAM User
    2. **運作流程**：員工用 AD 帳號驗證身份（AD Connector 負責）→ AD Group 對應到特定 IAM Role（如 `AD-DevOps-Group` → `IAM-DevOps-Role`）→ 員工登入後 **Assume** 這個 Role，獲得臨時權限
    3. **核心解答**：AWS 透過 **Role** 知道員工權限，Role 是外部身份進入 AWS 後的權限載體
- ★★ ALB 原生支援 Lambda 作為 Target — Serverless HTTP 處理⭐️
    1. **推出時間**：AWS 在 2018 年加入此功能
    2. **架構優勢**：傳統 ALB → EC2（要管 server、流量低也付費）vs ALB → Lambda（Serverless、按需付費）
    3. **用途**：將部分 API endpoints 用 Lambda 處理，不用全部跑在 EC2 上
- ★★ Route Table 與 NACL 的層級歸屬 — VPC 資源但關聯 Subnet
    1. **定義層級**：Route Table 和 NACL 都是 **VPC 層級的資源**（建立時屬於某個 VPC）
    2. **作用層級**：它們的**作用對象是 Subnet**（綁定到 Subnet 才生效）⭐️
    3. **強制關聯**：每個 Subnet **必須**關聯一個 Route Table（沒指定就用 Main Route Table）和一個 NACL（沒指定就用 Default NACL）
- ★★ VGW (Virtual Private Gateway) 核心定位與用途⭐️
    1. **定義**：VPC 的對外連接閘道，**專門用來連接 AWS 以外的網路（地端機房或其他雲端）**
    2. **連接方式**：
       - Site-to-Site VPN（透過 IPsec VPN 連接地端到 AWS VPC）
       - Direct Connect（透過專線連接，更穩定、更低延遲、更貴）
- ★★ VGW 考試重點與限制⭐️
    1. **觸發關鍵字**：題目出現 **Site-to-Site VPN** 或 **Direct Connect** → **一定**會有 VGW
    2. **架構限制**：VGW **attach 到 VPC**，一個 VPC 只能 attach 一個 VGW
    3. **擴展方案**：要連接**多個 VPC 到地端**，考慮用 **Transit Gateway** 取代多個 VGW（更好管理）
- ★★ RDS 自動備份考試判斷 — 35 天是關鍵門檻⭐️
    1. **觸發關鍵字**：題目出現「**超過 35 天**」的自動備份需求 → 答案必定指向 **AWS Backup**
    2. **記憶點**：35 天是 RDS 自動備份的硬性上限，無法突破
    3. **手動備份沒有天數限制**，但題目通常不會問這個
- ★★ Glacier Provisioned Capacity — 只為 Expedited 存在⭐️
    1. **核心答案**：**Standard 不需要買 Provisioned Capacity**，AWS 保證 capacity 充足不會失敗
    2. **Expedited 需求場景**：資源有限，高峰期可能「搶不到」導致請求失敗，Provisioned Capacity 等於「預約專屬插隊通道」
    3. **Bulk 不需要**：本來就是低優先級排隊處理，不存在「搶不到」的問題
- ★★ CloudTrail Management Events vs Data Events — 粒度差異⭐️
    1. **Management Events**：記錄「誰動了 bucket **本身**」（如門禁系統記錄「誰進出大樓」）
    2. **Data Events**：記錄「誰動了 bucket **裡的檔案**」（如監視器記錄「誰拿了哪個包裹」）
    3. **核心差異**：粒度不同，一個管控制平面、一個管資料平面
- ★★ EKS 雙層擴展觸發機制 — HPA 與 Karpenter 協同運作⭐️
    1. **觸發順序**：流量增加 → HPA 偵測 CPU/Memory 超過閾值 → HPA 增加 Pod replicas → Kubernetes Scheduler 嘗試調度新 Pods
    2. **分岔判斷**：現有 Nodes 資源足夠 → Pods 成功調度，結束；資源不足 → Pods 進入 **Pending 狀態** → Karpenter 偵測並啟動新 Nodes
    3. **關鍵原則**：現有 Nodes 資源還撐得住時，**只會觸發 HPA**，Karpenter 不會動作
- ★★ Aurora Replica 不會自動分流流量 — 程式碼主導架構⭐️
    1. **核心概念**：Replica 不會自己發揮作用，就是在那邊等；如果程式碼全部打 Cluster Endpoint，那些 Replica 就是吃資源、花錢、零貢獻
    2. **唯一剩下價值**：**failover 備援** — Primary 掛了，Aurora 自動把某個 Replica 升級成新的 Primary
    3. **關鍵理解**：Replica 的備援功能與「讀寫分離提升效能」是兩回事
- ★★ Aurora 讀寫分離考試重點 — 效能未改善的原因分析⭐️
    1. **典型題目**：「加了 Read Replica 但效能沒改善，為什麼？」
    2. **標準答案**：應用程式**沒有把讀取流量導向 Reader Endpoint**
    3. **記憶點**：Replica 存在 ≠ 自動分流，必須程式碼主動使用才有效
- ★ AWS Region 與 Global Service 區分⭐️
    1. **大部分服務**：Region 級別（資源建立在特定 Region）
    2. **Global Service（需記住）**：IAM（身份與權限管理）、Route 53（DNS）、CloudFront（CDN）
    3. **考試重點**：記住這三個 Global Service
- ★★ EBS Multi-Attach 特性與限制
    1. **支援卷類型**：僅限 **io1 / io2**（高效能 IOPS 類型）
    2. **掛載上限**：最多可同時掛載 **16 台 EC2**
    3. **AZ 限制**：必須在**相同 AZ** 內⭐️
- ★★ ALB 流量無法導向後端 — 考試陷阱題⭐️
    1. **典型題目**：ALB 沒辦法將流量導向後端
    2. **排查重點**：檢查 **Target Group 未設定**或**健康檢查失敗**
- ★ DNS 與 ALB 整合⭐️需要把自訂網域指向 ALB 的 DNS 名稱
    1. **自動 DNS**：ALB 建立後會自動生成 AWS 提供的 DNS 名稱（例如：`my-alb-123456.us-east-1.elb.amazonaws.com`）
    2. **自訂網域**：
       - **Subdomain**（如 `www.example.com`）：CNAME 或 Alias 皆可
       - **Root domain**（如 `example.com`）：**必須用 Route 53 Alias**（A record type）
- ★ DynamoDB 定價模式對比⭐️
    1. **核心定位**：為「彈性、高速、無需管理基礎架構」而設計，適合高流量應用
    2. **On-Demand 模式**：不需預先配置容量、按請求次數與資料大小計費、**單位成本高但彈性高**
    3. **Provisioned 模式**：手動配置 Read/Write Capacity Units (RCUs/WCUs)、適合流量穩定、**成本較低但需管理容量**
- ★★ **Lake Formation**：**跨帳號資料湖查詢**解決方案⭐️
    1. **題目關鍵**：跨帳號查詢 + 基於角色的存取控制
    2. **解決問題**：集中管理多帳號 S3 資料（不需移動資料）、統一權限管理（table/column 層級）、自動整合 AWS Glue 做資料目錄、最小化營運成本
- ★★ **企業級身份整合**：AD Connector + IAM Roles 解決方案⭐️
    1. **題目關鍵**：已有 AD (Active Directory) + 需要 federation(聯合身份驗證) + 角色已在 AD 的 group 中定義
    2. **正確架構**：Corporate AD (已有 groups & users) → AD Connector (橋接) → IAM Roles (權限對應) → AWS Console Access
- ★★ **IAM Groups vs AD Groups** 核心差異
    1. **IAM Groups**：AWS 內部的權限分組機制（給 IAM Users 用）
    2. **AD Groups**：企業 Active Directory 中的群組（已有權限管理）
    3. **錯誤迷思**：選 IAM Groups 等於在 AWS 重新建群組，違反 federation 精神，造成權限管理雙軌制⭐️
- ★ DDoS 防護選擇邏輯⭐️
    1. **WAF 適用**：單純**應用層**攻擊、成本考量、filter HTTP traffic
    2. **Shield Advanced 適用**：嚴重 DDoS、高價值系統、需專家支援、series of attacks
    3. **關鍵字觸發**：fortify/series of attacks → Shield Advanced
- ★ Auto Scaling 選擇邏輯⭐️
    1. **Scheduled Scaling**：時間點觸發(cron)、固定規律(上班時間)、設定簡單
    2. **Predictive Scaling**：ML 預測流量、不規則波動、需歷史數據
    3. **關鍵判斷**：8:50 先開機器 → 9:00 用戶來時已 ready（Scheduled 優勢）
- ★★ **Predictive Scaling 致命限制**：**異質**實例容量計算錯誤⭐️
    1. **核心問題**：假設**實例同質化**(homogenous)，題目說 different instance types and sizes → 會算錯容量
    2. **錯誤邏輯**：預測需要 100 單位容量，當前 10 台機器 → 誤以為每台 10 單位 → 建議擴展數量不準
    3. **實例場景**：5台 t3.large (各4單位) + 5台 t3.xlarge (各12單位) = 80單位，但 Predictive 誤判為每台8單位
- ★★ **Aurora Auto Scaling vs Global Database** 選擇邏輯⭐️
    1. **Auto Scaling 適用**：**單一區域**讀取擴展、突發流量處理、read replica 跟不上流量
    2. **Global Database 適用**：跨區域災難復原、全球用戶低延遲讀取、多區域部署
    3. **關鍵判斷**：題目有提「全球用戶」、「跨區域」、「災難復原」嗎？**沒有** → Auto Scaling
    4. Hence, the correct answer is: Use automatic scaling for the Aurora read replica using Aurora Auto Scaling.
- 😇 **Aurora 讀取擴展的完整選項**比較⭐️
    1. **Aurora Auto Scaling**：自動管理 read replica 數量、基於 CPU/連線數等指標（最適合突發流量）
    2. **手動增加 read replica**：最多 **15** 個 read replica、無法應對突發流量
    3. **Aurora Global Database**：跨區域複製、適合全球業務
    4. **Aurora Serverless v2**：連 writer 也能自動擴展、適合不可預測的極端波動
- ★★ **MGN (Migration Service) vs Managed Services** 選擇原則
    1. **MGN 適合場景**：大規模批次遷移（數百台 VM）、遷移後逐步現代化、沒有對應 AWS 託管服務⭐️
    2. **本題不適用原因**：只有 1 個資料庫 + 1 個應用、Oracle 有 RDS 版本、.NET 有 Beanstalk
    3. **考試核心**：偏好 **managed service over self-managed**，除非題目明確限制
- ★★ **AWS Transfer Family** 檔案傳輸服務核心概念
    1. **服務定位**：**託管的檔案傳輸服務**⭐️
       - 支援協定：SFTP、FTPS、FTP、AS2
       - 後端儲存：S3 或 EFS
       - 無需管理伺服器
    2. **題目關鍵字識別**：快速判斷是否使用 Transfer Family
       - existing SFTP clients
       - third-party partners upload files
       - legacy file transfer workflow
       - minimal changes to existing process
       - B2B file exchange
    3. **核心優勢**：保留舊有傳輸方式 + 搬到雲端儲存
       - 不用改客戶端程式
       - 不用自己管 SFTP server
       - 檔案自動進 S3
- ★ **FSx 系列選擇陷阱與記憶技巧**
    1. **考試陷阱識別**：看到 "Windows" + "high performance" 需求，別被 "Windows File Server" 名字誤導
    2. **判斷關鍵字**："trading application" + "block storage" → 需要 **iSCSI 協定** → 只有 **FSx for NetApp ONTAP**
    3. **服務定位記憶**：FSx for Windows = **檔案共享專用**（SharePoint、Home Directories）；FSx for NetApp ONTAP = **全功能儲存**（檔案 + 區塊）
- ★★ **共享存取陷阱**：跨平台儲存選擇邏輯
    1. **錯誤判斷**："共享存取" ≠ 一定是 EFS
    2. **正確選擇**：**Windows** → FSx for Windows；**Linux** → EFS
    3. **跨 AZ 區塊儲存**：只有 **FSx for NetApp ONTAP** 支援⭐️
    4. **EBS 限制**：**不支援跨 AZ** 共享
- 😇 **儲存類型記憶口訣**
    1. **Block**：資料庫⭐️/交易系統 = **iSCSI**
    2. **File**：檔案共享/使用者目錄 = **SMB/NFS**
    3. **Object**：其它都能用 = **S3**
- ★★ **IAM DB Authentication** EC2 安全連接 RDS 解決方案
    1. **題目關鍵**：如何讓 EC2 安全地連接 RDS
    2. **核心概念**：用 **IAM role** 取代傳統的帳號密碼登入資料庫
    3. **傳統方式問題**：EC2 → (username/password) → RDS ❌ 密碼外洩風險
    4. **IAM 方式優勢**：EC2 → (temporary token from IAM role) → RDS ✅ 無需管理密碼
- ★ IAM DB Authentication 三大優勢⭐️
    1. **加密傳輸**：強制使用 **SSL/TLS**
    2. **集中管理**：用 IAM 統一控制誰能存取資料庫，不用在每個 RDS instance 上管理帳密
    3. **EC2 無需密碼**：直接用 EC2 的 **instance profile** 拿 token，**15 分鐘有效期**後自動失效
- ★★ **IAM DB Authentication 設定流程**：RDS 功能配置要點
    1. **服務歸屬**：**RDS 的功能**，不是 EC2 的功能
    2. **RDS 端設定**：RDS Console → Database → Modify → 勾選 "Password and IAM database authentication"
    3. **EC2 端權限**：IAM role 需要 **rds-db:connect** 權限，指向特定 RDS resource ARN
    4. **資料庫內部**：建立對應的 IAM user (如: `CREATE USER iam_user; GRANT rds_iam TO iam_user;`)
- ★ IAM DB Authentication 完整流程
    1. **步驟一**：RDS 啟用 IAM Authentication（在 RDS console）⭐️
    2. **步驟二**：EC2 附加 IAM role（有 rds-db:connect 權限，在 IAM console）⭐️
    3. **步驟三**：在資料庫內建立 IAM user（用 SQL）
    4. **步驟四**：程式用 boto3 拿 token 連線（在 code 中）
- ★★ **S3 Access Points**：**多應用權限管理**解決方案⭐️
    1. **問題場景**：一個 S3 bucket 被多個應用/團隊共用，每個都需要不同權限，bucket policy 會變得超複雜
    2. **解決方案**：Access Point = bucket 的**命名入口**，每個入口有自己的權限政策
    3. **架構設計**
       - Application A → Access Point A (read-only)
       - Application B → Access Point B (write)
       - Application C → Access Point C (VPC-only)
       - 所有 Access Points → Same S3 Bucket
- ★ S3 **VPC-only** Access Points 內網安全存取
    1. **用途**：強制流量只能從特定 VPC 進來，不走公網
    2. **關鍵字**："restrict S3 access to private network" / "VPC endpoint"
    3. **考試重點**：內部應用安全存取 S3 的首選方案
- ★★ **EventBridge** 關鍵字識別與考試重點⭐️
    1. **典型關鍵字**："when X happens, do Y"、"automatically trigger"、"event-driven"
    2. **常考場景**
       - S3 事件觸發 Lambda/ECS/Step Functions
       - 定時任務 (cron 排程)
       - 跨服務整合 (EC2 狀態變化 → SNS 通知)
- ★★ **EventBridge vs S3 Event Notification** 目標服務差異
    1. **S3 Event Notification**：只能觸發 **Lambda/SQS/SNS**（限制較多）⭐️
    2. **EventBridge**：可觸發**更多目標服務** (ECS/Step Functions/跨帳號...)
    3. **關鍵記憶**：題目要觸發 **ECS task** → 用 EventBridge，不是 S3 Event Notification
- ★★ **KMS Custom Key Store + CloudHSM** 完全掌控加密金鑰解決方案
    1. **題目關鍵需求**
       - **完全控制金鑰** (full control over encryption keys)
       - **立即移除金鑰** (immediately remove key material from KMS)
       - **獨立稽核** (audit independently of CloudTrail)
    2. **一般 KMS 的限制**：金鑰存在 AWS 的 HSM 中（多租戶共享）、刪除金鑰有 7-30 天等待期、稽核只能靠 CloudTrail
- ★★ Custom Key Store 核心架構與運作原理⭐️
    1. **架構設計**：KMS Custom Key Store → 關聯你自己的 CloudHSM cluster → 金鑰材料「只」存在你的 HSM 中 → 直接控制 HSM 可立即移除金鑰
    2. **實際運作流程**
       - 創建 KMS key 時，金鑰材料生成在「你的 CloudHSM」
       - 金鑰永不以明文離開你的 HSM
       - 所有加密操作都在你的 HSM 上執行
       - 可直接管理 CloudHSM → 立即刪除金鑰
- ★ Custom Key Store 考試判斷關鍵字⭐️
    1. **觸發關鍵字**
       - "full control over keys"
       - "immediately remove"（不是 schedule deletion）
       - "audit independently"（不只靠 CloudTrail）
    2. **其他識別詞**
       - "dedicated HSM"
       - "single-tenant"
       - "compliance" + "hardware control"
- ★★ **Aurora Global Database** 跨區域資料庫複製解決方案⭐️
    1. **技術特性**：使用 **storage-based 技術**實現 < 1 秒的資料同步延遲
    2. **災難恢復優勢**：**RPO < 1 秒**（幾乎零資料遺失）、**RTO < 1 分鐘**（快速故障切換）
    3. **適用場景**：全球化應用需要各地低延遲讀取、對區域級災難 (region-wide outage) 有嚴格容錯要求
    4. **考試關鍵字**："global"、"cross-region DR"、"RTO/RPO 極致要求"
- ★★ **題目陷阱分析**：Control Tower vs RAM 選擇邏輯
    1. **題目關鍵字**："centrally manages" + "share resources"
    2. **核心差別**：Control Tower 負責**管理帳戶本身**，但不負責**共享具體資源**
    3. **判斷原則**：題目明確提到要共享 Transit Gateway、License Manager、Route 53 Resolver 規則 → 這正是 **RAM 的專屬功能**⭐️
    4. **類比記憶**：Control Tower 像是「帳戶管理員」，RAM 才是「資源共享平台」
- ★★ **Federation + STS** 身份轉換機制⭐️
    1. **Federation Proxy/Identity Provider + STS**：**目的**將企業 AD/LDAP 身份轉換成 AWS 臨時憑證
    2. **運作流程**：使用者登入 AD → Identity Provider 驗證 → **STS 發放臨時 token**
    3. **關鍵優勢**：STS (Security Token Service) 產生**臨時憑證**，避免建立大量 IAM user
- ★★ **IAM Role + Policy** 權限控制機制
    1. **IAM Role**：定義 STS 臨時憑證要扮演的角色
    2. **IAM Policy**：限制每個使用者只能存取自己的 folder
    3. **動態權限**：Policy 使用 `${aws:username}` 變數動態對應資料夾
- ★ **Federation 雙元件設計**原理與記憶技巧
    1. **為什麼需要兩個元件**：單獨 STS **只能產生臨時憑證**，但沒定義**權限範圍** → 需要 IAM **Role + Policy** 告訴 AWS「這個臨時憑證能做什麼」⭐️
    2. **記憶口訣**
       - **企業現有帳號系統 + AWS** → Federation + STS
       - **限制個別使用者權限** → IAM Role + Policy with variables
       - **大量使用者** → 絕對不是建立 1200 個 IAM user
