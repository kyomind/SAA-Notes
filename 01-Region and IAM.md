# 01，Region and IAM

2025-10-02

以下是針對我們的對話內容整理出的學習筆記，並適度補充了考試中常見的重點，幫助你快速掌握 **SAA 必考概念**。

---

## 🌍 AWS 基礎架構核心概念

### Availability Zone (AZ)

* **一個 AZ 通常由多個資料中心組成**。
* 同一個 Region 內的 AZ **距離通常在幾十至幾百公里**，確保：
  * **低延遲** (對應到應用需求)
  * **容災能力** (避免單點故障)
* **常見考點**：服務是否跨 AZ 自動做冗餘？
  * **S3 / DynamoDB：有** → 天生多 AZ 複寫。
  * **RDS 單 AZ：沒有** → 若要高可用，需要 Multi-AZ。

### Region 與 Global Service
* 大部分服務都是 **Region 級別**。
* **Global Service（需記住！）**：
  * **IAM**（身份與權限管理）
  * **Route 53**（DNS）
  * **CloudFront**（CDN）

**考試必記重點**：

> 如果題目問「這個服務在哪個區域運作？」，遇到 IAM、Route 53、CloudFront → 答案是 **Global**。

---

## 🔐 IAM (Identity and Access Management)

### 為何重要？
* **IAM 幾乎是每個服務的基礎**。
* 考試中大量題目涉及「誰有權限做什麼」。
* 所以課程通常會 **先從 IAM 開始**。

### IAM 與帳單存取
* **Root 用戶** 預設能看帳單。
* 其他 IAM 使用者需要 **明確授權** 才能看到帳單。
* **考試重點**：安全原則 → **Least Privilege（最小必要權限）**。

> 實務中，如果你是一人管理帳號，可以給自己 IAM 用戶開帳單權限，避免經常使用 root 帳號。

### IAM 與 Identity Center
* **SAA 考試重點是 IAM，不是 Identity Center**。
* Identity Center 偏向企業多帳號管理（進階層級，考試較少出現）。

### Policy vs Permission
* **Policy** = 權限規則的定義文件（通常是 JSON 格式）。
* **Permission** = 使用者/群組根據 Policy 實際擁有的操作能力。
* **關係**：Policy 定義 → Permission 實現。

> 考試不會要求你手寫 JSON，重點是理解 **結構與邏輯**。

### 使用者、群組與權限疊加
* 使用者可屬於多個群組。
* 權限是 **聯集**，只要一個群組給了權限，使用者就會擁有。
* **考試陷阱**：要注意多群組合併後的實際權限。

---

## 📋 IAM 常見考點總結表
| 概念                 | 考試出現頻率 | 必須記住？                                        |
| -------------------- | ------------ | ------------------------------------------------- |
| Root 帳號用途        | 高           | ✅ 必須知道只能用於必要操作（如刪帳號/改付費方式） |
| IAM 使用者/群組      | 高           | ✅ 必須熟悉差異與用法                              |
| Policy vs Permission | 高           | ✅ 必須掌握定義與關係                              |
| Least Privilege 原則 | 高           | ✅ 必須記住最佳實踐                                |
| 帳單存取權限         | 中           | 理解即可                                          |
| Identity Center      | 低           | 可略過                                            |

---

## 📝 學習建議
* **背下 IAM、Route 53、CloudFront 屬於 Global Service**。
* **搞清楚 Policy 與 Permission 的關係**（定義 vs 實際效果）。
* **多 AZ 與 Region 的差異必須熟悉**。
* 把 **Least Privilege 原則** 當作金科玉律，考試遇到安全/授權場景幾乎都是正解。

---

👉 建議你之後可以搭配一些 **真題練習**，因為很多陷阱題都跟 IAM 權限細節有關。


