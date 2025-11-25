# Speed Notes for SAA

1. ★★ **Lake Formation** 跨帳號資料湖查詢解決方案
   1. **題目關鍵**：跨帳號查詢 + 基於角色的存取控制
   2. **解決問題**：集中管理多帳號 S3 資料（不需移動資料）、統一權限管理（table/column 層級）、自動整合 AWS Glue 做資料目錄、最小化營運成本
2. ★ **Control Tower** 與 Lake Formation 差異分析
   1. **Control Tower 用途**：設定多帳號環境、套用 guardrails（帳號管理工具）
   2. **關鍵差別**：不處理資料查詢或存取控制，管理 AWS 帳號 ≠ 管理資料湖
   3. **記憶類比**：Control Tower 像公司 IT 管帳號，Lake Formation 像資料庫管查詢權限
3. 😇 Lake Formation vs Control Tower 判斷表格
   1. **跨帳號資料查詢** → Lake Formation
   2. **基於角色存取控制** → Lake Formation
   3. **帳號層級管理** → Control Tower
   4. **Guardrails/合規** → Control Tower
   5. **快速記憶法**："data lake" + "query" → Lake Formation
4. ★★ **企業級身份整合**：AD Connector + IAM Roles 解決方案
   1. **題目關鍵**：已有 AD (Active Directory) + 需要 federation(聯合身份驗證) + 角色已在 AD 的 group 中定義
   2. **正確架構**：Corporate AD (已有 groups & users) → AD Connector (橋接) → IAM Roles (權限對應) → AWS Console Access
5. ★★ **IAM Groups vs AD Groups** 核心差異
   1. **IAM Groups**：AWS 內部的權限分組機制（給 IAM Users 用）
   2. **AD Groups**：企業 Active Directory 中的群組（已有權限管理）
   3. **錯誤迷思**：選 IAM Groups 等於在 AWS 重新建群組，違反 federation 精神，造成權限管理雙軌制
6. 😇 企業身份整合記憶技巧
   1. **AD Connector**：串接現有身份來源
   2. **IAM Roles**：對應權限（支援 federation）
   3. **判斷法則**：看到「corporate AD」+「federation」→ 直接排除 IAM Groups
7. ★★ **Shield Advanced vs WAF** DDoS 防護差異
   1. **題目關鍵**：DDoS attacks(複數) + fortify infrastructure + protect data（要求全面防護）
   2. **攻擊分層**：Layer 7 (應用層 HTTP flood) vs Layer 3/4 (網路層 SYN flood, UDP flood)
   3. **WAF 限制**：只防 Layer 7 (HTTP/HTTPS)
   4. **Shield Advanced 優勢**：防 Layer 3/4/7 全層級 + 整合 WAF + DDoS Response Team + 成本保護
8. ★ **DDoS 防護選擇邏輯**
   1. **WAF 適用**：單純應用層攻擊、成本考量、filter HTTP traffic
   2. **Shield Advanced 適用**：嚴重 DDoS、高價值系統、需專家支援、series of attacks
   3. **關鍵字觸發**：fortify/series of attacks → Shield Advanced
9. 😇 DDoS 防護記憶技巧
   1. **Shield = 盾牌 = 全方位保護**
   2. **WAF = 應用層防火牆 = 只管 HTTP**
   3. **判斷原則**：看到「DDoS」且沒有成本限制 → 先考慮 Shield Advanced
10. ★★ **Scheduled Scaling vs Predictive Scaling** 核心差異
    1. **Predictive Scaling 限制**：需要歷史數據訓練，不適用於固定模式
    2. **題目陷阱**：start of the day 問題每天發生 = 可預測的固定模式
    3. **反應速度差異**：Scheduled 提前準備好 vs Predictive 預測後才擴展
11. ★ **Auto Scaling 選擇邏輯**
    1. **Scheduled Scaling**：時間點觸發(cron)、固定規律(上班時間)、設定簡單
    2. **Predictive Scaling**：ML 預測流量、不規則波動、需歷史數據
    3. **關鍵判斷**：8:50 先開機器 → 9:00 用戶來時已 ready（Scheduled 優勢）
12. ★★ **Predictive Scaling 致命限制**：異質實例容量計算錯誤
    1. **核心問題**：假設**實例同質化**(homogenous)，題目說 different instance types and sizes → 會算錯容量
    2. **錯誤邏輯**：預測需要 100 單位容量，當前 10 台機器 → 誤以為每台 10 單位 → 建議擴展數量不準
    3. **實例場景**：5台 t3.large (各4單位) + 5台 t3.xlarge (各12單位) = 80單位，但 Predictive 誤判為每台8單位
13. ★★ **Aurora Auto Scaling vs Global Database** 選擇邏輯
    1. **Auto Scaling 適用**：單一區域讀取擴展、突發流量處理、read replica 跟不上流量
    2. **Global Database 適用**：跨區域災難復原、全球用戶低延遲讀取、多區域部署
    3. **關鍵判斷**：題目有提「全球用戶」、「跨區域」、「災難復原」嗎？**沒有** → Auto Scaling
    4. Hence, the correct answer is: Use automatic scaling for the Aurora read replica using Aurora Auto Scaling.
14. ★ **Aurora 流量高峰應對策略**判斷技巧
    1. **看到關鍵字**：peak periods、fluctuating workload → 需要彈性擴展
    2. **確認範圍**：題目有提跨區域嗎？沒有 → Auto Scaling；有 → Global Database
    3. **記憶口訣**：Auto Scaling = 應付**流量高峰**；Global Database = 服務**全球用戶**
