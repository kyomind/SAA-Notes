1. EC2 Auto Scaling 的伸縮範圍是整個 region，不止是 AZ
2. A subnet is a range of IP addresses in your VPC.
3. The AWS PrivateLink (which is also known as VPC Endpoint) **is just a highly available, scalable technology** that enables you to privately connect your VPC to the AWS services as if they were in your VPC.
4. Public Subnet ≠ 自動有 Public IP，而是「有資格配置 Public IP + 有 IGW 路由」