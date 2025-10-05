# SAA 考試重點：EC2 購買選項與 Fleet 管理

2025-10-05

## EC2 購買選項總覽

### 四大購買選項比較

| 購買選項 | 適用場景 | 價格優勢 | 中斷風險 | 承諾期 |
|---------|---------|---------|---------|--------|
| **On-Demand** | 短期、不可預測工作負載 | 基準價格 | 無 | 無 |
| **Reserved (RI)** | 穩定、長期運行 | 省 75% | 無 | 1-3 年 |
| **Spot** | 可中斷、批次處理 | 省 90% | **有** | 無 |
| **Savings Plans** | 靈活長期使用 | 省 72% | 無 | 1-3 年 |

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

| 策略 | 行為 | 適用場景 | 考試關鍵字 |
|------|------|---------|-----------|
| **capacityOptimized** | 選最不易中斷的池 | **最推薦**，平衡成本與穩定性 | "minimize interruptions" |
| **lowestPrice** | 選最便宜組合 | 成本優先、短期任務 | "lowest cost" |
| **diversified** | 平均分散多個池 | 可用性優先 | "high availability" |
| **InstancePoolsToUseCount** | 限制使用池數量 | 搭配 lowestPrice | 進階配置 |

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

## EC2 Fleet vs Spot Fleet

### 主要差異

| 特性 | Spot Fleet | EC2 Fleet |
|------|-----------|-----------|
| **實例類型** | Spot + On-Demand | Spot + On-Demand |
| **靈活性** | 較低 | 較高 |
| **進階功能** | 基本 | 豐富（優先順序等） |
| **考試頻率** | 中 | 低 |

### EC2 Fleet 使用場景

**混合配置範例：**
- 80% Spot（省錢）
- 20% On-Demand（保底）

**適用於：**
- 需要最低可用性保證的批次處理
- 混合工作負載（關鍵 + 非關鍵）
- 快速擴展的服務

> **考試判斷：** 題目出現 "must maintain minimum capacity" + "cost optimization" = EC2 Fleet

---

## Dedicated Hosts vs Dedicated Instances

### 使用時機

**Dedicated Hosts：**
- **合規需求**：需要專屬物理主機
- **BYOL（自有授權）**：Oracle、Windows Server、SQL Server
- 可看到底層硬體規格（socket、core 數）

**Dedicated Instances：**
- 僅需物理隔離
- 不需要授權綁定
- 看不到底層硬體

### 詳細比較

| 特性 | Dedicated Hosts | Dedicated Instances |
|------|----------------|---------------------|
| **硬體可見性** | ✓ 可看 socket、core | ✗ 不可見 |
| **BYOL 支援** | ✓ 支援 | ✗ 不支援 |
| **計費方式** | 按主機計費 | 按實例計費 |
| **價格** | 較貴 | 較便宜 |
| **使用場景** | 授權綁定、嚴格合規 | 僅需物理隔離 |

> **考試關鍵字：** "server-bound license" / "BYOL" / "socket-based licensing" = Dedicated Hosts

### 實務案例

```
情境：公司有 Oracle 資料庫授權（以 CPU socket 計費）
需求：合規要求專屬硬體 + 使用現有授權省成本
解答：Dedicated Hosts（可綁定授權到特定主機）
```

---

## Placement Groups 概述

### 三種類型比較

| 類型 | 用途 | 跨 AZ | 主要特性 | 考試頻率 |
|------|------|-------|---------|---------|
| **Cluster** | 低延遲、高吞吐 | ✗ 單一 AZ | 10 Gbps 網路 | 中 |
| **Spread** | 高可用性 | ✓ 多 AZ | 分散實例到不同硬體 | 低 |
| **Partition** | 大數據（HDFS、Kafka） | ✓ 多 AZ | 分區隔離 | 極低 |

### Cluster Placement Group