15. 😇 **Aurora 讀取擴展的完整選項**比較
    1. **Aurora Auto Scaling**：自動管理 read replica 數量、基於 CPU/連線數等指標（最適合突發流量）
    2. **手動增加 read replica**：最多 15 個 read replica、無法應對突發流量
    3. **Aurora Global Database**：跨區域複製、適合全球業務
    4. **Aurora Serverless v2**：連 writer 也能自動擴展、適合不可預測的極端波動
16. ★ **Elastic Beanstalk for .NET** 企業應用遷移策略
    1. **適用場景**：deploy、manage、scale **ASP.NET web applications**，使用 IIS 的 web application
    2. **遷移組合**：Oracle DB → **RDS for Oracle** (Multi-AZ) + **.NET app** → **Elastic Beanstalk** (Multi-AZ)
17. ★★ **MGN (Migration Service) vs Managed Services** 選擇原則
    1. **MGN 適合場景**：大規模批次遷移（數百台 VM）、遷移後逐步現代化、沒有對應 AWS 託管服務
    2. **本題不適用原因**：只有 1 個資料庫 + 1 個應用、Oracle 有 RDS 版本、.NET 有 Beanstalk
    3. **考試核心**：偏好 **managed service over self-managed**，除非題目明確限制
18. ★★ **MGN vs DMS** 資料庫遷移工具選擇
    1. **錯誤選項分析**：Use **AWS MGN** to migrate Oracle database server to **EC2 instance** (不正確)
    2. **正確做法**：**Amazon RDS** 支援標準 Oracle databases，應使用 **AWS DMS** 進行資料庫遷移
    3. **核心差異**：MGN = 整台伺服器遷移；DMS = 專門的資料庫遷移工具
19. ★★ **AWS Transfer Family** 檔案傳輸服務核心概念
    1. **服務定位**：**託管的檔案傳輸服務**
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
20. ★ **Transfer Family vs 其他服務**選擇邏輯
    1. **Transfer Family 適用場景**
       - 合作夥伴用 SFTP 傳檔
       - legacy file transfer workflow
       - B2B 檔案交換
    2. **其他服務選擇**：根據不同需求選擇對應服務
       - 應用程式用 SDK 上傳 → 直接用 S3 API
       - 大量資料遷移 → DataSync / Snow Family
       - 資料庫遷移 → DMS
    3. **考試陷阱**：避免選擇自建解決方案
       - 題目說「partners use SFTP」→ 別想用 **EC2 自建 SFTP server**
       - 優先考慮託管服務而非自建服務
21. ★★ **FSx for Windows vs NetApp ONTAP** 儲存協定差異
    1. **協定支援差異**
       - **FSx for Windows**：僅支援 **SMB (檔案層級)**
       - **FSx for NetApp ONTAP**：支援 **SMB + iSCSI (區塊層級)**⭐️
    2. **適用場景**：FSx for Windows 限於檔案共享、使用者目錄；FSx for NetApp ONTAP 適合區塊儲存 + 檔案儲存
    3. **交易應用支援**：FSx for Windows ❌ 不適合；FSx for NetApp ONTAP ✅ 適合
22. ★★ **交易系統需要 block storage** 的技術原因
    1. **效能要求**：**sub-millisecond latency**（檔案系統無法達成）
    2. **存取方式**：透過 **iSCSI 協定**像本地磁碟一樣直接操作
    3. **I/O 最佳化**：交易系統對 I/O 效能極度敏感，需要區塊層級存取
    4. **關鍵限制**：FSx for Windows File Server 只提供 SMB 檔案共享（**檔案層級存取**），不符合交易應用效能需求
23. ★ **FSx 系列選擇陷阱與記憶技巧**
    1. **考試陷阱識別**：看到 "Windows" + "high performance" 需求，別被 "Windows File Server" 名字誤導
    2. **判斷關鍵字**："trading application" + "block storage" → 需要 **iSCSI 協定** → 只有 **FSx for NetApp ONTAP**
    3. **服務定位記憶**：FSx for Windows = **檔案共享專用**（SharePoint、Home Directories）；FSx for NetApp ONTAP = **全功能儲存**（檔案 + 區塊）
24. ★★ **FSx 服務選擇陷阱**：Windows 平台需求分析
    1. **錯誤判斷**："Windows" ≠ 一定選 FSx for Windows
    2. **正確分析**：看需求是 **block** 還是 **file**
    3. **Trading/Database**：需要 **NetApp ONTAP (iSCSI)** 而非 Windows File Server
25. ★★ **共享存取陷阱**：跨平台儲存選擇邏輯
    1. **錯誤判斷**："共享存取" ≠ 一定是 EFS
    2. **正確選擇**：**Windows** → FSx for Windows；**Linux** → EFS
    3. **跨 AZ 區塊儲存**：只有 **FSx for NetApp ONTAP** 支援⭐️
    4. **EBS 限制**：不支援跨 AZ 共享
26. 😇 **儲存類型記憶口訣**
    1. **Block**：資料庫⭐️/交易系統 = **iSCSI**
    2. **File**：檔案共享/使用者目錄 = **SMB/NFS**
    3. **Object**：其它都能用 = **S3**
