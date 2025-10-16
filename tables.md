## AWS 服務的 Caching 機制

| 服務        | Caching 機制                                                |
| ----------- | ----------------------------------------------------------- |
| API Gateway | 可啟用 stage-level caching，暫存 API 回應內容（TTL 可調整） |
| DynamoDB    | 有 DAX (DynamoDB Accelerator)，提供 in-memory cache         |
| CloudFront  | 本質就是 CDN，核心功能就是 edge caching                     |

## Gateway Endpoint vs Interface Endpoint

| 特性     | Gateway Endpoint | Interface Endpoint    |
| -------- | ---------------- | --------------------- |
| 支援服務 | S3、DynamoDB     | 其他 AWS 服務         |
| 費用     | 免費             | 按小時 + 流量收費     |
| 實作方式 | 修改 route table | 建立 ENI (private IP) |
| 使用場景 | 考試首選         | 需要 PrivateLink 時   |

## RDS Read Replica 按位置分類

| 位置              | 用途                        | 說明                                 |
| ----------------- | --------------------------- | ------------------------------------ |
| 同 Region 不同 AZ | ✅ 分散讀取 + 災難備援       | 主要用途，既能分流讀取又能跨 AZ 容錯 |
| 同 AZ             | ✅ 分散讀取                  | 單純分流讀取，但沒有 AZ 層級容錯     |
| 跨 Region         | ✅ 分散讀取 + 跨區域災難備援 | 全球化應用、異地備援                 |

## Read Replica vs Multi-AZ

| 功能     | Read Replica           | Multi-AZ                     |
| -------- | ---------------------- | ---------------------------- |
| 目的     | 讀取分流、擴展效能     | 高可用性、自動容錯           |
| 是否可讀 | ✅ 可以讀取             | ❌ Standby 不可讀（同步備援） |
| 故障切換 | ❌ 需手動提升為主庫     | ✅ 自動切換，無需人工介入     |
| 延遲     | 非同步複製（秒級延遲） | 同步複製（零資料遺失）       |