# IAM Identity Center (AWS SSO) 學習摘要與考試重點

2025-10-04

這份整理幫助你理解 **IAM Identity Center (以前的 AWS SSO)** 的運作邏輯，並針對 SAA 考試可能出現的重點進行補充。

---

## IAM Identity Center 基本概念

### 1. Permission Set（權限集）

* **定義**：Permission Set 是一組權限，本質上是 IAM Policy 的集合。
* **功能**：定義使用者在登入 AWS Account 時，能扮演什麼角色。
* **對應關係**：每個 Permission Set 在 AWS 裡會對應成一個 IAM Role。

### 2. Assignment（指派）

* **指派的三元素**：

  ```
  User / Group  <-->  AWS Account  <-->  Permission Set
  ```
* 權限不會直接附加在 User/Group 上，而是透過 Assignment 產生。
* **群組的作用**：Group 只是方便管理，一旦 Group 被指派 Permission Set，底下所有 User 就會繼承該權限。

### 3. Session Duration（會話時長）

* 控制使用者透過 Identity Center 登入後，多久會被登出。
* 限制範圍：**3600 秒 (1 小時) ~ 43200 秒 (12 小時)**。
* 與自訂權限無關，所有 Permission Set 都適用這個限制。

---

## 實戰經驗整理

### 情境一：PowerUserAccess 的限制

* **PowerUserAccess** = 幾乎所有服務的管理權限，但 **不能管理 IAM (User/Group/Role/Policy)**。
* 設計目的：給開發團隊完整資源操作能力，但避免越權修改 IAM。

### 情境二：如何額外加上 IAM 權限？

* 方法一：新增 **IAM-readonly** Permission Set，讓使用者切換角色。
* 方法二（最佳做法）：建立自訂 Permission Set，合併以下：

  * `PowerUserAccess`
  * `iam:Get*` / `iam:List*` → 允許查看 IAM 資源
  * （選擇性）`iam:CreateRole`, `iam:AttachRolePolicy` → 練習角色相關操作

### 情境三：為什麼權限沒有「自動疊加」？

* 因為一個 Permission Set = 一個 IAM Role。
* 使用者登入時只能切換成某一個 Role，所以權限不會合併。
* **解法**：在一個 Permission Set 裡合併需要的權限。

---

## 考試重點提醒

> **SAA 考試常見陷阱**：
>
> * Identity Center **不會直接給 User/Group IAM Policy**，是透過 Permission Set + Assignment。
> * **權限不會疊加**，如果需要合併權限，必須建立一個新的 Permission Set。
> * **多帳號場景**：Identity Center 的價值在於能集中管理多個 AWS Account 的存取。

### IAM Identity Center vs IAM 的差異表格

| 功能項目  | IAM Identity Center                | IAM                              |
| ----- | ---------------------------------- | -------------------------------- |
| 使用者管理 | 可整合 AD/SSO/User Pool，統一登入          | 只能建立 IAM User                    |
| 權限附加  | 透過 **Permission Set + Assignment** | 直接掛 IAM Policy 在 User/Group/Role |
| 跨帳號管理 | ✅ 很方便（多帳號架構最佳解）                    | ❌ 必須手動配置 Role 信任政策               |
| 權限疊加  | ❌ 不支援（需合併成單一 Permission Set）       | ✅ IAM User/Group/Policy 可疊加      |

---

## 推薦學習策略

1. **熟悉核心服務**：EC2, S3, RDS, Lambda, IAM, VPC → 這些一定會考。
2. **理解 Identity Center 的角色定位**：

   * 考試題目多半是「多帳號管理」的情境。
   * 出現 SSO/集中管理，就要聯想到 IAM Identity Center。
3. **實戰練習**：

   * 建立一個自訂 Permission Set，結合 PowerUserAccess + IAM readonly。
   * 測試登入，體驗「角色切換」與「單一角色合併權限」的差異。

---

## 小總結

* **Permission Set = IAM Role 定義**
* **Assignment = User/Group + AWS Account + Permission Set**
* **權限不疊加，需合併到同一個 Permission Set**
* **Session Duration 上限 12 小時**
* 考試遇到「多帳號、集中管理」→ 直接聯想到 **IAM Identity Center**