27. ★★ **IAM DB Authentication** EC2 安全連接 RDS 解決方案
    1. **題目關鍵**：如何讓 EC2 安全地連接 RDS
    2. **核心概念**：用 **IAM role** 取代傳統的帳號密碼登入資料庫
    3. **傳統方式問題**：EC2 → (username/password) → RDS ❌ 密碼外洩風險
    4. **IAM 方式優勢**：EC2 → (temporary token from IAM role) → RDS ✅ 無需管理密碼
28. ★ **IAM DB Authentication 三大優勢**
    1. **加密傳輸**：強制使用 **SSL/TLS**
    2. **集中管理**：用 IAM 統一控制誰能存取資料庫，不用在每個 RDS instance 上管理帳密
    3. **EC2 無需密碼**：直接用 EC2 的 **instance profile** 拿 token，**15 分鐘有效期**後自動失效
29. ★★ **IAM DB Authentication 設定流程**：RDS 功能配置要點
    1. **服務歸屬**：**RDS 的功能**，不是 EC2 的功能
    2. **RDS 端設定**：RDS Console → Database → Modify → 勾選 "Password and IAM database authentication"
    3. **EC2 端權限**：IAM role 需要 **rds-db:connect** 權限，指向特定 RDS resource ARN
    4. **資料庫內部**：建立對應的 IAM user (如: `CREATE USER iam_user; GRANT rds_iam TO iam_user;`)
30. ★ **IAM DB Authentication 完整流程**
    1. **步驟一**：RDS 啟用 IAM Authentication（在 RDS console）⭐️
    2. **步驟二**：EC2 附加 IAM role（有 rds-db:connect 權限，在 IAM console）⭐️
    3. **步驟三**：在資料庫內建立 IAM user（用 SQL）
    4. **步驟四**：程式用 boto3 拿 token 連線（在 code 中）
31. ★★ **S3 Access Points** 多應用權限管理解決方案
    1. **問題場景**：一個 S3 bucket 被多個應用/團隊共用，每個都需要不同權限，bucket policy 會變得超複雜
    2. **解決方案**：Access Point = bucket 的**命名入口**，每個入口有自己的權限政策
    3. **架構設計**
       - Application A → Access Point A (read-only)
       - Application B → Access Point B (write)
       - Application C → Access Point C (VPC-only)
       - 所有 Access Points → Same S3 Bucket
32. ★★ **S3 Access Points** 權限管理優勢
    1. **簡化權限管理**：避免寫超長的 bucket policy（❌ 難維護），每個 app 有自己的 policy（✅ 清楚）
    2. **關鍵字識別**："multiple applications with different permissions" → S3 Access Points
    3. **權限協同機制**：最終權限 = **Access Point Policy AND Bucket Policy**（兩者都要允許才行）
33. ★ **VPC-only Access Points** 內網安全存取
    1. **用途**：強制流量只能從特定 VPC 進來，不走公網
    2. **關鍵字**："restrict S3 access to private network" / "VPC endpoint"
    3. **考試重點**：內部應用安全存取 S3 的首選方案
34. 😇 **S3 Access Points** 典型考題場景
    1. **題目模式**："公司有多個部門共用 S3 bucket，財務部只能讀 `/finance/*`，工程部能讀寫 `/engineering/*`，如何簡化管理？"
    2. **標準答案**：建立兩個 Access Points，各自設定不同的 prefix 和權限，比單一 bucket policy 更好維護
    3. **記憶要點**
       - Access Point = **bucket 的別名 + 專屬權限**
       - 主要用於**多租戶/多應用**場景
       - 可設定 **VPC-only** 強化安全
35. ★★ **EventBridge** 關鍵字識別與考試重點
    1. **典型關鍵字**："when X happens, do Y"、"automatically trigger"、"event-driven"
    2. **常考場景**
       - S3 事件觸發 Lambda/ECS/Step Functions
       - 定時任務 (cron 排程)
       - 跨服務整合 (EC2 狀態變化 → SNS 通知)
36. ★★ **EventBridge vs S3 Event Notification** 目標服務差異
    1. **S3 Event Notification**：只能觸發 **Lambda/SQS/SNS**（限制較多）
    2. **EventBridge**：可觸發**更多目標服務** (ECS/Step Functions/跨帳號...)
    3. **關鍵記憶**：題目要觸發 **ECS task** → 用 EventBridge，不是 S3 Event Notification
37. ★★ **KMS Custom Key Store + CloudHSM** 完全掌控加密金鑰解決方案
    1. **題目關鍵需求**
       - **完全控制金鑰** (full control over encryption keys)
       - **立即移除金鑰** (immediately remove key material from KMS)
       - **獨立稽核** (audit independently of CloudTrail)
    2. **一般 KMS 的限制**：金鑰存在 AWS 的 HSM 中（多租戶共享）、刪除金鑰有 7-30 天等待期、稽核只能靠 CloudTrail
38. ★★ **Custom Key Store 核心架構與運作原理**
    1. **架構設計**：KMS Custom Key Store → 關聯你自己的 CloudHSM cluster → 金鑰材料「只」存在你的 HSM 中 → 直接控制 HSM 可立即移除金鑰
    2. **實際運作流程**
       - 創建 KMS key 時，金鑰材料生成在「你的 CloudHSM」
       - 金鑰永不以明文離開你的 HSM
       - 所有加密操作都在你的 HSM 上執行
       - 可直接管理 CloudHSM → 立即刪除金鑰
39. ★ **Custom Key Store 考試判斷關鍵字**
    1. **觸發關鍵字**
       - "full control over keys"
       - "immediately remove"（不是 schedule deletion）
       - "audit independently"（不只靠 CloudTrail）
    2. **其他識別詞**
       - "dedicated HSM"
       - "single-tenant"
       - "compliance" + "hardware control"
