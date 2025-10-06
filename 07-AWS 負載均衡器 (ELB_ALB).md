# 07-AWS 負載均衡器 (ELB / ALB)

2025-10-06

---

### 🌐 一、ELB（Elastic Load Balancer）概念

**Elastic Load Balancer (ELB)** 是 AWS 提供的負載均衡服務，用於自動將進入的流量分配到多個後端資源（如 EC2、Lambda、IP、容器等），以確保高可用性與可擴展性。

#### 🧩 優點

* **高可用性**：跨多個 AZ 自動分配流量。
* **自動擴展**：可根據流量自動調整容量。
* **健康檢查 (Health Check)**：只將流量導向健康的後端資源。
* **SSL 終端管理**：支援 HTTPS/TLS 加密。

> 💡 **考試提醒**：
> AWS 會問你如何提升應用的高可用性與容錯性，**ELB + 多 AZ + Auto Scaling** 幾乎是標準答案。

---

### ⚙️ 二、ELB 三種類型比較

| 負載均衡器類型                             | 層級               | 特點                                                 | 適用場景              |
| ----------------------------------- | ---------------- | -------------------------------------------------- | ----------------- |
| **ALB (Application Load Balancer)** | 第7層 (HTTP/HTTPS) | 支援內容導向路由（Path、Host、Header、Query）、WebSocket、OIDC 驗證 | Web應用、微服務架構       |
| **NLB (Network Load Balancer)**     | 第4層 (TCP/UDP)    | 高效能、低延遲、支援固定 IP、可處理大量連線                            | 遊戲伺服器、金融交易、高頻請求場景 |
| **GLB (Gateway Load Balancer)**     | 第3層              | 用於部署第三方網路設備（如防火牆、入侵偵測系統）                           | 安全與網路監控架構         |

> 💬 **記憶訣竅**：
>
> * ALB：**A = Application (L7)**
> * NLB：**N = Network (L4)**
> * GLB：**G = Gateway (L3)**

---

### 🧱 三、ALB 與 Target Group 概念

**Target Group** 是 ALB 將流量導向的後端目標集合，可以包含：

* EC2 實例
* IP 地址
* Lambda 函數
* ECS / EKS 容器服務

#### 🔄 運作流程

1. ALB 接收流量
2. 根據路由規則（Host / Path / Header / Query）匹配對應 Target Group
3. 將流量導向健康的目標

> ⚠️ **考試陷阱題**：如果題目說 ALB 沒辦法將流量導向後端，要檢查是否有 **Target Group 未設定或健康檢查失敗**。

---

### 🛡️ 四、安全性設定重點

#### 1. **安全群組（Security Group）最佳實踐**

* EC2 的安全群組應只允許 **來自 ALB 安全群組** 的入站流量。
* ALB 對外公開，而 EC2 可保持在私有子網。

#### 2. **真實客戶端 IP 問題**

* ALB 轉發流量時，後端只看到 ALB 的 IP。
* 可透過 **HTTP Header：X-Forwarded-For** 取得客戶端真實 IP。

> 💡 **考試提示**：若題目問「後端需要知道客戶端 IP」，正確答案通常是 **使用 X-Forwarded-For header**。

---

### 🌍 五、DNS 與 ALB 整合

* ALB 建立後會自動生成一個 AWS 提供的 DNS 名稱（例如：`my-alb-123456.us-east-1.elb.amazonaws.com`）。
* 若要使用自訂網域，需在 Route 53 或 DNS 管理平台建立 **CNAME** 記錄指向 ALB DNS 名稱。

> 📘 **延伸補充**：
>
> * Route 53 可以建立 **Alias Record** 指向 ALB，效能更佳且不產生額外 DNS 查詢成本。

---

### ⚡ 六、效能與延遲考量

* 使用 **CNAME** 或 **Alias Record** 導向 ALB 時，效能影響極小。
* 真正影響延遲的因素通常是：

  * 後端服務的回應時間
  * ALB 的區域與客戶端的地理距離

---

### 🧠 七、SAA 考試重點總結

| 主題           | 重點                        | 考試常見陷阱             |
| ------------ | ------------------------- | ------------------ |
| ELB 類型選擇     | 根據 OSI 層級與用途選擇            | 混淆 ALB/NLB 功能差異    |
| Target Group | ALB 必須綁定至少一個 TG           | 健康檢查錯誤導致流量中斷       |
| 安全群組設定       | 只允許 ALB -> EC2 流量         | EC2 對外開放 0.0.0.0/0 |
| 客戶端 IP       | 使用 X-Forwarded-For header | 誤以為 EC2 會自動取得真實 IP |
| DNS 整合       | 使用 Route 53 Alias Record  | 使用 CNAME 導致額外查詢延遲  |

---

### 🏁 八、學習建議

1. **多畫架構圖**：理解流量如何流經 ALB、Target Group、EC2。
2. **多看題目關鍵字**：如 "Application layer", "TCP", "health check", "SSL termination"。
3. **實作練習**：在 AWS Console 中建立一個簡單的 ALB + EC2 架構。
4. **背熟對照表**：ALB、NLB、GLB 的用途與協定層級。

> ✅ **最重要的觀念**：ELB 並不只是「分流器」，它是 AWS 架構中實現 **高可用性、彈性伸縮、安全隔離** 的關鍵服務之一。
