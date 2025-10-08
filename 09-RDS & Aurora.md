# 09-RDS & Aurora

2025-10-08

---

## 🧠 核心概念總覽

### RDS（Relational Database Service）

* AWS 的 **託管式關聯式資料庫服務**。
* 支援多種資料庫引擎（MySQL、PostgreSQL、MariaDB、Oracle、SQL Server、Aurora）。
* 自動處理：**備份、修補、監控、容錯**。

### Aurora（Amazon Aurora）

* 屬於 RDS 家族中的「**雲端原生資料庫引擎**」，兼容 **MySQL** 與 **PostgreSQL**。
* 比原生 MySQL / PostgreSQL 高達 **3～5 倍效能**。
* 內建 **高可用**（Multi-AZ）與 **自動擴展儲存容量（最高 128TB）**。
* 適合：**高效能、高可用、雲端優化** 的應用場景。

> 📘 **考試提醒**：題目問到「高可用、高效能、雲端原生」→ **Aurora 是標準答案。**

---

## ⚙️ RDS Read Replica（讀取副本）

### 定義與用途

* **Read Replica** 是一個只讀的資料庫副本，用來分擔主資料庫的讀取壓力。
* 使用 **非同步複製（Asynchronous Replication）**。

### 適用情境

* 主資料庫（Primary）讀取負載太高。
* 想提升讀取效能或做報表查詢。
* 作為跨區（Cross-Region）災難備援的基礎。

> 💡 **記憶技巧**：
>
> * 提升讀取效能 → **Read Replica**。
> * 提升寫入或高可用性 → **Multi-AZ Deployment**。

### 類型與差異

| 類型                | 說明                  | 適用場景            |
| ----------------- | ------------------- | --------------- |
| 同區域（Intra-Region） | 複製在同一個 Region，不同 AZ | 提升讀取效能、分散負載     |
| 跨區域（Cross-Region） | 複製到不同 Region        | 災難恢復（DR）、全球讀取需求 |

> 🧭 **考試陷阱**：跨區複製的延遲較高，不適合即時查詢。

---

## 🏗️ Multi-AZ 部署 vs Read Replica

| 比較項目 | Multi-AZ        | Read Replica      |
| ---- | --------------- | ----------------- |
| 同步方式 | 同步（Synchronous） | 非同步（Asynchronous） |
| 用途   | 高可用（HA）與容錯備援    | 分擔讀取壓力            |
| 是否可讀 | Standby 節點不可讀   | 可讀取               |
| 切換行為 | 主節點故障自動切換       | 需手動升級為主節點         |

> 📘 **考試提醒**：題目若提到「自動故障切換」或「高可用性」，選 **Multi-AZ**；若提到「報表查詢」或「讀取流量分散」，選 **Read Replica**。

---

## 🌍 Aurora 架構與端點（Endpoints）

### Aurora 的節點類型

| 節點類型        | 功能          | 可讀/可寫  |
| ----------- | ----------- | ------ |
| Writer Node | 主節點，處理寫入與讀取 | ✅ 讀寫皆可 |
| Reader Node | 副本節點，用於分擔讀取 | ✅ 只讀   |

### Aurora 端點種類

| 端點類型                | 說明                | 用途            |
| ------------------- | ----------------- | ------------- |
| **Writer Endpoint** | 連接主節點（唯一寫入點）      | 應用程式寫入資料時使用   |
| **Reader Endpoint** | AWS 自動分配流量到多個讀取副本 | 分擔讀取負載        |
| **Custom Endpoint** | 使用者自訂副本群組端點       | 針對特定工作負載或應用程式 |

> 💡 **考試技巧**：
>
> * 想要「自動分散讀取流量」→ **Reader Endpoint**。
> * 想「自訂流量導向」→ **Custom Endpoint**。

---

## 🧩 Snapshot（快照）

### 定義

* 一種 **時點備份（Point-in-Time Backup）**。
* 可用於：

  * 手動或自動備份資料庫。
  * 建立新資料庫實例（例如從 Snapshot 還原）。

### 適用於服務

* **EBS（Elastic Block Store）**
* **RDS / Aurora**
* **Redshift**

> 📘 **考試提醒**：Snapshot 是一種「備份」而非「即時同步」工具。

---

## 🧩 延伸補充（SAA 常見考點）

### RDS 與 Aurora 的備援與恢復選項

| 功能                        | RDS | Aurora |
| ------------------------- | --- | ------ |
| 自動備份                      | ✅   | ✅      |
| Snapshot                  | ✅   | ✅      |
| Cross-Region Read Replica | ✅   | ✅      |
| Multi-AZ 同步複製             | ✅   | ✅（內建）  |
| 自動擴展儲存                    | ❌   | ✅      |

### 常見陷阱題

> 🚨 **陷阱 1**：Read Replica 不是自動容錯備援，要手動 Promote。
>
> 🚨 **陷阱 2**：Multi-AZ standby 無法讀取，只能在故障時接管。
>
> 🚨 **陷阱 3**：跨區複製延遲高，不適合低延遲應用。
>
> 🚨 **陷阱 4**：Aurora 的 Reader Endpoint 是自動負載平衡，Custom Endpoint 才是手動選擇節點。

---

## 🏁 結論與考試建議

* **RDS Read Replica**：提升讀取效能。
* **Multi-AZ**：提升高可用性與容錯。
* **Aurora**：RDS 的進化版，效能強、高可用、雲端原生。
* **Snapshot**：時點備份，用於還原與複製。

> 🎯 **考試策略**：
>
> * 看關鍵字判斷考點：
>
>   * 「High Availability」→ Multi-AZ
>   * 「Read Scaling」→ Read Replica
>   * 「Cross-Region DR」→ Cross-Region Read Replica
>   * 「Fully Managed & Performance」→ Aurora