40. ★★ **Aurora Global Database** 跨區域資料庫複製解決方案
    1. **技術特性**：使用 **storage-based 技術**實現 < 1 秒的資料同步延遲
    2. **災難恢復優勢**：**RPO < 1 秒**（幾乎零資料遺失）、**RTO < 1 分鐘**（快速故障切換）
    3. **適用場景**：全球化應用需要各地低延遲讀取、對區域級災難 (region-wide outage) 有嚴格容錯要求
    4. **考試關鍵字**："global"、"cross-region DR"、"RTO/RPO 極致要求"
41. ★★ **AWS RAM vs Control Tower** 跨帳戶管理服務差異
    1. **AWS RAM (Resource Access Manager)**：**專門用途**跨帳戶資源共享、**可共享**Transit Gateway、子網路、Route 53 Resolver 規則、License Manager 配置等具體資源、**使用場景**多帳戶環境下共享特定 AWS 資源
    2. **AWS Control Tower**：**專門用途**多帳戶治理與管理、**核心功能**建立 landing zone、設定 guardrails(護欄政策)、帳戶自動化配置、**使用場景**大規模多帳戶的集中管理與治理
42. ★★ **題目陷阱分析**：Control Tower vs RAM 選擇邏輯
    1. **題目關鍵字**："centrally manages" + "share resources"
    2. **核心差別**：Control Tower 負責**管理帳戶本身**，但不負責**共享具體資源**
    3. **判斷原則**：題目明確提到要共享 Transit Gateway、License Manager、Route 53 Resolver 規則 → 這正是 **RAM 的專屬功能**
    4. **類比記憶**：Control Tower 像是「帳戶管理員」，RAM 才是「資源共享平台」
43. 😇 **RAM vs Control Tower** 記憶技巧與判斷法則
    1. **RAM** = Resource **Access** Manager → 想到「訪問」→ 跨帳戶**共享資源**
    2. **Control Tower** = **Control**(控制) → 帳戶層級的**治理與政策**
    3. **快速判斷**：看到「share resources such as Transit Gateway, License Manager」→ 直接選 **RAM**
44. ★★ **Organizations vs RAM** 核心功能區別
    1. **AWS Organizations**：**管理帳戶**建立、組織、套用政策、**不共享資源**只管理帳戶結構與權限政策
    2. **AWS RAM**：**共享資源**Transit Gateway、子網路等實體資源、**不管帳戶**只負責資源共享機制
45. 😇 **實務組合使用**：大型企業多帳戶管理策略
    1. **同時使用**：Organizations 管理帳戶架構（OU、SCP 政策）+ RAM 在這些帳戶間共享資源
    2. **分工明確**：Organizations = 帳戶治理；RAM = 資源共享
46. ★★ **企業身份聯合 (Enterprise Identity Federation)** 大量用戶存取解決方案
    1. **題目場景**：企業現有 AD/LDAP 使用者需存取 AWS S3，而不需要為每個人建立 IAM user
    2. **核心挑戰**：1200 個使用者 → 避免建立 1200 個 IAM user
47. ★★ **Federation + STS** 身份轉換機制
    1. **Federation Proxy/Identity Provider + STS**：**目的**將企業 AD/LDAP 身份轉換成 AWS 臨時憑證
    2. **運作流程**：使用者登入 AD → Identity Provider 驗證 → **STS 發放臨時 token**
    3. **關鍵優勢**：STS (Security Token Service) 產生**臨時憑證**，避免建立大量 IAM user
48. ★★ **IAM Role + Policy** 權限控制機制
    1. **IAM Role**：定義 STS 臨時憑證要扮演的角色
    2. **IAM Policy**：限制每個使用者只能存取自己的 folder
    3. **動態權限**：Policy 使用 `${aws:username}` 變數動態對應資料夾
49. ★ **Federation 雙元件設計**原理與記憶技巧
    1. **為什麼需要兩個元件**：單獨 STS 只能產生臨時憑證，但沒定義**權限範圍** → 需要 IAM Role + Policy 告訴 AWS「這個臨時憑證能做什麼」
    2. **記憶口訣**
       - **企業現有帳號系統 + AWS** → Federation + STS
       - **限制個別使用者權限** → IAM Role + Policy with variables
       - **大量使用者** → 絕對不是建立 1200 個 IAM user
50. ★★ **過度設計陷阱**：CloudFront + 504 錯誤處理
    1. **核心錯誤**：被 "global users" 誤導，題目真正在測**你會不會過度設計(over-engineering)**
    2. **考試重點**：看似合理的大方案(Multi-region) ❌ vs 直接解決問題的小方案(**Origin Failover**) ✅
51. ★★ **Multi-region 方案錯誤分析**
    1. **成本高**：多區域部署、跨區資料同步、維護複雜度
    2. **沒必要**：CloudFront 已經是全球 CDN，用戶延遲問題它已解決
    3. **沒解決 504**：origin 掛了，換區域一樣掛
52. ★★ **考試陷阱模式識別**：關鍵字判斷技巧
    1. **"cost-effective"** → 排除複雜方案
    2. **"CloudFront 已部署"** → 不需要 multi-region
    3. **"504 錯誤"** → origin 可用性問題，不是地理位置問題
