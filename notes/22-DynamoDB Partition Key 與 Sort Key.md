# 22-DynamoDB Partition Key 與 Sort Key

2025-10-16

---

## 🔑 Partition Key 與 Sort Key 的基本規則

### 單一 Partition Key

* **Partition key = Primary key**
* **不可重複**：全表唯一。
* 適合資料筆數少、單一值唯一的情境。

### Partition Key + Sort Key（複合主鍵）

* **唯一性規則**：必須保證 `(Partition key, Sort key)` **組合唯一**。
* **Partition key 可以重複**，但 Sort key 需不同。
* 適合一對多的情境，例如：

  ```
  Partition key: user_id
  Sort key: order_id
  ```

  * (123, A001) ✅
  * (123, A002) ✅
  * (123, A001) ❌（完全重複）

### GSI / LSI（輔助索引）

* 遵循相同規則。
* Partition key 也能重複，但若搭配 Sort key，仍需整組唯一。

---

## 📌 Cardinality（基數）的重要性

### 例子：訂單資料設計

* **❌ 低 cardinality**：`partition key = country`

  * 台灣 900 萬筆，其他國家 100 萬筆。
  * 90% 請求集中在 `Taiwan` → **Hot partition**。

* **✅ 高 cardinality**：`partition key = user_id`

  * 100 萬用戶 × 每人 10 筆訂單。
  * 請求分散到 100 萬個 partition key → **良好分散**。

> **考試提醒**：如果題目提到 **throttling**（限流問題），要優先檢查 **Partition key 是否集中在少數值**。

---

## 📊 Partition Key 與 Sort Key 的分工

| 角色         | Partition Key                        | Sort Key                       |
| ------------ | ------------------------------------ | ------------------------------ |
| **唯一性**   | 單一主鍵時必須唯一；複合主鍵時可重複 | 搭配 Partition key 必須唯一    |
| **主要作用** | 決定資料的分佈與流量分散             | 提供次層級的唯一性與排序能力   |
| **查詢能力** | 可用來直接定位 partition             | 可用來範圍查詢 (Range Query)   |
| **設計重點** | 需高 cardinality，避免 Hot partition | 提升資料存取彈性，支援複雜查詢 |

---

## 🏗️ Composite Primary Key 的效能優勢

這段在強調 **Composite Primary Key 的好處**。

### 核心觀念

Composite Primary Key（partition key + sort key）能提升效能，**不該避免使用**。

原因：
- 更多 partition key 值的組合 = 資料分散更均勻
- 避免 hot partition
- 提升 throughput 利用率

### 實例對比

**Simple：** partition key = `user_id`
- 每個 user 只能有一筆資料

**Composite：** partition key = `user_id` + sort key = `timestamp`
- 每個 user 可以有多筆資料（按時間排序）
- 同樣的 partition key 值，但資料量分散更好

### 考試陷阱

題目說「為了避免 hot partition，不要用 composite key」→ **錯誤選項**

正確做法是：**用 composite key + 選對 high-cardinality 的 partition key**

---

## ⚡ 補充重點

1. **Hot partition 問題**：當流量集中在單一或少數 partition key，會導致 throughput 限制 → throttling。
2. **設計原則**：

   * 優先選擇 **高 cardinality 的 Partition key**。
   * 使用 Sort key 來支援一對多、時間序列、排序等需求。
3. **考試必考點**：

   * **單一 Partition key Table → key 不可重複**。
   * **有 Sort key 的 Table → Partition key 可重複，但組合唯一**。
   * **throttling → 通常是 Partition key 設計不良（低 cardinality）**。
