# 30-NAT 網路位址轉換

2025-11-04

---

### 📌 NAT 的本質與用途

**NAT（網路位址轉換）** 是一種讓私有子網（Private Subnet）裡的資源能透過一個公有 IP 位址來訪問外部網路的技術。

* **主要用途**：

  * 讓 **沒有公有 IP 的實例**（例如在 Private Subnet 裡的 EC2）能連到 Internet（例如下載更新、存取 API）
  * 保護內部網路位址不被外部直接存取
* **不會允許外部主動連入**，因為 NAT **只允許回應既有連線的流量**（有狀態追蹤）

> 💡 **考試重點：** NAT 是用來「讓私有子網的資源可以對外連線」，不是拿來阻擋外部連入的工具，這個行為是 NAT 的副作用而非本職。

---

### 🧠 NAT 是怎麼運作的？

* 當 Private Subnet 的資源對外發起連線（如 HTTP 請求），NAT 會：

  1. 替流量套用公有 IP 與新端口
  2. 建立 **連線狀態表（Connection Tracking Table）**
  3. 外部伺服器回應流量時，NAT 根據狀態表轉發回私有資源

* **無法從外部猜出 NAT 狀態**：

  * NAT 映射是動態分配的，含隨機端口與時間戳
  * NAT 不會把這些映射資訊公開

> ✅ **記住：NAT 是有狀態（Stateful）服務，只允許來自內部發起的連線對應的回應流量進來。**

---

### 🏡 家用 NAT vs AWS NAT

| 項目     | 家用 NAT                   | AWS NAT（NAT Gateway）       |
| -------- | -------------------------- | ---------------------------- |
| 部署位置 | 家用路由器                 | 公有子網中的 NAT Gateway     |
| 功能     | 讓家庭設備共用公有 IP 上網 | 讓私有子網資源可對外連線     |
| IP 分配  | ISP 分配固定或動態公有 IP  | 自行配置 Elastic IP          |
| 技術原理 | IPv4 NAT                   | IPv4 NAT，但更適用於雲端架構 |

> 📌 **本質相同，皆為位址轉換技術，但 AWS 的 NAT Gateway 是雲端化、可擴展、高可用版本。**

---

### 🌐 NAT 與 IPv6 的關係

* 在 IPv6 環境下：

  * 每個資源都有**全球唯一的可路由地址**
  * 通常不需使用 NAT，因為不存在 IPv4 的 IP 位址不足問題

* 然而：

  * 仍可使用 **NAT66（IPv6 的 NAT 版本）**，但實務上很少見
  * 安全性改由 **Security Groups** 與 **NACLs（網路 ACL）** 負責

> 🧠 **考試時要記住：IPv6 環境中不靠 NAT 控制連線，而是依賴 SG/NACL 控制流量進出。**

---

### 🔐 補充：NAT 與 AWS 安全機制

| 安全機制       | 功能                          | 建議搭配用途                  |
| -------------- | ----------------------------- | ----------------------------- |
| NAT Gateway    | 對外連線、遮蔽內部 IP         | 與 Private Subnet 一起使用    |
| Security Group | Instance 級別防火牆（有狀態） | 控制 EC2 等資源的進出流量     |
| NACL           | Subnet 級別防火牆（無狀態）   | 控制整個子網進出流量，補足 SG |

> 📌 **NAT 不等於防火牆。記得 NAT 是為了 outbound，而 SG/NACL 才是為了防護。**

---

### 🧪 考題判斷技巧小抄

* **題目問「如何讓 private subnet EC2 更新系統」→ 用 NAT Gateway**
* **題目問「讓外部伺服器無法主動存取內部」→ NAT 是自然隔離，但記得靠 SG/NACL 控制更嚴格**
* **IPv6 子網想控制入站流量→ 用 Security Groups，不需要 NAT**
* **有 NAT Gateway 但無 Internet Gateway → 還是無法連外（缺少路徑）**

---

### ✅ 總結

* **NAT 是有狀態的轉發機制，解決私有資源連外的問題**
* **AWS 上部署 NAT Gateway 時，通常會搭配 Elastic IP 和 Route Table 配置**
* **IPv6 環境下 NAT 非必需，安全依賴 SG/NACL**

> 📘 **建議練習：** 設計一個 VPC，包含 Public Subnet + Private Subnet，搭配 NAT Gateway 與 Route Table 練習配置流量路徑。

---

這些觀念理解透徹，你在 NAT 相關題目基本上就穩了。