53. ★ **CloudFront 效能問題**診斷與記憶技巧
    1. **核心概念**：**CloudFront 已存在 = 全球分發已搞定**
    2. **問題判斷**：login 慢 → 可能是 origin 慢；504 錯誤 → 一定是 origin 問題
    3. **診斷順序**：先看是不是 origin 端問題 → 再考慮是不是快取策略問題 → 最後才考慮架構大改(multi-region)
54. ★★ **Amazon Inspector** 資源安全掃描工具
    1. **功能定位**：掃描**你的資源**找漏洞
    2. **檢查範圍**：EC2/container 的安全弱點、網路暴露風險、給你修復建議
    3. **核心重點**：**不是**拿來下載 AWS 合規文件的
55. ★★ **AWS Artifact** 合規文件中心
    1. **服務定位**：下載 **AWS 的**合規證明文件
    2. **提供內容**：SOC 報告、PCI DSS 證書、ISO 認證、HIPAA BAA 協議、各種合規文件
    3. **使用特色**：**完全免費，隨時下載**
    4. **考試關鍵字**："compliance reports" → Artifact、"security assessment" → Inspector、"regulatory documents" → Artifact、"vulnerability scan" → Inspector
    5. **記憶技巧**：Inspector → 檢查「你的」資源；Artifact → 下載「AWS 的」合規證明
56. ★ **ALB 支援協定**：現代化負載均衡器協定支援
    1. **支援協定**：**HTTP/1.1**、**HTTP/2**、**gRPC**
    2. **關鍵優勢**：支援現代微服務架構（gRPC）、提升效能（HTTP/2 多路復用）
57. ★★ **Route 53 健康檢查**：端點可用性監控機制
    1. **檢查方式**：發送 **HTTP/HTTPS/TCP** 請求到指定的 IP 位址和連接埠
    2. **網路設定要求**：路由器和防火牆規則必須允許來自 **Route 53 健康檢查器 IP** 的入站流量
58. 😇 **Route 53 健康檢查網路配置**：確保檢查正常運作
    1. **NACL 設定**：設定網路存取控制清單允許 Route 53 請求
    2. **路由表配置**：確保路由表允許健康檢查流量到達端點
    3. **Evaluate Target Health**：設定為 **Yes** 啟用目標健康評估
59. ★★ **Lambda 呼叫方式**：同步 vs 非同步處理機制
    1. **Synchronous（同步）**：API Gateway、ALB 直接呼叫，等待回應
    2. **Asynchronous（非同步）**：S3、SNS 觸發後就離開，Lambda 自己處理
60. ★★ **Kinesis + Lambda 特殊機制**：Event Source Mapping 架構
    1. **核心機制**：Lambda 使用 **Event Source Mapping** 主動 poll Kinesis stream
    2. **資料流向**：不是 Kinesis push 給 Lambda，是 **Lambda pull from Kinesis**
    3. **自動管理**：Lambda 自動管理 **checkpoint** 和 **retry**
61. ★ **Kinesis + Lambda 架構優勢**：高效能非同步處理
    1. **立即回應**：Kinesis 收到資料就回 success，不用等處理完成
    2. **自動擴展**：Lambda 根據 **shard 數量和資料量**自動調整
    3. **成本優化**：只付 Kinesis shard 費用 + Lambda 執行時間
    4. **處理流程**：User upload → Kinesis Stream（立即回應）→ Lambda poll（非同步處理）→ S3 儲存
62. ★★ **ECS + SQS Batch Job 架構**：容器化批次處理解決方案
    1. **架構流程**：S3 upload → **S3 Event** → **SQS Queue** → **ECS Task (Docker)** → 處理後上傳 S3
    2. **SQS 中介層優勢**：解耦 S3 event 和 ECS task、根據 **queue 訊息數量**調整 ECS task 數量、訊息不會遺失（可設定 retention 時間）、處理失敗的訊息會回到 queue
63. 😇 **AWS DMS SSL 加密**：端點安全連接配置
    1. **加密方式**：使用 **Secure Sockets Layer (SSL)** 加密來源和目標端點連接
    2. **設定方法**：透過 **AWS DMS Management Console** 或 **AWS DMS API** 分配憑證給端點
    3. **憑證管理**：可使用 **AWS DMS console** 管理憑證
64. 👀 **DMS Certificate 管理功能**：基本憑證儲存機制
    1. **核心功能**：上傳 certificate 到 DMS、指定給 endpoint 用於 SSL 連線、驗證 source/target 身份
    2. **服務定位**：**不是完整的 PKI 管理系統**，更像是讓你把 cert 放進去，DMS 幫你在連線時使用
    3. **實務應用**：On-premises DB 需要 SSL 連線時上傳 **CA cert** 到 DMS；RDS 預設有 AWS 提供的 cert
    4. **考試重點**：知道「**DMS 支援 SSL 加密傳輸**」即可，不會深入考憑證管理細節
65. 😇 **AWS Directory Service 功能定位**：使用者目錄管理服務
    1. **主要功能**：管理使用者目錄，例如 **Microsoft Active Directory**
    2. **服務限制**：**不適合**直接用於多帳戶身份驗證目的
    3. **記憶要點**：Directory Service = 管理目錄，不是多帳戶認證解決方案
66. ★★ **多帳戶身份認證解決方案**：IAM Identity Center + AD Connector 組合
    1. **關鍵字識別**："existing directory service" → 已有 AD/LDAP；"user authentication" → SSO 需求
    2. **正確答案組合**：**IAM Identity Center**（舊名 AWS SSO）負責中央化管理多帳號存取 + **AD Connector** 連接現有 AD（不用複製用戶到 AWS）
    3. **考試重點**：多帳戶環境下的身份管理，優先考慮現有身份來源整合
