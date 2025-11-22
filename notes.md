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
