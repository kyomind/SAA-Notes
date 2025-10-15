# 21-Cognito 與 DynamoDB

2025-10-15

---

### 📌 Cognito：身分驗證與授權服務

#### ✅ 核心概念

* **Amazon Cognito** 是 AWS 提供的無伺服器身分驗證服務，用來管理使用者登入、註冊、授權。
* 適用場景：Web / Mobile 應用程式需要登入功能、不想自己打造驗證系統。

#### 🔐 兩大元件區別

| 功能   | User Pools             | Identity Pools            |
| ---- | ---------------------- | ------------------------- |
| 用途   | 處理使用者登入 / 註冊 / 認證      | 提供 AWS 資源的臨時存取憑證          |
| 驗證方式 | 自有帳密、社交登入 (Google, FB) | 與 User Pool 或聯邦身份驗證整合     |
| 常見搭配 | 應用程式前端                 | AWS 資源 (如 S3, DynamoDB 等) |

> 📘 **考試提醒**：
>
> * **User Pool = 身分驗證**，**Identity Pool = 授權資源存取**。
> * 若題目出現「存取 AWS 資源」、「取得 IAM 權限」關鍵字，答案傾向是 **Identity Pool**。

#### 🧠 快速記憶口訣

> 使用者怎麼登入？--> **User Pool**
> 登入後要用 AWS 資源？--> **Identity Pool**

#### 📦 Cognito 功能補充

* 支援「自訂註冊流程」：用戶可自行註冊帳密。
* 支援「社交登入 / 聯邦認證」：整合 Google、Facebook、SAML 等。
* 身分驗證完成後會提供 **JWT token**（ID token / Access token / Refresh token）給應用程式使用。

---

### 🗃️ DynamoDB：NoSQL 資料庫服務

#### ✅ 核心概念

* **Amazon DynamoDB** 是一種全託管的 Key-Value/Document NoSQL 資料庫，具備高可用性與自動擴展能力。
* 適合用在高流量的應用程式，如遊戲、IoT、即時數據平台等。

#### 📏 讀取容量與效能

| 名稱                             | 說明                            |
| ------------------------------ | ----------------------------- |
| **RCU (Read Capacity Unit)**   | 每秒支援一個 4KB 的強一致性讀取，或兩個最終一致性讀取 |
| **WCU (Write Capacity Unit)**  | 每秒支援一個 1KB 的寫入                |
| **DAX (DynamoDB Accelerator)** | 快取層，提供微秒級延遲的讀取，減少 RCU 使用      |

> 📘 **考試提醒**：
>
> * 遇到「高讀取頻率、低延遲需求」題目，答案傾向是 **DAX**。
> * 遇到「讀取延遲增加」但沒提快取，答案傾向是 **提高 RCU**。

#### 🧠 快速判斷邏輯

* **單純讀取變慢、用量增加**：提高 RCU。
* **極高讀取頻率、需要加速**：加上 DAX。

#### 📘 實務補充：DAX vs RCU 應用情境

| 場景                | 解法                    |
| ----------------- | --------------------- |
| API 讀取偶爾變慢        | 提高 RCU                |
| 熱門查詢資料反覆被查        | 加上 DAX 快取             |
| 高速 leaderboard 查詢 | 使用 DAX + Auto Scaling |

---

### ✅ 總結：重點記憶法

* **Cognito = 身分驗證與資源授權的服務**
* **User Pool 管登入、Identity Pool 管權限**
* **DynamoDB 用 RCU 控制讀取性能，用 DAX 提升極速讀
