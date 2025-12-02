# Super Speed Notes for SAA

★★ An **Edge-Optimized API Gateway** is **best for geographically distributed clients.**

1. API requests are routed to the **nearest CloudFront Edge Location** which improves latency.
2. The API Gateway still lives in **one** AWS Region.
3. 這是預設選項，而且自動整合 CloudFront⭐️

★★ VPC 內的應用如何安全存取 DynamoDB（避免走公網）

1. **解決方案：Gateway Endpoint**
2. 運作流程：EC2 (private subnet) → VPC route table (自動更新 DynamoDB CIDR) → Gateway Endpoint → DynamoDB (走 AWS 內網)⭐️

★★ Gateway Endpoint vs Interface Endpoint 比較⭐️

1. **Gateway Endpoint**：路由層級，**改 route table，免費**
2. **Interface Endpoint**：網路層級，加 ENI，要錢

★★ Kinesis Data Streams + Lambda 做即時資料脫敏

1. **標準套路**：AWS 處理敏感資料的推薦架構
2. **運作流程**：Kinesis 負責接收串流資料 → Lambda 在資料「transit（傳輸中）」時就進行轉換/匿名化 → 確保資料在落地 DynamoDB 前就已經處理好
3. **串流 + 即時處理 = Kinesis + Lambda**⭐️
