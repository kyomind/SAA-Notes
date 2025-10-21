# 26-Secrets Manager 與 RDS、ACM 整合

2025-10-21

## AWS Secrets Manager 與 RDS 整合

### ✅ 考試重點

> Secrets Manager 與 RDS 的整合可提供 **安全、可自動化的密碼管理機制**，考試中常出現此應用場景。

### 💡 本質與應用場景

* Secrets Manager 是 **用來儲存敏感資訊**（如 DB 密碼）的安全保險箱。
* RDS 是 AWS 提供的 **託管式資料庫服務**（如 PostgreSQL、MySQL 等）。
* 兩者整合後，可讓程式在執行時動態取得 DB 密碼，而不是硬編在 `.env` 或程式碼中。

### 🧠 實作方式

1. **在 Secrets Manager 中建立 Secret**（儲存 DB 資訊如帳號密碼）
2. **使用 AWS SDK（如 Boto3、AWS SDK for Java）在應用程式中動態取得 Secret**
3. **使用該資訊連接 RDS**

### 🔁 自動輪換密碼

* Secrets Manager 可自動幫你：

  * **產生新密碼**
  * **更新 RDS 密碼**（透過 Lambda function）
  * **同步更新 Secrets Manager 中的密碼值**

> ✅ 考試常考點：Secrets Manager 能與 RDS 整合並自動輪換密碼，不需要人工介入！

---

## AWS Certificate Manager（ACM）整合應用

### ✅ 考試重點

> ACM 可自動管理 TLS/SSL 憑證，並與 **ELB、CloudFront、API Gateway** 整合，提供 **HTTPS 加密**

### 📌 ACM 支援的服務與用途

| 服務            | 功能與整合點            |
| ------------- | ----------------- |
| ELB (ALB/NLB) | 安裝憑證來終止 HTTPS 流量  |
| CloudFront    | 提供全球 HTTPS CDN 支援 |
| API Gateway   | 提供 API HTTPS 安全連線 |

### 🔐 憑證管理特色

* **憑證自動續期**（90 天期效，ACM 自動續期）
* **不需手動更新憑證**
* 整合式 UI 操作簡便

### 🧩 API Gateway 三種 Endpoint 類型

| Endpoint 類型    | 是否支援 ACM | 用途描述               |
| -------------- | -------- | ------------------ |
| Edge-optimized | ✅        | 全球加速，適用跨地區用戶       |
| Regional       | ✅        | 區域內應用，延遲更低         |
| Private        | ❌        | 僅供內部 VPC 存取，無需公開憑證 |

> 🧠 記憶技巧：**只有 Public Endpoint 需要 HTTPS，加上 ACM**！

### 🌐 ALB 的 HTTP ➝ HTTPS 重導

* ALB 支援直接在 Listener Rule 設定中進行 HTTP ➝ HTTPS 自動轉向
* **常見應用場景**：強制所有使用者透過 HTTPS 存取網站

> ✅ 考點提示：ALB 支援 HTTP 轉 HTTPS，是設定 HTTPS 安全性最佳實踐之一！
