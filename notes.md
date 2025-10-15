1. EC2 Auto Scaling 的伸縮範圍**預設是整個 region，也就是可以跨 AZ**
   1. 可以設定成單一 AZ，但省不了多少錢
   2. 數據傳輸：跨 AZ 有費用（約 $0.01/GB），但通常佔比很小
   3. 不能跨 region
2. **ELB 也不能跨 region，只能跨 AZ**
   1. 所有跨 region 的答案都有問題
   2. 跨 Region 需要用 Route 53 + 多個 Regional ELB
3. A subnet is a range of IP addresses in your VPC.
4. The AWS PrivateLink (which is also known as VPC Endpoint) **is just a highly available, scalable technology** that enables you to privately connect your VPC to the AWS services as if they were in your VPC.
5. Public Subnet ≠ 自動有 Public IP，而是「有資格配置 Public IP + 有 IGW 路由」
6. When you create a EC2 Capacity Reservation, you specify:
   1. – The **Availability Zone** in which to reserve the capacity
   2. – The **number of instances** for which to reserve capacity
   3. – The **instance attributes**, including the instance type, tenancy, and platform/OS
7. **You are limited to running On-Demand Instances per your vCPU-based On-Demand Instance limit**, purchasing 20 Reserved Instances, and requesting Spot Instances per your dynamic Spot limit **per region**.
   1. New AWS accounts 可能更低，而不是上述的 20
   2. **AWS 的 On-Demand Instance quota 是以 vCPU 為單位計算，而非實例數量**
8. Install the Amazon **CloudWatch agent** to all the EC2 instances that gather the memory and disk utilization data.
   1. View the custom metrics in the CloudWatch console.
   2. 這些 metrics 會被收集到 CloudWatch Logs，而且邏輯要自己寫
9.  you need to prepare a custom metric using a Perl or other shell script, as there are **no** ready-to-use metrics for the following:
   1. **Memory utilization**
   2. Disk swap utilization
   3. Disk space utilization
   4. Page file utilization
   5. Log collection
10. CloudWatch 預設可以看到以下這些 EC2 指標：
    1. CPU Utilization
    2. Disk Read activity
    3. Network In/Out
    4. Network packets In/Out
11. Auto Scaling group 的 scaling policy 有四種，都要了解具體內涵：
    1. Target Tracking Scaling
    2. Step Scaling
    3. Simple Scaling
    4. Scheduled Scaling
12. DynamoDB 的 RCU 和 WCU 是完全獨立的，可以各自調整，互不影響
13. Hot Key 問題不是總容量不夠，而是**單一 partition 被打爆**
    - 增加 RCU 只是增加總容量，但熱門商品還是集中在同一個 partition
    - **花大錢解決不了問題**
14. DAX 的缺點（考試也會考）
    - 只支援 DynamoDB，不能用在 RDS、Aurora
    - **最終一致性**（Eventually Consistent），快取可能有延遲
    - **成本**：DAX 節點要另外收費（但比暴力增加 RCU 便宜）
15. DAX = DynamoDB 的 Redis
    - Redis 用來快取 API response
    - DAX 用來快取 DynamoDB 熱門資料
    - 存取速度從毫秒降到微秒
16. DynamoDB Accelerator (DAX) is a fully managed, highly available, **in-memory cache for DynamoDB** that delivers up to 10x performance improvement.
17. DynamoDB Streams = 資料庫的變更日誌（Change Data Capture, CDC）
18. 白話：每當 DynamoDB 有資料新增/修改/刪除，Streams 會即時記錄這個事件，**然後可以觸發 Lambda 做後續處理**
19. An Edge-Optimized API Gateway is **best for geographically distributed clients.**
    1. API requests are routed to the nearest CloudFront Edge Location which improves latency.
    2. The API Gateway still lives in **one** AWS Region.
20. 目的關鍵需求：在 CloudFront Edge Locations 執行認證
    1. 不要打回 Origin
    2. 降低延遲——**用戶在哪裡，認證就在哪裡執行**
21. Lambda@Edge 是什麼？白話：**在 CDN 節點上跑的 Lambda，讓你在離用戶最近的地方處理請求**
22. **API Gateway 部署在單一 Region**，不在 Edge
    - 用戶還是要跨國打到你的 API Gateway，延遲沒改善
    - CloudFront 前面再放 API Gateway 是畫蛇添足
23. Lambda@Edge 可以在 CloudFront Edge Location 執行程式碼
24. The maximum size of **an item in a DynamoDB table** is **400 KB**.
25. Which AWS service allows you to build Serverless workflows using AWS services (e.g., Lambda) and supports **human approval**?
    - Step Functions
26. DynamoDB **原生支援「Export to S3」功能**，幾個點擊就完成，零程式碼、零維護、最便宜
27. DynamoDB + TTL (Time To Live) = 完美的 Session 管理方案
28. Amazon Cognito lets you add user sign-up, sign-in, and access control to your web and mobile apps quickly and easily.
    1. Amazon Cognito scales to millions of users
    2. Supports sign-in with social identity providers, such as Apple, Facebook, Google, and Amazon, and enterprise identity providers via **SAML 2.0 and OpenID Connect**.
29. A **launch template** is a template that an Auto Scaling group uses to launch EC2 instances.
    1. When you create a launch template, you specify information for the instances
    2. such as the ID of the **Amazon Machine Image (AMI)**, the instance type, a key pair, one or more security groups, and a block device mapping.
30. You can specify your launch template with multiple Auto Scaling groups.
    1. However, you can only specify **one** launch template for an Auto Scaling group at a time
    2. and **you can’t modify a launch template after you’ve created it.**
31. Therefore, if you want to change the launch template for an Auto Scaling group
    1.  you must create a template
    2.  and then update your Auto Scaling group with the new launch template.
32. To improve the **scalability** of your distributed system, you can add an **Auto Scaling group** to your EC2 instances.
33. 經典高可用架構:
    1. Launch an **Auto-Scaling group of EC2 instances** to host your application services and an **SQS queue**.
    2. Include an Auto Scaling trigger to **watch** the SQS queue size which will either scale in or scale out the number of EC2 instances **based on the queue**.
34. Predictive Scaling 假設 Auto Scaling Group 內所有 EC2 實例能力相同，才能準確預測需要多少台機器。
35. Step Scaling Policy：
    1. 你要明確定義每個區間要做什麼
    2. 可以立即回應多個告警（即使前一次擴展還在進行中）
    3. 適合流量變化劇烈的場景
36. 直白說：這題在考你理解「**高可用架構 = 多 AZ + Auto Scaling + ELB**」的**標準組合**
    1. T3 burstable 只是讓你知道能應付短期流量尖峰。
    2. 110% 運算能力是「短期」的，credits 用完就回到 baseline

