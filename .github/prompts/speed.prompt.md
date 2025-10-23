# AWS SAA 筆記精簡萃取 Prompt

你是 AWS 認證考試專家。請分析我的 SAA 學習筆記，生成「考前衝刺用」的精簡版本。

## 📋 輸入輸出設定
- **輸入來源**：`notes.md`（原始速記筆記）
- **輸出檔案**：`speed.md`（精簡萃取版本，同目錄）
- **處理方式**：逐行分析、合併同類、優先保留高頻考點

## 🎯 萃取標準

### 必須保留
1. **高頻服務**：EC2、S3、RDS、Lambda、VPC、IAM、CloudWatch、DynamoDB、ALB/NLB、ECS/EKS、Cognito
2. **易混淆對比**：
   - S3 儲存類別（Standard vs IA vs Glacier vs Intelligent-Tiering vs One Zone-IA）
   - RDS vs DynamoDB vs Aurora vs DocumentDB
   - ALB vs NLB vs CLB（Layer 4 vs Layer 7）
   - SG vs NACL（Stateful vs Stateless）
   - 加密類型（SSE-S3 vs SSE-KMS vs SSE-C vs Client-Side）
3. **關鍵數字**（務必正確）
   - 時間限制：Lambda 15min、RTO/RPO、Cooldown period 預設 300s、DynamoDB item 400KB、EBS snapshot S3、SQS visibility timeout 預設 30s、SQS Long Polling 最高 20s
   - 大小限制：DynamoDB item 400KB、SNS message 256KB、SQS message 256KB
   - 配額：EC2 On-Demand vCPU limit per region、EBS Multi-Attach 僅限 io1/io2、ENI per instance
4. **決策情境**：特定業務需求 → 推薦服務（必須有判斷邏輯）
5. **★★ 標記項目**：必考點，一定要保留

### 必須捨棄
- 冷門或邊界知識（如 Predictive Scaling 邊界假設）
- 逐步操作細節（UI 點擊、console 步驟）
- 重複的概念解釋（同一概念出現多次時只保留最精簡的）
- 出現頻率 < 2 次的服務（如 Neptune、Textract 除非涉及決策對比）
- 純英文原文（已有中文翻譯時刪除原文）

## 📝 輸出格式範本

### 單一服務模板
```
### [服務名稱] ⭐ (或 ⚠️)
**定義**：一句話說明核心用途
**使用場景**：
- 場景 1
- 場景 2
**關鍵限制**：
- 限制 1（單位）
- 限制 2（時間）
**易混淆點**：vs [相似服務]、常見誤解
```

### 對比模板（易混淆類）
```
| 對比項 | 選項A | 選項B | 選項C | 判斷依據 |
|--------|-------|-------|-------|---------|
| 場景 1 | ... | ... | ... | 關鍵詞 |
| 場景 2 | ... | ... | ... | 決策邏輯 |
```

### 關鍵數字參考表
```
| 服務 | 項目 | 數值 | 備註 |
|------|------|------|------|
| Lambda | 最長執行時間 | 15 分鐘 | 無法延長 |
```

## 🔍 精簡原則

### 保留形式
- **✅ 保留**：`★★ [核心概念]` → 直接成為 section
- **✅ 保留**：數字、限制、易錯點
- **✅ 保留**：決策判斷邏輯（「當...應選...」）
- **✅ 保留**：對比表格、架構組合（如 Multi-AZ + ASG + ELB）
- **✅ 保留**：符號標記（⭐️、⚠️、❌、✅）

### 刪除形式
- **❌ 刪除**：`👀 [資訊型]` 除非涉及常見誤解
- **❌ 刪除**：「已移除」項目
- **❌ 刪除**：重複編號的同一概念
- **❌ 刪除**：純 AWS 官方文案（除非是關鍵定義）

## 🎓 內容結構

### 建議組織順序
1. **核心服務**（按考試重要度）：IAM、VPC、EC2、ELB、Auto Scaling、RDS/Aurora/DynamoDB、S3、Lambda
2. **監控與合規**：CloudWatch、CloudTrail、GuardDuty、AWS Organizations
3. **進階主題**：災難恢復 (DR vs HA)、加密與安全、跨帳號設計
4. **易混淆清單**：對比表格集中放在最後

## ⚡ 特別要求
- **篇幅目標**：壓縮至原筆記的 25-35%
- **技術準確度**：必須 100% 正確，不能簡化到錯誤
- **語言**：繁體中文（技術術語保留英文原文）
- **符號使用**：
  - ⭐️ = 必考內容（考試高頻出現）
  - ⚠️ = 易錯內容（常見陷阱）
  - ❌ = 常見誤解
  - ✅ = 正確做法
- **表格校驗**：所有對比表格必須檢查行列對齊

## 🚫 常見誤區（避免）
- 不要把「預設值」當成「必須值」
- 不要忘記「跨 region vs 跨 AZ」的區別
- 不要混淆「同步複製」vs「非同步複製」
- 不要遺漏「IAM Policy + Resource Policy」的雙層檢查邏輯
- 不要把「功能限制」寫成「bug」

