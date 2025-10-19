# 25-IAM、CloudTrail、Organizations 與 Config

2025-10-19

## AWS SAA 考試重點彙整：IAM、CloudTrail、Organizations、Config

本摘要整理了與 AWS IAM 權限管理、CloudTrail 日誌、Organizations 帳戶管理，以及 AWS Config 相關的重要概念與考試常見陷阱，適合備考 AWS Solutions Architect Associate (SAA) 使用。

---

## 🔐 IAM (Identity and Access Management)

### IAM Policy 核心邏輯

* **Allow 和 Deny 規則**：IAM 會將所有策略綜合評估，最終決定允許或拒絕。

  * **明確 Deny > 允許 (Allow)**：若任何一條規則是明確 Deny，則操作一定會被拒絕。

> 📌 **考試提醒**：看到題目問到「哪一種設定可以確保某操作永遠無法執行」，答案很可能是使用明確的 `Deny`。

### IAM Conditions 條件判斷

* 可限制：來源 IP、時間範圍、使用 MFA、VPC、S3 Prefix 等。
* **用途**：在授權操作時加上額外限制。

### Permission Boundary 權限上限

* 是一種 **附加在 IAM 使用者或角色上** 的策略，用來限制他們最多能有的權限範圍。
* **只會限制權限，不會給予新權限**。
* 常用於：委派管理權限給開發者但想要設限。

> 📌 **考試提醒**：若提到「如何防止 IAM 使用者獲得超出預期的權限」，答案可能是使用 Permission Boundary。

### Resource-based Policy 資源型策略

| 特性     | IAM Policy  | Resource-based Policy   |
| -------- | ----------- | ----------------------- |
| 綁定位置 | 使用者/角色 | 資源本身 (如 S3 bucket) |
| 控管角度 | 身份為主    | 資源為主                |

> 📌 常見考題場景：跨帳號存取 S3 bucket，通常會考你 Resource-based Policy 的使用。

### IAM Role 與 Assume Role

* IAM Role 是一種「身份」，允許其他實體（如 EC2）**臨時獲得角色權限**。
* Assume Role 會讓使用者/服務 **暫時切換身份**，原本的 IAM 權限暫時失效。

---

## 📜 CloudTrail 與 CloudTrail Insights

### CloudTrail

* 作用：記錄 AWS API 呼叫，包括來自 Console、CLI、SDK 的操作。
* 可將記錄送往 S3 儲存，也可搭配 CloudWatch Logs 進行分析。

### CloudTrail Insights

* 功能：進階功能，**主動偵測異常 API 行為模式**（例如：突增的 StopInstances 請求）。
* 須啟用，會產生額外費用。

> 📌 考試提醒：若題目問「如何自動偵測 API 行為異常」，請選 CloudTrail Insights。

### 與 GuardDuty 比較

| 功能     | CloudTrail Insights | GuardDuty                          |
| -------- | ------------------- | ---------------------------------- |
| 分析對象 | API 活動異常行為    | 多來源威脅分析（含 VPC Flow Logs） |
| 用途     | 行為基準偏移偵測    | 威脅偵測與警報                     |

> 📌 CloudTrail Insights 專注在「行為異常」，GuardDuty 是全面安全偵測。

---

## 🏢 AWS Organizations

* 用來集中管理多個 AWS 帳戶：帳單合併、策略下放（SCP）。
* 可建立 Organizational Units (OUs) 組織結構。
* **Service Control Policy (SCP)**：設定 OU 或帳戶的最大權限範圍。

> 📌 考試常見陷阱：SCP 並不賦予權限，只是限制最大上限，還是需要 IAM Policy 才能真正執行操作。

---

## 📝 AWS Config

* 功能：紀錄 AWS 資源的「配置變更歷史」。
* 可自動偵測變更（例如：Security Group 被修改）。
* 可設定規則（Config Rules）來進行合規性評估。

> 📌 考試提醒：若題目問「如何查看資源配置是否合規、是否曾被變更」，請選 AWS Config。

---

## 📚 補充建議與記憶技巧

* **Deny 優先原則**：任何時候，只要出現明確 Deny，就算其他地方 Allow 也無效。
* **Role 與 Policy 的角度區分**：Role 給身份切換用，Policy 管權限給誰做什麼。
* **IAM = 誰能做什麼事在哪裡；Config = 資源狀態變了沒；CloudTrail = 誰做了什麼事；Organizations = 帳戶管理與權限框架。**

---

這份摘要若能熟記並理解，你在考試中遇到 90% 與權限、日誌、組織管理相關的題目時都能有明確的判斷依據。