67. ★★ **Route 53 + S3 靜態網站**：前置條件配置
    1. **必要條件一**：**S3 bucket 名稱**必須與網域名稱完全相同（如 `www.example.com` bucket 對應 `www.example.com` 網域）
    2. **必要條件二**：S3 bucket 必須**啟用靜態網站託管功能**（開啟 "Static website hosting" + 設定 index document）
    3. **常見錯誤**：S3 bucket **不需要**與 hosted zone 在同一個 region（Route 53 是全球服務）
    4. **記憶技巧**：「**名稱對應 + 靜態開關**」- 這兩個是必要條件，region 不重要
68. 😇 **AWS Storage Gateway vs S3 Glacier 成本陷阱**：備份儲存類別選擇
    1. **錯誤選項分析**：使用 **AWS Storage Gateway** 備份資料到 **S3 Glacier Flexible Retrieval** 雖然有效，但成本通常比直接備份到 **Glacier Deep Archive** 更昂貴
    2. **成本考量**：Glacier Deep Archive 比 Glacier Flexible Retrieval 更便宜（適合長期封存）
    3. **考試重點**：備份解決方案要考慮**成本效益**，選擇最便宜的合適選項
69. ★ **SNS Fan-out 架構錯誤選項分析**：單一 topic vs 多 topic 設計
    1. **錯誤想法**：為每種訊息類型建立獨立的 SNS topic + 對應的 SQS queue
    2. **正確架構**：**單一 SNS topic** + **多個 SQS queue** + **Filter Policy** 實現訊息分流
    3. **Filter Policy 機制**：在 SQS subscription 設定過濾條件，只接收符合條件的訊息，避免所有 subscriber 收到全部訊息
    4. 沒有過濾的話，就是典型的 fan-out 架構——所有 subscriber 都會收到所有訊息
70. ★★ **ECS 容器編排服務定位**：AWS 特色的容器管理解決方案
    1. **核心定位**：AWS 原生容器編排服務，深度整合 AWS 生態系統
    2. **與競爭對手對比**
       - **Kubernetes (EKS)**：業界標準但複雜
       - **ECS**：AWS 簡化版但整合性強
       - **Docker Swarm**：已式微不考
71. ★★ **ECS 的 AWS 原生整合優勢**
    1. **身份權限整合**：直接連接 IAM Role (Task Role/Execution Role)
    2. **監控日誌整合**：自動整合 CloudWatch Logs/Metrics
    3. **配置管理整合**：原生支援 Secrets Manager/Parameter Store
    4. **網路整合**：直接整合 ALB/NLB Target Groups、VPC/Security Groups
72. ★★ **ECS vs EKS 管理模式差異**
    1. **ECS 優勢**：AWS 全管控制平面，使用者只需管理 tasks
    2. **EKS 限制**：需要管理 Kubernetes control plane 或付費給 EKS
    3. **學習曲線**：ECS 平緩，EKS 需要 Kubernetes 專業知識
73. ★★ **ECS 兩種運行模式選擇**
    1. **EC2 Launch Type**：使用者管理底層 EC2 實例，適合需要控制底層資源
    2. **Fargate**：完全 serverless，連 EC2 都不用管，適合簡化營運
74. ★★ **容器服務考試選擇邏輯**
    1. **ECS 適用關鍵字**："AWS-native"、"tight AWS integration"
    2. **EKS 適用關鍵字**："Kubernetes workloads"、"migrate from on-premises K8s"
    3. **Fargate 適用關鍵字**："serverless containers"、"no cluster management"
75. ★ **容器服務記憶技巧**
    1. **ECS = Easy Container Service**（AWS 簡化版）
    2. **EKS = Elastic Kubernetes Service**（業界標準）
    3. **選擇原則**：優先考慮 AWS 原生整合需求，再考慮 Kubernetes 相容性需求
76. ★★ **容器中不運行資料庫的技術原因**
    1. **狀態管理複雜**：需要 StatefulSets + Persistent Volumes，增加架構複雜度
    2. **資料一致性風險**：pod 重啟可能遺失資料，無法保證資料安全性
    3. **備份/恢復困難**：需自己實作備份機制，缺乏自動化保護
    4. **效能不穩定**：容器 I/O 效能不如實體機/VM，影響資料庫效能
77. ★★ **AWS 容器與資料庫最佳實踐分離原則**
    1. **Container (ECS/EKS)**：專門運行**無狀態服務**（應用程式、API、微服務）
    2. **Managed Service**：專門處理**有狀態資源**（RDS、Aurora、ElastiCache）
    3. **關鍵優勢**：各司其職，容器負責運算，託管服務負責資料持久化
78. ★★ **容器資料庫考試陷阱識別**
    1. **正確選項**：RDS/Aurora (managed)、DynamoDB (serverless)
    2. **錯誤選項**：「在 ECS/EKS 跑 MySQL container」（絕不會是正確答案）
    3. **記憶口訣**：**容器跑 app，資料庫用 managed service**
79. ★★ DynamoDB 計費模式選擇
    1. **On-Demand 模式**：按實際請求付費、自動擴展、無需容量規劃（**純 serverless**）
    2. **Provisioned Capacity 模式**：預設 **RCU/WCU**、可啟用 Auto Scaling、成本可預測
