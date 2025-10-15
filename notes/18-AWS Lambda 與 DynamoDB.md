# 18-AWS Lambda 與 DynamoDB

2025-10-15

### 📌 Lambda：事件驅動的無伺服器函數運算

#### ✅ Lambda 本質與執行邏輯

* Lambda 是 **無伺服器 (serverless)** 計算服務，**僅在被觸發時運行**。
* 適用於輕量級、無狀態的運算任務，例如圖片處理、日誌分析、API 請求處理等。
* 執行完畢後，Lambda 執行個體會進入短暫的 **熱狀態 (warm state)**，以利重複使用。
* 每個請求可由不同的執行個體處理，支援自動水平擴展。

#### 🔄 Lambda 執行個體生命週期

* **冷啟動 (cold start)**：當新個體第一次被觸發時，會有延遲。
* **熱重用**：如果請求頻繁，Lambda 執行個體可重複處理多個請求。
* **閒置回收**：若執行個體空閒一段時間，AWS 會自動將其釋放。

> 📘 **考試重點提示**：
> Lambda 是「按請求計費」服務，**只有實際執行期間會計費**，閒置不會產生費用。

#### ⚙️ Lambda 與其他服務整合

* 可與 **S3、EventBridge、DynamoDB Streams、API Gateway** 等服務無縫整合。
* 亦可 **獨立使用**，只需確保網路連通性（例如連線至自建資料庫）。

#### 🧠 Lambda 使用場景範例

* 圖片上傳至 S3 後，Lambda 自動產生縮圖
* 從 API Gateway 收到請求，Lambda 查詢資料後回傳結果
* 定時任務：搭配 EventBridge 每小時執行清理作業

---

### 🗃️ DynamoDB：AWS 的 NoSQL 服務

#### ✅ 本質與特性

* **完全託管**、**高可用性**、**高擴展性**的 **NoSQL Key-Value/Document 資料庫**。
* 與 MongoDB 類似，但操作單位是 **Table**（無 database 層級）。
* 每張 Table 包含 **主鍵 (Primary Key)**，可以是：

  * 單一 Partition Key
  * 或 Partition Key + Sort Key（複合鍵）

#### 🆚 與 MongoDB 對照

| 項目     | DynamoDB                         | MongoDB                  |
| -------- | -------------------------------- | ------------------------ |
| 架構     | 無 database 概念，直接操作 table | 有 database + collection |
| 資料模型 | Key-Value / Document             | Document (BSON)          |
| 托管方式 | 完全由 AWS 管理                  | 可自建或使用雲端託管     |
| 彈性擴展 | 水平擴展、高可用內建             | 水平擴展需設計叢集結構   |

> 📘 **考試重點提示**：
> DynamoDB 是為了「彈性、高速、無需管理基礎架構」而設計的，適合高流量應用。

#### 💰 定價模式

* **On-Demand 模式**：不需預先配置容量，按請求次數與資料大小計費，**單位成本高但彈性高**。
* **Provisioned 模式**：手動配置 Read/Write Capacity Units (RCUs/WCUs)，適合流量穩定、**成本較低但需管理容量**。

> 🎯 **考試技巧**：
>
> * 流量不穩定 → On-Demand
> * 預期穩定流量 → Provisioned（可搭配 Auto Scaling）

---

### 🧠 總結：Lambda + DynamoDB 架構思維

* 適合建構 **無伺服器全託管應用架構**。
* Lambda 處理邏輯、API 請求等，DynamoDB 儲存資料。
* 可快速擴展、維運成本低，符合「雲端原生」設計哲學。

> 🧪 **考試建議**：理解服務特性與組合邏輯比死背 API 更重要，記得以「解決什麼問題」為核心思考每個服務。

---

若需針對某主題進一步練習或舉例，歡迎隨時指定主題！