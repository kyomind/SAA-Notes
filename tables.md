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