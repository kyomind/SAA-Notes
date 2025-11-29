# AWS Route Table 學習筆記

## 什麼是 Route Table

Route Table 是 VPC 內的**流量導向規則表**，決定網路封包要送往哪個目的地。

- **建立位置**：VPC 層級
- **作用對象**：Subnet 層級
- 每個 VPC 建立時會自動產生一個 **Main Route Table**

> 💡 **考試重點**：Route Table 是 VPC 的資源，但在 Subnet 層級生效。

---

## Route Table 組成結構

每條路由規則包含兩個欄位：

| 欄位            | 說明           | 範例                          |
| --------------- | -------------- | ----------------------------- |
| **Destination** | 目標 CIDR 範圍 | `10.0.0.0/16`、`0.0.0.0/0`    |
| **Target**      | 封包送往的目標 | `local`、`igw-xxx`、`nat-xxx` |

### 常見 Target 類型

| Target     | 用途                               |
| ---------- | ---------------------------------- |
| `local`    | VPC 內部流量（自動存在，無法刪除） |
| `igw-xxx`  | Internet Gateway，對外網路         |
| `nat-xxx`  | NAT Gateway，Private Subnet 對外   |
| `pcx-xxx`  | VPC Peering Connection             |
| `tgw-xxx`  | Transit Gateway                    |
| `vpgw-xxx` | Virtual Private Gateway（VPN）     |
| `eni-xxx`  | Elastic Network Interface          |

---

## Route Table 與 Subnet 的關聯關係

```bash
VPC (10.0.0.0/16)
│
├── Route Table A (Public)
│   ├── 關聯 → Public Subnet 1
│   └── 關聯 → Public Subnet 2
│
├── Route Table B (Private)
│   ├── 關聯 → Private Subnet 1
│   └── 關聯 → Private Subnet 2
│
└── Main Route Table (預設)
    └── 未明確關聯的 Subnet 自動使用
```

### 關聯規則

| 關係方向             | 對應關係   | 說明                        |
| -------------------- | ---------- | --------------------------- |
| Route Table → Subnet | **一對多** | 一個 RT 可關聯多個 Subnet   |
| Subnet → Route Table | **一對一** | 一個 Subnet 只能關聯一個 RT |

> 💡 **考試重點**：每個 Subnet **只能關聯一個** Route Table。未明確關聯的 Subnet 會自動使用 Main Route Table。

### 為什麼 Route Table 建立在 VPC 層級？

**好處：重複使用、集中管理**

- 一個 Route Table 可以關聯多個相同性質的 Subnet
- 修改一次，所有關聯的 Subnet 同時生效
- 避免重複設定相同規則

---

## Public vs Private Subnet 的路由差異

區分 Public 與 Private Subnet 的**關鍵在於 Route Table 設定**：

| Subnet 類型 | Route Table 特徵     | 對外流量路徑                 |
| ----------- | -------------------- | ---------------------------- |
| **Public**  | 有 `0.0.0.0/0 → IGW` | 直接走 Internet Gateway      |
| **Private** | 無直接到 IGW 的路由  | 走 `0.0.0.0/0 → NAT Gateway` |

### Public Subnet Route Table 範例

| Destination   | Target    | 說明               |
| ------------- | --------- | ------------------ |
| `10.0.0.0/16` | local     | VPC 內部流量       |
| `0.0.0.0/0`   | igw-xxxxx | 所有對外流量走 IGW |

### Private Subnet Route Table 範例

| Destination   | Target    | 說明                   |
| ------------- | --------- | ---------------------- |
| `10.0.0.0/16` | local     | VPC 內部流量           |
| `0.0.0.0/0`   | nat-xxxxx | 對外流量走 NAT Gateway |

> ⚠️ **注意**：僅有 Route Table 設定 IGW 還不夠，Instance 還需要 **Public IP** 才能真正對外連線。

---

## 路由選擇規則：最長前綴匹配