**適用場景：**
- HPC（高效能運算）
- 大數據分析（Hadoop、Spark）
- 需要實例間高速通訊
- 低延遲要求的應用

**關鍵特性：**
- 所有實例在**同一個 AZ**
- 10 Gbps 低延遲網路
- 風險：單一 AZ 故障 = 全部失效

> **考試關鍵字：** "lowest latency" / "high network throughput" / "tightly coupled" / "HPC" = Cluster

**反向判斷：**
- 題目強調 HA（高可用性）→ 不選 Cluster（選 Spread）

---

## 考試答題技巧

### 成本優化題型

```
判斷流程：
1. 可中斷 + 最低成本 → Spot Instance
2. 長期穩定運行 → Reserved Instance / Savings Plans
3. 短期不可預測 → On-Demand
4. 需要保底 + 省錢 → EC2 Fleet（混合）
```

### 合規與授權題型

```
判斷流程：
1. 需要 BYOL → Dedicated Hosts
2. 僅需物理隔離 → Dedicated Instances
3. 無特殊要求 → 其他選項
```

### Fleet 管理題型

```
判斷流程：
1. 純成本優化 + 可中斷 → Spot Fleet
2. 需要混合 Spot + On-Demand → EC2 Fleet
3. 最小化中斷 → capacityOptimized 策略
4. 最低成本 → lowestPrice 策略
```

---

## 備考建議與投資報酬率

### 高投資報酬率（必讀）

- ✅ **Spot Instance 使用場景**（15 分鐘）
- ✅ **Dedicated Hosts vs Instances**（10 分鐘）
- ✅ **購買選項比較表**（記憶 5 分鐘）

### 中投資報酬率（理解即可）

- ⭕ **Spot Fleet 分配策略**（10 分鐘）
- ⭕ **Cluster Placement Group**（10 分鐘）

### 低投資報酬率（知道概念即可）

- ⚠️ **EC2 Fleet 進階功能**（可能不考）
- ⚠️ **Spread/Partition Placement Group**（極少考）

---

## DevOps 實戰關聯

### 轉職 DevOps 會用到的技術

1. **Spot Instance + Spot Fleet**
   - CI/CD Runner（Jenkins、GitLab Runner）
   - Kubernetes Node Group（搭配 Karpenter）
   - 非關鍵任務容器化工作負載

2. **Auto Scaling 整合**
   - Spot Fleet 自動維持目標容量
   - 混合策略（Spot + On-Demand）

3. **成本優化實務**
   - 80% Spot + 20% On-Demand 配置
   - 使用 capacityOptimized 策略減少中斷

> **職涯建議：** 這些 Fleet 管理技術是 DevOps 日常工作核心技能，考試內容與實務高度相關。

---

## 記憶口訣整理

### Spot Instance
**"可中斷、批次處理、最省錢"**

### Dedicated Hosts
**"BYOL、看得到硬體、最合規"**

### Spot Fleet 策略
- **capacityOptimized** = 最穩（首選）
- **lowestPrice** = 最省（有風險）
- **diversified** = 分散（高可用）

### Placement Group
**"Cluster 快但脆、Spread 穩但散"**

---

## 補充：Reserved Instance 進階概念

### RI 類型比較

| 類型 | 靈活性 | 折扣 | 可否轉換 |
|------|--------|------|---------|
| **Standard RI** | 低 | 最高（75%） | ✗ |
| **Convertible RI** | 高 | 較低（54%） | ✓ 可換實例類型 |

### Savings Plans vs RI

- **Savings Plans**：更靈活，承諾每小時花費額度
- **Reserved Instance**：綁定特定實例類型/區域

> **考試趨勢：** AWS 推廣 Savings Plans，但 RI 仍會考。

---

## 最後提醒

- **不要死背**：理解使用場景比記規格重要
- **看關鍵字**：題目會用特定詞彙暗示答案
- **反向排除**：不符合條件的選項先刪除
- **實務思維**：想像你是架構師，會怎麼選擇