# Speed Notes for SAA

1. ★★ **VPC 與 Availability Zone 關係**
   1. VPC **跨越整個 region 的所有 Availability Zone**⭐️
   2. **每個 subnet 必須完全位於單一 Availability Zone**，無法跨 zone
   3. VPC **只存在於單一 region**
   4. 跨 region 通訊要用 VPC Peering 或 Transit Gateway
2. ★★ **Subnet CIDR 配置原則**
   1. **Subnet CIDR 是 VPC CIDR 的子集**
   2. 在不同 Availability Zone 部署 instance 可**保護應用程式免於單點故障**
3. ★ **Subnet 預設網路設定**
   1. 每個 subnet 對應到**單一** Availability Zone
   2. 新建 subnet **預設連接到** VPC 的 main route table
4. 😇 **VPC 架構記憶技巧**
   1. **大樓比喻**：VPC = 整棟大樓（region）
   2. **樓層概念**：Availability Zone = 不同樓層（1F、2F、3F）
   3. **房間限制**：Subnet = 每層樓的房間，**房間不能跨樓層**
5. ★★ **VPC 與 Subnet 抽象層次差異**
   1. **VPC 是邏輯網路空間**：虛擬網路定義，只是「IP 範圍配置規則」
   2. **不需實體存在**：可覆蓋整個 region 的所有 AZ
   3. **Subnet 是實際資源部署位置**：真正放置 EC2、RDS 的地方
6. ★ **資源部署限制原理**
   1. **實體資源限制**：EC2 實例必須存在於具體的資料中心（AZ）
   2. **單一 AZ 綁定**：一台 EC2 不可能同時存在於兩個機房
   3. **Subnet 必須綁定單一 AZ**：因為承載實際的運算資源
7. 😇 **公司組織架構類比**
   1. **VPC (10.0.0.0/16)**：公司總部的「員工編號規則」
   2. **Subnet A/B/C**：台北/新竹/台中辦公室的員工（各自對應 AZ-1/2/3）
   3. **核心概念**：編號規則適用全公司，但員工只能實際坐在一個辦公室
8. ★ **考試重點記憶**
   1. **VPC 是規則，Subnet 是實體位置**
   2. **規則可涵蓋全 region，實體資源必須選定一個 AZ 部署**
9. ★★ **Private Subnet 核心定義**
   1. **Private subnet = 沒有 public IP 的 subnet**
   2. **EC2 在 private subnet 不能直接綁定 EIP**（綁定後就變成 public）
   3. **對外通訊途徑**：只能透過 NAT Gateway/Instance 做轉發
10. 👀 **Private Subnet 概念澄清**
    1. **正確定義**：Private subnet 就是 instance 沒有 public IP
    2. **常見誤解**：混淆 subnet 類型與 IP 分配的關係
