# 28-VPC 與 NAT Gateway 深入解析

2025-10-23

## AWS 網路核心概念與 NAT Gateway 深入解析

本筆記整理自前述對話內容，並補充 SAA 考試常見重點，幫助你快速掌握 **VPC、Public/Private Subnet、Internet Gateway (IGW)** 與 **NAT Gateway (NGW)** 的核心概念與考題陷阱。

---

## 🌐 VPC 與 Subnet 概念

### VPC 是什麼？

* **VPC (Virtual Private Cloud)**：在 AWS 雲端中建立的隔離網路空間，類似你在本地資料中心中劃出的私有網路。
* 你可以自行定義 VPC 的 **CIDR 範圍 (例如：10.0.0.0/16)**。

### Subnet 的意義

* **Subnet 是 VPC 的子網段**，是從 VPC 的 CIDR 範圍中切割出來的小網路。
* 一個 VPC 可以擁有多個 Subnet，分佈於不同 **Availability Zone (AZ)**。

| 類型           | 能否連外       | 是否需 IGW | 是否需 NAT           | 常見用途                                   |
| -------------- | -------------- | ---------- | -------------------- | ------------------------------------------ |
| Public Subnet  | ✅ 可直接連外   | ✅ 需要     | ❌ 不需要             | 放置可被外部訪問的資源，如 Web Server、ALB |
| Private Subnet | 🚫 無法直接連外 | ❌ 不需要   | ✅ 若需連外則使用 NAT | 放置後端服務、DB、內部 API                 |

> 🧠 **考試提醒**：Public Subnet 能否「上網」的關鍵在於是否有 route table 指向 **Internet Gateway**，而非它的 IP 位址是否屬於公網。

---

## 🌉 Internet Gateway (IGW)

### 功能

* 讓 **VPC 內的 Public Subnet** 可以 **雙向連接網際網路**。
* 使 Public Subnet 內的實例能獲得公有 IP，外部可直接連入。

### 路由設定示例

```text
Destination     Target
0.0.0.0/0       igw-xxxxxx
```

> 🧩 **重點：** Internet Gateway 提供的是「雙向通道」，允許外部主動連線進來。

---

## 🔄 NAT Gateway (NGW)

### 主要用途

* 讓 **Private Subnet 的實例可以主動對外連線**（例如下載更新、呼叫外部 API）。
* 外部無法主動連入 Private Subnet，因此 **安全性更高**。

### 工作原理簡述

1. 內部實例向外發送請求時，請求先經由 NAT Gateway。
2. NAT Gateway 將封包的來源 IP 改寫成自己的公有 IP，並記錄對應表（內部 IP + Port → NAT 公網 IP + Port）。
3. 外部回應時，NAT Gateway 根據這個對應表，將回應正確轉回原內部實例。

> 🧠 **考試陷阱**：NAT Gateway 是「單向」的──允許內部連外，但不允許外部主動連入。

### 封包追蹤與 Port 對應

* NAT 透過「連線追蹤表」維護每個連線的對應關係。
* 使用不同的 **Port 組合** 區分不同內部連線。

| 封包欄位              | 用途                                             |
| --------------------- | ------------------------------------------------ |
| Source IP / Port      | 內部實例資訊                                     |
| Destination IP / Port | 外部目標，例如 `8.8.8.8:80`                      |
| NAT Translation       | 將內部 IP/Port 映射成 NAT Gateway 的公有 IP/Port |

### NAT Gateway 特性

* **高可用 (AZ 級別)**：每個 AZ 內建冗餘。
* **託管服務**：無需自行維運。
* **高吞吐量**：適合高頻外部連線。
* **按流量計費**：依據出站流量收費。

### 常見應用場景

* 私有子網中的 EC2 需下載系統更新或連接外部 API。
* 後端服務需同步外部資料來源。
* 保持伺服器隱私又能對外通訊。

> ⚠️ **考試提醒**：NAT Gateway 必須部署在 **Public Subnet**，並且關聯一個 **Elastic IP**。

---

## 🔐 Security Group：Inbound / Outbound

* **Inbound**：控制哪些外部流量能進入實例。
* **Outbound**：控制實例可以對外發送什麼流量。

> ✅ **回應流量自動允許**：若是內部主動發起連線，回應流量會自動被允許通過（有狀防火牆機制）。

---

## 🧩 總結比較表

| 功能             | Internet Gateway | NAT Gateway       |
| ---------------- | ---------------- | ----------------- |
| 方向性           | 雙向             | 單向（內部→外部） |
| 部署位置         | Public Subnet    | Public Subnet     |
| 需 Elastic IP    | 可選             | 必須              |
| 是否可被外部連入 | ✅ 可以           | 🚫 不可以          |
| 常見用途         | 公網 Web Ser     |                   |
