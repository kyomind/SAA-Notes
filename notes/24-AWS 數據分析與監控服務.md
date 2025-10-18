# 24-AWS 數據分析與監控服務

2025-10-18

## 🔍 Athena：查詢 S3 上的結構化資料

### ✅ 本質與用途

* **互動式查詢服務**：允許你用 SQL 查詢儲存在 **S3 上的結構化或半結構化檔案**（例如：CSV、JSON、Parquet）
* **免伺服器、即用即付**：不需啟動叢集，也無需管理基礎設施

### 📌 考試重點

> **Athena 適用於：**
>
> * 大量資料已儲存在 S3
> * 需要 ad-hoc SQL 分析查詢

### ⚠️ 限制

* 無法查詢非結構化格式（如圖片、影片、二進位檔）

---

## 🧠 Redshift：數據倉儲服務

### ✅ 本質與用途

* **用來做 PB 級的 OLAP（分析型）查詢**
* 支援標準 SQL
* 可與 BI 工具整合，適合報表與歷史數據分析

### 📌 考試重點

> **Redshift 適用於：**
>
> * 長期儲存與分析大量資料
> * 建立數據倉儲解決方案

---

## 🔎 OpenSearch：全文檢索與日誌分析

### ✅ 本質與用途

* 取代原本的 Elasticsearch + Kibana 服務
* 適合快速做全文檢索、日誌視覺化分析
* 可搭配 CloudWatch Logs 或 Kinesis 資料流

### 📌 考試重點

> **OpenSearch 適用於：**
>
> * 搜尋功能（例如商品、文件內容）
> * 日誌查詢、可視化儀表板

---

## 🔄 EMR（Elastic MapReduce）：大數據處理框架平台

### ✅ 本質與用途

* 託管型 Hadoop / Spark 等大數據框架
* 用於批次資料處理、ETL、機器學習前處理

### 📌 考試重點

> **EMR 適用於：**
>
> * 需要大規模計算與分散式處理場景
> * 處理 TB~PB 級資料

---

## 📈 CloudWatch：AWS 原生監控服務

### CloudWatch Logs

* **集中收集應用與系統日誌**（EC2、Lambda 等）
* 可設定保留時間與搜尋過濾條件

### CloudWatch Logs Agent

* 安裝在 EC2 上，**自動上傳本地日誌到 CloudWatch Logs**

### CloudWatch Alarms

* 根據 Metrics 設定閾值，**自動觸發通知或操作**（例如透過 SNS、Auto Scaling、Lambda）

> 📌 **常見考點**：
>
> * EC2 CPU 超過 80%，自動發警報 → CloudWatch Alarm + SNS
> * 日誌集中管理 → CloudWatch Logs + Logs Agent

---

## 🔁 EventBridge：事件觸發整合服務

### ✅ 本質與用途

* AWS 的事件總線（Event Bus）服務
* 支援來自 AWS、SaaS、應用自定義事件
* 可自動觸發多種動作：Lambda、SQS、Step Functions 等

### 📌 考試重點

> **EventBridge 適用於：**
>
> * 架構中的事件驅動設計
> * AWS 服務整合自動化

---

## 🧠 總結與判斷技巧

| 服務        | 適用情境                | 特點                   |
| ----------- | ----------------------- | ---------------------- |
| Athena      | 查詢 S3 上結構化資料    | SQL 查詢、免伺服器     |
| Redshift    | 數據倉儲、大規模分析    | 高效分析、支援 BI      |
| OpenSearch  | 全文檢索、日誌分析      | 快速搜尋、大量資料分析 |
| EMR         | Hadoop / Spark 批次處理 | 分散式運算、大數據場景 |
| CloudWatch  | 日誌、指標、告警        | 原生整合、告警自動化   |
| EventBridge | 事件驅動自動化          | 無縫串接 AWS 各服務    |

> 📌 **記憶口訣：**
>
> * **分析選 Athena，倉儲用 Redshift，大數據丟給 EMR，搜尋交給 OpenSearch，告警靠 CloudWatch，自動觸發靠 EventBridge。**

---

如需後續針對每個服務進行實戰題練習或模擬題解析，也可以再指定我幫你準備。
