# 29-VPC 與網路安全核心觀念

2025-10-24

---

## 🧠 基礎概念：VPC 與私有連線

### 什麼是 VPC？

**VPC（Virtual Private Cloud）** 是 AWS 上的虛擬網路環境，讓你能自訂 IP 範圍、子網（Subnet）、路由表與安全設定。

### VPC Endpoint

**用途：** 讓 VPC 內的資源可以私下、安全地連接到 AWS 的服務，**不經過公網**。

#### 兩種類型

| 類型                                 | 特點                                                                | 適用服務        | 成本              |
| ------------------------------------ | ------------------------------------------------------------------- | --------------- | ----------------- |
| **Gateway Endpoint**                 | 使用路由表導向，**免費**、簡單                                      | S3、DynamoDB    | 無額外費用        |
| **Interface Endpoint (PrivateLink)** | 在 VPC 內建立 **ENI（Elastic Network Interface）** 提供私有 IP 存取 | 大多數 AWS 服務 | 有流量與 ENI 成本 |

> 💡 **考試提醒：** 如果題目提到 *S3* 或 *DynamoDB* 且要求私有連線，優先考慮 **Gateway Endpoint**；其他服務則使用 **Interface Endpoint**。

---

## 🧩 ENI（Elastic Network Interface）

**ENI 是虛擬網卡**，存在於 VPC 中，可附加到 EC2、Lambda（在 VPC 內時）、RDS 等資源上。

* **功能：** 提供資源網路連線能力（相當於機器的網路卡）
* **可附帶的資訊：**

  * Primary & Secondary Private IPs
  * Elastic IP（若有綁定）
  * Security Groups

> 🧭 **記憶法：** ENI 是“載體”，IP 是“地址”；IP 綁在 ENI 上，而 ENI 綁在資源上。

---

## 🔒 網路安全機制

### Security Group（SG）

* **有狀態（Stateful）**：若允許 inbound，則回應的 outbound 自動允許。
* **層級：** 附加在 **個別資源**（如 EC2、RDS、ELB）。
* **用途：** 控制資源的進出流量。

### Network ACL（NACL）

* **無狀態（Stateless）**：進出流量皆需個別設定規則。
* **層級：** 附加在 **Subnet** 層級。
* **用途：** 子網級別的封包過濾器。

#### 差異對照表

| 項目     | Security Group   | Network ACL               |
| -------- | ---------------- | ------------------------- |
| 狀態性   | 有狀態           | 無狀態                    |
| 附加對象 | EC2 / 資源       | Subnet                    |
| 流量方向 | 自動允許回應流量 | 需分別設 inbound/outbound |
| 預設行為 | 拒絕所有流量     | 允許所有流量              |

> ⚠️ **考試陷阱：** 流量必須同時通過 SG 與 NACL 的允許，**任一拒絕都會封鎖流量**。

> 💬 **補充：** Security Group 名稱中的 "Group" 是指一組安全規則，可重複套用至多個資源。

---

## 📊 網路監控與分析

### VPC Flow Logs

* **功能：** 記錄 VPC、子網、或 ENI 層級的流量資訊（來源 IP、目的 IP、傳輸協定、狀態等）。
* **輸出位置：** CloudWatch Logs 或 S3。

### 搭配 Athena 進行分析

* **Athena** 可直接使用 SQL 查詢存放在 S3 的 Flow Logs。
* **用途：** 網路行為分析、安全稽核、疑難排解。

> 🧠 **考試關鍵字對應：**
>
> * “分析 Flow Logs 資料” → Athena
> * “集中監控網路流量” → CloudWatch Logs

---

## 🧭 總整理：服務對應與考點速記

| 類別       | 服務           | 功能關鍵字        | 考試常見情境                      |
| ---------- | -------------- | ----------------- | --------------------------------- |
| **連線**   | VPC Endpoint   | 私有存取 AWS      | 不走公網、S3 或 DynamoDB 私有存取 |
| **網路卡** | ENI            | 虛擬網路介面      | 附加到 EC2 / Lambda（VPC 內）     |
| **安全性** | Security Group | 有狀態、綁資源    | 回應流量自動允許                  |
| **安全性** | NACL           | 無狀態、綁 Subnet | 要分別設進出規則                  |
| **監控**   | VPC Flow Logs  | 記錄流量          | 可搭配 Athena 查詢分析            |

---

> ✅ **考試建議：**
>
> * 多用比較表思考：Gateway vs Interface、SG vs NACL。
> * 題目若提到「安全性」、「私有連線」、「流量分析」等字眼，立刻聯想到對應服務。
> * 熟悉這些核心網路元件的角色與互動，能應付約 **20% 的考題權重**。