當封包目的地符合多條路由規則時，AWS 使用**最長前綴匹配（Most Specific Route Wins）**。

**範例**：封包目的地為 `10.0.1.50`

| Destination   | Target   | 是否匹配           |
| ------------- | -------- | ------------------ |
| `10.0.0.0/16` | Target A | ✅ 匹配             |
| `10.0.1.0/24` | Target B | ✅ 匹配（**優先**） |

結果：選擇 `/24`，因為比 `/16` 更精確。

> 💡 **考試重點**：CIDR 範圍越小（前綴數字越大），優先權越高。

---

## VPC Peering 與 Route Table

建立 VPC Peering 後，**雙方都必須手動新增路由**才能互通。

```bash
VPC A (10.0.0.0/16)              VPC B (172.16.0.0/16)
│                                │
└── Route Table                  └── Route Table
    172.16.0.0/16 → pcx-xxx          10.0.0.0/16 → pcx-xxx
```

> ⚠️ **考試陷阱**：VPC Peering 建立後不會自動新增路由，必須手動設定。

---

## Transit Gateway 簡化路由管理

當有多個 VPC 需要互連時，VPC Peering 會變得複雜（N 個 VPC 需要 N×(N-1)/2 個 Peering）。

**Transit Gateway** 作為中央路由器，簡化多 VPC 架構：

```bash
        VPC A ──┐
                │
        VPC B ──┼── Transit Gateway ── On-premises
                │
        VPC C ──┘
```

> 💡 **考試重點**：需要連接多個 VPC 或混合雲架構時，優先考慮 Transit Gateway。

---

## SAA 考試重點整理

| 主題                    | 重點                                  |
| ----------------------- | ------------------------------------- |
| **歸屬層級**            | Route Table 建立在 VPC，作用在 Subnet |
| **關聯規則**            | 一個 Subnet 只能關聯一個 Route Table  |
| **預設行為**            | 未關聯的 Subnet 使用 Main Route Table |
| **Public/Private 判斷** | 看是否有 `0.0.0.0/0 → IGW` 路由       |
| **路由優先權**          | 最長前綴匹配（/24 優先於 /16）        |
| **VPC Peering**         | 雙方都要手動加路由                    |
| **多 VPC 連接**         | 考慮 Transit Gateway                  |
| **local 路由**          | 自動存在，無法刪除或修改              |

## Route Table 實例解析

假設你的 VPC CIDR 是 `10.0.0.0/16`，有一個 Public Subnet 的 Route Table 長這樣：

| Destination   | Target     | 說明         |
| ------------- | ---------- | ------------ |
| `10.0.0.0/16` | local      | VPC 內部流量 |
| `0.0.0.0/0`   | igw-abc123 | 對外流量     |

---

### 情境 1：EC2 要連到同 VPC 內的 RDS

- **封包目的地**：`10.0.2.50`（RDS 的 Private IP）
- **匹配規則**：`10.0.0.0/16 → local`
- **實際行為**：封包留在 VPC 內部，直接送到目標，不經過任何 Gateway

---

### 情境 2：EC2 要連到 Google DNS

- **封包目的地**：`8.8.8.8`
- **匹配規則**：`0.0.0.0/0 → igw-abc123`
- **實際行為**：封包送往 Internet Gateway，然後出去公網

---

### 情境 3：Private Subnet 的 EC2 要更新套件

Private Subnet 的 Route Table：

| Destination   | Target     |
| ------------- | ---------- |
| `10.0.0.0/16` | local      |
| `0.0.0.0/0`   | nat-xyz789 |

- **封包目的地**：`151.101.1.69`（PyPI 伺服器）
- **匹配規則**：`0.0.0.0/0 → nat-xyz789`
- **實際行為**：封包送往 NAT Gateway → NAT Gateway 用自己的 Public IP 轉發到公網 → 回應封包原路返回

---

### 一句話總結

**Destination = 「封包要去哪」，Target = 「封包要走哪條路」**
