# 27-VPC 與 Subnet 核心概念

2025-10-22

本篇整理自我們的對話內容，補充並強化 AWS Solutions Architect Associate (SAA) 考試中常見的 **VPC 與 Subnet** 概念，讓你能以系統設計的思維理解考點。

---

## 🌐 一、VPC（Virtual Private Cloud）是什麼？

### 本質理解

* **VPC 就是你在 AWS 雲端上的專屬虛擬網路。**
* 可以把它想像成：你在雲端租了一整棟大樓（VPC），裡面可以自由劃分房間（Subnet），決定誰能進出（Security Group / NACL），以及對外的出入口（IGW / NAT Gateway）。

### 功能與作用

* 讓你的 AWS 資源（EC2、RDS、Lambda、ECS 等）運行在一個安全、隔離的網路環境中。
* 你能完全控制：

  * IP 範圍 (CIDR Block)
  * 路由表 (Route Table)
  * 安全性控制 (Security Group / NACL)
  * 對外連線方式 (Internet Gateway / NAT Gateway / VPN / Transit Gateway)

### 考試重點提醒

> 💡 **考試常考點：** VPC 預設提供一個 default VPC，方便快速啟用 EC2，但實務上與考試最佳實踐都是「自己建立專屬 VPC」以提升安全與控制性。

---

## 🧩 二、Subnet（子網）

### 概念與用途

* **Subnet 是在 VPC 裡劃分出的更小的網路區段。**
* 每個 Subnet 都有自己的 IP 範圍（CIDR Block），資源必須放在某個 Subnet 內才能運作。

### 公有子網 vs 私有子網

| 類型                    | 能否有公有 IP       | 能否被外部訪問     | 常見用途                    |
| --------------------- | -------------- | ----------- | ----------------------- |
| 公有子網 (Public Subnet)  | ✅ 可以           | ✅ 可以        | Web Server、Bastion Host |
| 私有子網 (Private Subnet) | ❌ 不行（除非透過 NAT） | ❌ 不可直接被外網訪問 | Database、App Server     |

> 💡 **考試提示：** 若題目提到「需要上網但不允許被外部連入」，選 **私有子網 + NAT Gateway**。

---

## 🧮 三、IP 分配邏輯

### 1. 私有 IP (Private IP)

* 用於 VPC 內部通訊，對外部不可見。
* 例如：`10.0.0.0/16`、`172.16.0.0/12`、`192.168.0.0/16`。

### 2. 公有 IP (Public IP / Elastic IP)

* 讓外部能直接訪問你的資源。
* 通常會綁定在 **Public Subnet 裡的 EC2** 或其他服務上。

### 3. IP 範例

```
VPC CIDR: 10.0.0.0/16 → 大範圍 (可容納約 65,536 個 IP)
Subnet: 10.0.1.0/24 → 小範圍 (可容納約 256 個 IP)
EC2: 10.0.1.15 → 從該 Subnet 分配的私有 IP
```

> ⚠️ **注意：** AWS 會保留每個 Subnet 的前五個 IP 地址供系統用途，實際可用 IP 會少一些。

---

## 🔗 四、VPC 之間的連線

### 1. 預設情況

* 不同 VPC 之間是完全隔離的，無法直接通訊。

### 2. 若需互聯的方式

| 連線方式                | 適用情境           | 特點                              |
| ------------------- | -------------- | ------------------------------- |
| **VPC Peering**     | 兩個 VPC 要直接互通   | 簡單但無法轉傳 (No transitive routing) |
| **Transit Gateway** | 多個 VPC、大規模網路架構 | 中央集線設計，更靈活可擴展                   |
| **PrivateLink**     | 單向私有連線到特定服務    | 高安全性，常用於內部 API 暴露               |

> 💡 **考試陷阱題常見：** 若需要多個 VPC 串接，**Transit Gateway** 通常是正確答案，不要選 Peering。

---

## 🚪 五、對外連線設計

| 類型                                     | 用途                           | 適用子網   |
| -------------------------------------- | ---------------------------- | ------ |
| **Internet Gateway (IGW)**             | 讓 VPC 內資源可被外部訪問              | 公有子網   |
| **NAT Gateway / NAT Instance**         | 讓私有子網的資源能上網 (下載更新)           | 私有子網   |
| **VPC Endpoint (Gateway / Interface)** | 讓 VPC 內資源能私下連到 AWS 服務 (不走公網) | 公私子網皆可 |

> 🧠 **記憶技巧：**
>
> * 公有子網想「被訪問」→ 用 **IGW**。
> * 私有子網想「出去但不被訪問」→ 用 **NAT Gateway**。

---

## 🧱 六、Subnet 規劃建議

1. **至少兩個 AZ（可用區）**：高可用性需求時，將子網分佈在多個 AZ。
2. **一個 AZ 多個子網**：可分為 Public / Private。
3. **CIDR 劃分原則**：

   * 不要太小（避免 IP 不夠）
   * 不要太大（浪費 IP 空間）
4. **命名規則建議**：

   * `vpc-prod-ap-northeast-1`
   * `subnet-public-a` / `subnet-private-b`

---

## 📘 七、SAA 考試重點整理

> ⚡ **你應該能回答的關鍵問題：**
>
> 1. VPC 與 Subnet 的差別是什麼？
> 2. 私有子網的 EC2 如何上網？
> 3. 如何讓多個 VPC 互通？
> 4. IGW 與 NAT Gateway 的差異？
> 5. 為什麼要跨多個 AZ 建立子網？

> 🎯 **考試判斷原則：**
>
> * 題目強調「安全性」→ 用私有子網 + NAT Gateway。
> * 題目強調「多 VPC 溝通」→ Transit Gateway。
> * 題目提到「私下存取 AWS 服務（如 S3）」→ VPC Endpoint。
> * 題目要求「公網可訪問」→ Public Subnet + IGW。

---

## ✅ 八、結語

理解 VPC 與 Subnet 的關係後，你就能建立清晰的雲端網路架構思維：

> **VPC 是大樓 → Subnet 是房間 → EC2 是房間裡的電腦 → IGW/NAT 是出入口。**

當你能用這種概念去思考，任何 VPC 類題目在考試中都能迎刃而解。
