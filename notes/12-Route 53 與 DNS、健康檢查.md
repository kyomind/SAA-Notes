# 12-Route 53 與 DNS、健康檢查

2025-10-11

### 📌 考試重點與服務本質

#### Route 53 的核心功能

* **DNS 解析服務（Domain Name System）**：將網域名稱解析成 IP 位址。
* **健康檢查（Health Check）**：定期檢查資源是否正常運作。
* **路由策略（Routing Policy）**：控制流量該如何分配，例如 Failover、Simple、Multi-Value 等。
* **網域名稱註冊（Domain Registration）**：可直接在 Route 53 購買與管理網域。

> 💡 **考試提醒：** DNS 是為了提供**高可用性與容錯能力**，健康檢查常常與路由策略搭配考。

---

### 🔍 Health Check 健康檢查

#### 目的與使用情境

* 持續監控 EC2、ALB、S3 靜態網站等資源是否健康。
* 若資源失效，可搭配路由策略（如 Failover）將流量導向其他健康端點。

#### 設定方式

* 設定一個 **可被存取的 URL 或 IP 端點**，例如 `https://example.com/health`。
* Health Check 將定時對此端點發送 HTTP/HTTPS 檢查。

#### Health Check 與路由策略的配合

| 路由策略                | 是否支援 Health Check | 說明                              |
| ------------------- | ----------------- | ------------------------------- |
| Simple Routing      | ❌ 不支援             | 單純地將所有流量導向單一或多個固定 IP，不會偵測健康狀態。  |
| Multi-Value Routing | ✅ 支援              | 可針對每個 IP 設定健康檢查，只回傳健康的 IP 給使用者。 |
| Failover Routing    | ✅ 必須搭配            | 一主一備模式，根據健康狀態自動切換流量。            |

> 📘 **補充：** Health Check 也可搭配 CloudWatch Alarm 提供通知與進一步的自動化處理。

---

### 🔁 Failover Routing Policy 詳解

#### 運作方式

* 設定兩個端點：**Primary (主要)** 與 **Secondary (備用)**。
* Route 53 根據 Health Check 結果，自動將流量由主要端點切換到備用端點。

#### 考試關鍵點

> 🧠 **記憶重點：Failover 是一主一備。**
>
> * 備用端點需「**持續維持可用狀態**」，才能立即承接流量。
> * 常考陷阱：Failover 並非多活架構，只有兩個端點。

---

### 🧩 Domain Registrar vs DNS Provider

#### 差異比較

| 功能        | Domain Registrar             | DNS Provider                   |
| --------- | ---------------------------- | ------------------------------ |
| 管理網域名稱    | ✅ 是                          | ❌ 否                            |
| 提供 DNS 解析 | ❌ 否                          | ✅ 是                            |
| 例子        | GoDaddy、Namecheap、Cloudflare | Route 53、Cloudflare、Google DNS |

> ☑️ 考試觀念：註冊商與 DNS 提供商可分開，DNS 可設定成使用不同供應商。

---

### 🌟 Golden AMI 概念

#### 定義與用途

* Golden AMI 是一個 **經過最佳化、安裝好必要套件與安全設定的 AMI 映像檔**。
* 好處：確保每次部署一致性，減少人為錯誤。

#### 常見應用

* 自動化部署（搭配 CodePipeline、CodeDeploy）。
* 建立固定標準的環境映像，提升可維護性與一致性。

---

### ☁️ Elastic Beanstalk 簡介

#### 定義

* **Platform as a Service（PaaS）**：AWS 管理基礎設施，開發者專注撰寫應用。

#### 功能

* 自動建立與管理 EC2、ALB、Auto Scaling、RDS（可選）、CloudWatch。
* 支援 Node.js、Python、Java、.NET、Docker 等多種平台。

#### 考試重點

> 🎯 **重點：Elastic Beanstalk 可快速部署應用，抽象化底層資源管理。**

---

### 🧠 總結

* Route 53 不只是 DNS，還提供 Health Check 與多種流量路由策略。
* Health Check 是確保高可用性的重要工具，常與 Failover 或 Multi-Value 配合使用。
* Domain Registrar ≠ DNS Provider，要知道兩者職責。
* Golden AMI 有助於標準化部署流程。
* Elastic Beanstalk 適合快速部署、簡化管理，是應用程式開發者的好幫手。
