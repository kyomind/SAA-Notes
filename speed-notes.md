1. EC2 Auto Scaling 的伸縮範圍**預設是整個 region，也就是可以跨 AZ**
   1. 可以設定成單一 AZ，但省不了多少錢
   2. 數據傳輸：跨 AZ 有費用（約 $0.01/GB），但通常佔比很小
2. A subnet is a range of IP addresses in your VPC.
3. The AWS PrivateLink (which is also known as VPC Endpoint) **is just a highly available, scalable technology** that enables you to privately connect your VPC to the AWS services as if they were in your VPC.
4. Public Subnet ≠ 自動有 Public IP，而是「有資格配置 Public IP + 有 IGW 路由」
5. When you create a EC2 Capacity Reservation, you specify:
   1. – The **Availability Zone** in which to reserve the capacity
   2. – The **number of instances** for which to reserve capacity
   3. – The **instance attributes**, including the instance type, tenancy, and platform/OS
6. You are limited to running On-Demand Instances per your vCPU-based On-Demand Instance limit, purchasing 20 Reserved Instances, and requesting Spot Instances per your dynamic Spot limit per region.
   1. New AWS accounts may start with limits that are lower than the limits described here.
7. Install the Amazon **CloudWatch agent** to all the EC2 instances that gather the memory and disk utilization data.
   1. View the custom metrics in the CloudWatch console.
   2. 這些 metrics 會被收集到 CloudWatch Logs，而且邏輯要自己寫
8. you need to prepare a custom metric using a Perl or other shell script, as there are **no** ready-to-use metrics for the following:
   1. Memory utilization
   2. Disk swap utilization
   3. Disk space utilization
   4. Page file utilization
   5. Log collection
9.