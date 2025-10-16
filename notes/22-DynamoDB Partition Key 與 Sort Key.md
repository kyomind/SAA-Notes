# 22-DynamoDB Partition Key 與 Sort Key

2025-10-16

---

## 🔑 基本規則與唯一性

### 單一 Partition Key

* **Partition Key = Primary Key**
* **不可重複**：全表唯一
* 適合資料筆數少、單一值唯一的情境

### Partition Key + Sort Key（複合主鍵）

* **唯一性規則**：必須保證 `(Partition Key, Sort Key)` **組合唯一**
* **Partition Key 可以重複**，但 Sort Key 需不同
* 適合一對多的情境，例如：

  ```
  Partition Key: user_id
  Sort Key: order_id
  ```

  * (123, A001) ✅
  * (123, A002) ✅
  * (123, A001) ❌（完全重複）

### GSI / LSI（輔助索引）

* 遵循相同規則
* Partition Key 也能重複，但若搭配 Sort Key，仍需整組唯一

---

## 📊 功能分工與比較

| 角色         | Partition Key                        | Sort Key                       |
| ------------ | ------------------------------------ | ------------------------------ |
| **唯一性**   | 單一主鍵時必須唯一；複合主鍵時可重複 | 搭配 Partition Key 必須唯一    |
| **主要作用** | 決定資料的分佈與流量分散             | 提供次層級的唯一性與排序能力   |
| **查詢能力** | 可用來直接定位 partition             | 可用來範圍查詢 (Range Query)   |
| **設計重點** | 需高 cardinality，避免 Hot Partition | 提升資料存取彈性，支援複雜查詢 |

---

## � 設計原則與最佳實務

### Cardinality（基數）的重要性

**❌ 低 cardinality 設計：**
```
Partition Key = country
- 台灣 900 萬筆，其他國家 100 萬筆
- 90% 請求集中在 Taiwan → Hot Partition
```

**✅ 高 cardinality 設計：**
```
Partition Key = user_id
- 100 萬用戶 × 每人 10 筆訂單
- 請求分散到 100 萬個 Partition Key → 良好分散
```

### Composite Primary Key 的效能優勢

**核心觀念：** Composite Primary Key（Partition Key + Sort Key）能提升效能，**不該避免使用**

**原因：**
- 更多 Partition Key 值的組合 = 資料分散更均勻
- 避免 Hot Partition
- 提升 throughput 利用率

**實例對比：**

* **Simple Primary Key：** `user_id`
  - 每個 user 只能有一筆資料

* **Composite Primary Key：** `user_id` + `timestamp`
  - 每個 user 可以有多筆資料（按時間排序）
  - 同樣的 Partition Key 值，但資料量分散更好

---

## ⚡ 考試重點與陷阱

### 必考核心概念

1. **單一 Partition Key Table** → Key 不可重複
2. **有 Sort Key 的 Table** → Partition Key 可重複，但組合唯一
3. **Throttling 問題** → 通常是 Partition Key 設計不良（低 cardinality）

### 常見考試陷阱

**❌ 錯誤選項：** 「為了避免 Hot Partition，不要用 Composite Key」

**✅ 正確做法：** 用 Composite Key + 選對 high-cardinality 的 Partition Key

### 設計原則總結

1. **優先選擇高 cardinality 的 Partition Key**
2. **使用 Sort Key 來支援一對多、時間序列、排序等需求**
3. **Hot Partition 問題** → 當流量集中在單一或少數 Partition Key，會導致 throughput 限制
