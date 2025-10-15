# SAA 考試重點：EC2 購買選項與 Fleet 管理

2025-10-05

## EC2 購買選項總覽

### 四大購買選項比較

| 購買選項          | 適用場景               | 價格優勢 | 中斷風險 | 承諾期 |
| ----------------- | ---------------------- | -------- | -------- | ------ |
| **On-Demand**     | 短期、不可預測工作負載 | 基準價格 | 無       | 無     |
| **Reserved (RI)** | 穩定、長期運行         | 省 75%   | 無       | 1-3 年 |
| **Spot**          | 可中斷、批次處理       | 省 90%   | **有**   | 無     |
| **Savings Plans** | 靈活長期使用           | 省 72%   | 無       | 1-3 年 |

> **考試提醒：** Reserved Instance 是「計費折扣」不是實例類型，會自動套用到符合條件的 On-Demand 實例上。

---

## Spot Instance 詳解

### 核心特性

- **最高省 90%** 成本（相比 On-Demand）
- 價格浮動，基於 AWS 供需狀況
- **可被中斷**：AWS 提前 2 分鐘通知

### 使用場景判斷

#### ✅ 適合使用
- Batch 處理（資料分析、影片轉檔）
- 無狀態應用
- 容錯性高的工作負載
- CI/CD pipeline
- 大數據分析

#### ❌ 不適合使用
- 資料庫主節點
- 關鍵業務應用
- 需要持續運行的服務
- Web Server 主要流量

### 中斷處理機制

- **2 分鐘警告**：透過 EC2 metadata 或 EventBridge 接收
- **應對策略**：
  - Checkpoint 機制
  - 重新排程到其他實例
  - 保存中間狀態

> **考試關鍵字：** 看到 "lowest cost" + "fault-tolerant" + "can be interrupted" = Spot Instance

---

## Spot Fleet 管理

### 核心概念

**Spot Fleet** = 自動管理一組 Spot Instance（可選擇性包含 On-Demand）

**解決問題：**
- 單一實例類型缺貨 → 自動切換其他類型
- 價格飆高被中斷 → 自動替換
- 提高可用性 → 分散在多種實例類型

### 分配策略（考試重點）

| 策略                        | 行為             | 適用場景                     | 考試關鍵字               |
| --------------------------- | ---------------- | ---------------------------- | ------------------------ |
| **capacityOptimized**       | 選最不易中斷的池 | **最推薦**，平衡成本與穩定性 | "minimize interruptions" |
| **lowestPrice**             | 選最便宜組合     | 成本優先、短期任務           | "lowest cost"            |
| **diversified**             | 平均分散多個池   | 可用性優先                   | "high availability"      |
| **InstancePoolsToUseCount** | 限制使用池數量   | 搭配 lowestPrice             | 進階配置                 |

> **考試提醒：** Spot Fleet 現在支援混合 Spot + On-Demand Instance！

### 實務應用

```
典型配置範例：
- 目標容量：100 vCPU
- 實例類型池：t3.medium、t3.large、m5.large
- 策略：capacityOptimized
- 結果：AWS 自動選擇最穩定且划算的組合
```

---

## Dedicated Hosts vs Dedicated Instances

### 使用時機與核心差異

| 特性           | Dedicated Hosts     | Dedicated Instances |
| -------------- | ------------------- | ------------------- |
| **主要用途**   | BYOL（自有授權）    | 僅需物理隔離        |
| **硬體可見性** | ✓ 可看 socket、core | ✗ 不可見            |
| **授權綁定**   | ✓ 支援              | ✗ 不支援            |
| **合規需求**   | 嚴格合規            | 基本隔離            |
| **價格**       | 較貴                | 較便宜              |

> **考試關鍵字：** "server-bound license" / "BYOL" / "socket-based licensing" = Dedicated Hosts

**實務案例：** 公司有 Oracle 資料庫授權（以 CPU socket 計費）+ 合規要求專屬硬體 → Dedicated Hosts

---

## Placement Groups - Cluster 類型

### 使用場景與特性

**Cluster Placement Group** - 適用於需要低延遲、高吞吐量的應用：

- **適用場景：** HPC（高效能運算）、大數據分析（Hadoop、Spark）、需要實例間高速通訊
- **關鍵特性：** 所有實例在同一個 AZ、10 Gbps 低延遲網路
- **風險：** 單一 AZ 故障 = 全部失效

> **考試關鍵字：** "lowest latency" / "high network throughput" / "tightly coupled" / "HPC" = Cluster

> **記憶口訣：** "Cluster 快但脆"（高效能但單點故障風險）

---

## 考試答題技巧與關鍵字總結

### 成本優化題型判斷流程
1. **可中斷 + 最低成本** → Spot Instance
2. **長期穩定運行** → Reserved Instance / Savings Plans
3. **短期不可預測** → On-Demand
4. **需要保底 + 省錢** → Spot Fleet（混合策略）

### 合規與授權題型
- **需要 BYOL** → Dedicated Hosts
- **僅需物理隔離** → Dedicated Instances

### Fleet 管理策略
- **最小化中斷** → capacityOptimized 策略（推薦）
- **最低成本** → lowestPrice 策略（有風險）

### 記憶口訣
- **Spot Instance：** "可中斷、批次處理、最省錢"
- **Dedicated Hosts：** "BYOL、看得到硬體、最合規"
- **Cluster Placement：** "快但脆"（高效能但單點故障）

---

## 重點總結

### 必須掌握（高頻考點）
✅ **Spot Instance 使用場景判斷**
✅ **Dedicated Hosts vs Instances 差異**
✅ **購買選項比較（成本、穩定性、承諾期）**
✅ **Cluster Placement Group 應用**

### 理解即可（中低頻）
⭕ **Spot Fleet 分配策略**
⭕ **混合 Spot + On-Demand 配置**