80. ★★ DynamoDB RCU/WCU 考試重點
    1. **限定關係**：RCU/WCU **只和 Provisioned Capacity** 有關
    2. **考試規律**：題目提到 RCU/WCU，答案必是 **Provisioned Capacity**
    3. **On-Demand 特性**：無需設定容量單位，按實際用量計費
81. ★★ CloudHSM 密鑰恢復限制
    1. **AWS 無法恢復密鑰**：遺失 HSM 憑證 = 密鑰永久遺失
    2. **考試陷阱**：題目問「如何恢復？」→ 答案：**無法恢復**
82. ★★ CloudHSM 生產環境部署要求
    1. **最少 2 個 HSM**：分散不同 AZ
    2. **避免單點故障**：防止單一 AZ 故障導致密鑰遺失
    3. **考試記憶口訣**：CloudHSM = **高安全性換來高責任**
83. ★★ Direct Connect Virtual Interface (VIF) 類型選擇
    1. **Private VIF**：存取 **VPC 內部私有資源**
    2. **Public VIF**：存取 **AWS 公開服務**（S3、DynamoDB 等）
    3. **考試關鍵字判斷**："on-premises to VPC" + "dedicated connection" → **Direct Connect + Private VIF**
84. ★ Direct Connect VIF 使用場景記憶技巧
    1. **Private VIF = VPC 內部**：EC2、RDS、內部應用程式
    2. **Public VIF = AWS 公開服務**：S3、DynamoDB、公開 API
    3. **關鍵差別**：Private VIF 訪問 **VPC 內部**，Public VIF 訪問 **AWS 公開服務**
85. ★★ **ENA vs EFA** HPC 網路需求與考試陷阱
    1. **題目關鍵**：HPC 高效能運算的網路需求 + **Windows servers**
    2. **真正考點**：ENA vs EFA 的差異，以及 **Windows 的限制**
    3. **邏輯推演**：題目要求「higher bandwidth + higher PPS + lower latency」→ 直覺想到 EFA（因為 HPC 場景）→ **但關鍵字是 Windows servers** → EFA 的 OS-bypass 功能不支援 Windows → 只能退而求其次選 ENA
86. ★★ **ENA vs EFA 技術差異**表格比較
    1. **ENA**：支援 Windows + Linux、核心優勢 SR-IOV 最高 100 Gbps、適用一般 HPC 大頻寬需求
    2. **EFA**：**僅 Linux**、核心優勢 ENA + OS-bypass、適用極致低延遲（MPI、ML）
87. ★ **ENA vs EFA 選擇邏輯**與記憶技巧
    1. **記憶口訣**：**EFA = ENA + OS-bypass = Linux Only**
    2. **判斷規則**：Windows → 直接排除 EFA，選 ENA；Linux + HPC/ML → 考慮 EFA；一般高頻寬需求 → ENA 就夠
88. ★★ **SAA 典型陷阱設計**：情境 + 限制條件組合題
    1. **陷阱設計**：AWS 故意在 HPC 情境下放 **Windows**，讓你以為要選「最強的 EFA」，但實際上 Windows 根本用不了 EFA 的核心功能（OS-bypass）
    2. **考試重點**：看似完美的高階方案，但藏了一個限制條件讓它不可行
    3. **應對策略**：看到 HPC 不要反射性選 EFA → 先檢查 OS；Windows 在 AWS 的限制比 Linux 多 → 遇到 Windows 要多留意
89. ★★ **Kinesis Data Streams** 實時日誌處理架構解決方案
    1. **題目關鍵**：實時分析 + 多來源日誌合併 + 12 小時內數據回溯
    2. **核心需求分析**：需要 streaming 服務 + 接收多個 stream + 短期數據保留
90. ★★ **Kinesis 技術優勢**：完美符合實時日誌處理需求
    1. **實時數據攝取和處理**：支援高吞吐量數據流
    2. **數據保留特性**：預設保留數據 **24 小時**（可延長到 365 天）
    3. **多消費者支援**：支援多消費者同時讀取同一份數據
    4. **數據重播功能**：可以「重播」過去 12 小時的數據做驗證
91. ★★ **Storage Gateway Cached vs Stored Volumes** 存取模式選擇
    1. **題目關鍵**：low latency access to **frequently accessed data subsets** locally
    2. **Cached Volumes 優勢**：只有**熱點資料**快取在本地，適合部分資料集的低延遲存取
    3. **Stored Volumes 限制**：需要低延遲存取**整個資料集**，不適合只存取部分資料的場景
92. ★★ **CloudTrail** AWS API 操作記錄與審計服務
    1. **服務定位**：專門記錄 **AWS API 呼叫歷史**
    2. **記錄範圍**：誰做了什麼操作、何時做的、對哪個資源操作、操作結果
93. ★ **CloudTrail 加密機制**：預設安全保護配置
    1. **預設加密**：**S3 SSE 加密**（免費）
    2. **進階加密**：**AWS KMS 加密**（更嚴格的金鑰管理）
    3. **考試重點**：CloudTrail **預設就有加密**，不需要額外設定
94. 😇 **CloudTrail 進階功能**：日誌檔案管理與通知設定
    1. **儲存管理**：可在 S3 bucket 中長期儲存日誌檔案、定義 **S3 lifecycle 規則**自動封存或刪除日誌檔案
    2. **通知機制**：設定 **Amazon SNS 通知**，在日誌檔案傳送和驗證時接收通知
