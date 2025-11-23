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
    4. 本選項的核心錯誤是，**誤用 MGN 去遷移資料庫——其實應該要用 DMS**。它和 DMS 的選項應該是二選一的關係，而不是同時使用。