95. ★★ **Security Groups vs NACL** SSH 連線配置差異
    1. **Security Groups**：只需設定 **Inbound Rule** 允許 SSH 流量（**stateful**）
    2. **NACL**：需設定 **Inbound 和 Outbound Rules** 都允許 SSH 流量（**stateless**）
96. ★ **Storage Gateway Cached Volumes** 混合雲儲存最佳化解決方案
    1. **資料儲存架構**：主要資料存放在 **Amazon S3**，僅在 on-premises 保留 **熱點資料快取**
    2. **成本優勢**：大幅節省主要儲存成本、最小化 on-premises 儲存擴展需求
    3. **效能保證**：對**常用資料子集**保持**低延遲存取**，適合部分資料集的高頻存取場景
97. 😇 **AppSync Pipeline Resolvers** 多資料來源聚合解決方案
    1. **服務定位**：**託管式 GraphQL API 服務**，適合需要聚合多個 DynamoDB tables 的資料
    2. **核心優勢**：**單次 API 呼叫**讀取多個資料來源、**伺服器端資料聚合**、避免多次往返網路延遲
    3. **考試關鍵字**："retrieve from multiple DynamoDB tables" + "single API call" + "serverless data aggregation" → AppSync Pipeline Resolvers
    4. **支援資料來源**：DynamoDB（最常見）、RDS/Aurora、Lambda（自訂邏輯）、HTTP endpoints、OpenSearch、EventBridge
    5. **Pipeline 混合使用**：可在單次 GraphQL request 中組合多種資料來源（如：DynamoDB 讀取 → Lambda 處理 → Aurora 寫入），題目聚焦 **DynamoDB** 因其 **serverless + NoSQL** 的典型組合特性
98. ★★ **Route 53 Alias A Record vs CNAME Alias** Zone Apex 限制解析
    1. **CNAME 的 DNS 限制**：根據 RFC 規範，Zone Apex 不能有 CNAME，因為 CNAME 會「獨佔」該名稱，與必須存在的 SOA、NS 記錄衝突
    2. **Route 53 Alias 的解法**：AWS 發明了 Alias Record，它「看起來」像 A Record，但背後會自動解析目標（如 ALB）的 IP，這繞過了 CNAME 的限制
    3. **ALB 沒有固定 IP**：不能直接用傳統 A Record 指定 IP，必須用 Alias 讓 Route 53 動態解析
99. ★ **Zone Apex DNS 考試記憶點**：Load Balancer 指向規則
    1. **考試核心規則**：**Zone Apex + Load Balancer = A Record Alias**
    2. **關鍵字識別**：看到「apex」或「裸域名」指向 ALB/NLB/CloudFront → 答案就是 **Alias A Record**，永遠不會是 CNAME
100. 😇 **Alias A Record 實際設定步驟**：Route 53 Console 操作細節
     1. **Name（記錄名稱）**：`tutorialsdojo.com`（空白或 `@`，取決於 DNS 介面）
     2. **Type**：A
     3. **Alias**：Yes
     4. **Alias Target（值）**：`elbtutorialsdojo123.us-east-1.elb.amazonaws.com`
101. 👀 **Route 53 Alias Target 選擇機制**：Console 便利功能
     1. **界面操作**：不會看到 IP，而是選擇 ALB 的 DNS name
     2. **下拉選單**：Route 53 Console 裡會有下拉選單讓你直接選 AWS 資源（ALB、CloudFront、S3 等），不用手打
     3. **背後運作**：Route 53 會在背後把這個 DNS name 解析成當下的 IP 回給查詢者
102. ★★ **Auto Scaling Cooldown Period** 抖動問題解決方案
     1. **問題情境**：Auto Scaling 在短時間內反覆 scale up/down（抖動），導致不穩定或成本浪費
     2. **解決方案**：延長 **cooldown period** + 提高 **CloudWatch 觸發閾值**
     3. **考試判斷點**：看到「頻繁 scaling」、「抖動」、「scale up/down 太快」→ 想到 cooldown period 太短
     4. **雙重調整策略**：提高 CloudWatch threshold 讓指標不要太敏感，兩者搭配才能穩定 scaling 行為

103. 👀 **AWS Service Quotas 監控架構**：事件驅動的配額檢查解決方案
     1. **核心流程**：Trusted Advisor (檢查配額) → EventBridge (捕捉事件) → SNS (發通知)
     2. **Lambda 角色**：每 24 小時定期 refresh Trusted Advisor 的配額檢查數據（因為不是即時更新）
     3. **關鍵字識別**：Service Quotas/Service Limits 監控 → AWS 推薦的「Quota Monitor」解決方案架構
104. 👀 **Service Quotas 監控元件功能**分析
     1. **Trusted Advisor**：配額檢查的資料來源
     2. **EventBridge**：事件捕捉與路由
     3. **SNS**：當配額接近上限時發送通知
     4. **Lambda**：自動化刷新或處理邏輯
105. 👀 **AWS Wavelength** 5G 邊緣運算解決方案
     1. **服務定位**：5G 邊緣運算，提供超低延遲應用
     2. **部署位置**：電信商機房內（不是一般 AWS Region）
     3. **目標場景**：AR/VR、即時遊戲、自駕車、工業 IoT
106. 👀 **Wavelength 考試策略**
     1. **出現機率**：極低，1-2 題最多
     2. **判斷關鍵字**：「5G」、「ultra-low latency」+「mobile network」、「edge of telecom network」
     3. **應對策略**：知道它存在、記住關鍵字就夠了，考試 CP 值太低
