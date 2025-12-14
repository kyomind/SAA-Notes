# AWS 圖解集

## Auto Scaling 與 ELB 範圍

ASG 和 ELB 都是區域性服務 (Regional Services)，它們的運作範圍涵蓋整個區域 (Region) 內的多個可用區 (Availability Zones, AZs)。

![01](https://img.kyomind.tw/img-20251015-230427.png)

## Amazon Kinesis Data Streams Integrations

![02](https://img.kyomind.tw/img-20251016-112338.png)

## DynamoDB Streams 觸發 Lambda 與 SNS 通知流程

Lambda 是以 poll 的方式來讀取 DynamoDB Streams 的資料。因為 DynamoDB Streams 並不會主動推送資料給 Lambda。

![03](https://img.kyomind.tw/img-20251016-115523.png)

## EFS Performance Mode and Throughput Mode

注意，兩者並非二選一，而是可以**同時設定**的兩個不同層面的參數。

![04](https://img.kyomind.tw/img-20251017-100010.png)

## AWS ELB Health Check 設定

健康檢查是 ELB 重要的功能，說明它不只可以 proxy，還可以偵測目標的健康狀態

![05](https://img.kyomind.tw/img-20251017-165046.png)

## S3 Lifecycle configuration and Storage Class

![06](https://img.kyomind.tw/img-20251018-143843.png)

## Kinesis Data Streams + Lambda 的非同步處理架構

![圖 1](https://img.kyomind.tw/img-20251019-120246.png)

## Using Lambda@Edge and Kinesis together

![圖 2](https://img.kyomind.tw/img-20251019-170114.png)

## API Gateway + Lambda = Scalable Serverless API

注意 Lambda 後面接 DynamoDB，也是很常見的組合

![圖 3](https://img.kyomind.tw/img-20251019-170542.png)

## S3 加密

![圖 4](https://img.kyomind.tw/img-20251021-170229.png)

## S3 + SNS + SQS + 處理邏輯

![圖 5](https://img.kyomind.tw/img-20251022-172146.png)

## AWS WAF 運作機制

注意 WAF 常附著的三個服務：CloudFront、API Gateway、Application Load Balancer

![圖 6](https://img.kyomind.tw/img-20251022-175903.png)

## 用 S3 靜態網站托管 + CloudFront + Route 53 架構

![圖 7](https://img.kyomind.tw/img-20251022-180659.png)

## CloudFront Cache 行為

![圖 8](https://img.kyomind.tw/img-20251022-181233.png)

## CloudFront Signed URLs and Signed Cookies

![圖 9](https://img.kyomind.tw/img-20251023-135019.png)

## CloudFront Origin Access Control (OAC)

強制只能透過 CloudFront 存取 S3，避免直接從 S3 存取內容。

![圖 10](https://img.kyomind.tw/img-20251023-154415.png)

## AWS Control Tower

![圖 11](https://img.kyomind.tw/img-20251023-175137.png)

## Amazon Aurora Serverless v2 database

![圖 12](https://img.kyomind.tw/img-20251023-175922.png)

## Amazon Elastic Beanstalk

![圖 4](https://img.kyomind.tw/img-20251024-112920.png)

## Amazon EventBridge (Amazon CloudWatch Events) 整合

![圖 5](https://img.kyomind.tw/img-20251024-113633.png)

## 使用 AWS Storage Gateway（File Gateway）將本地檔案透明上傳至 S3 並自動歸檔到 Glacier

![圖 6](https://img.kyomind.tw/img-20251024-152412.png)

這張圖展示了 **File Gateway** 如何讓本地應用程式像存 NAS 一樣存放檔案，但實際資料會被上傳到 **Amazon S3**，並可透過 **Lifecycle Policy** 自動轉存到 **S3 Glacier** 做長期歸檔。

### 1. File Gateway = On-Prem NFS/SMB → S3 的橋樑

* 本地端(Server) 透過 **NFS/SMB 存取 File Gateway**
* 寫入的檔案會被當成 **objects** 放到 S3

**記住：File Gateway 是把「檔案」轉成「S3 Object」，不是 block volume。**

### 2. S3 是真正的儲存位置（不是 File Gateway 裝置）⭐️

File Gateway 本身不儲存資料，只做：⭐️

* Metadata / Cache
* 上傳 S3
* 提供本地 SMB/NFS 介面

所以容量擴展完全靠 S3，這也是為什麼考試常問你：

> 本地檔案庫要無限擴充 → 用 File Gateway + S3

你的圖中流程是：**On-Prem Server → File Gateway（VM/Appliance） → S3 Bucket → Glacier**

其中：

| 元件                    | 角色                                   | 是否存放資料？             |
| ----------------------- | -------------------------------------- | -------------------------- |
| File Gateway Appliance  | 協議轉換（NFS/SMB → S3 API），含 cache | ⚠️ 少量 cache（非真實儲存） |
| Storage Gateway service | 控制平面、管理、設定                   | ❌ 不存資料                 |
| S3 Bucket               | 真正儲存檔案                           | ✔️ 主要儲存                 |
| Glacier                 | 便宜的長期存放                         | ✔️ 歸檔儲存                 |

## AWS Shield Advanced Architecture

![圖 7](https://img.kyomind.tw/img-20251024-153353.png)

## Setting for IAM DB Authentication

![圖 8](https://img.kyomind.tw/img-20251024-154724.png)

## Enterprise Identity Federation

![圖 9](https://img.kyomind.tw/img-20251024-212920.png)

## AWS Transfer Family

![圖 10](https://img.kyomind.tw/img-20251024-222131.png)

## KMS and CloudHSM Integration

![圖 11](https://img.kyomind.tw/img-20251025-150425.png)

## Invoke an AWS Lambda function from an Amazon Aurora MySQL-Compatible Edition DB cluster with a native function or a stored procedure

![圖 13](https://img.kyomind.tw/img-20251027-173948.png)

## VPC Endpoint for S3 and DynamoDB

![圖 14](https://img.kyomind.tw/img-20251027-180055.png)

## ETL Architecture with AWS Glue

![圖 12](https://img.kyomind.tw/img-20251027-221851.png)

## S3 Access Point - VPC Only

You can configure any access point to accept requests only from a virtual private cloud (VPC) to restrict Amazon S3 data access to a private network.

![圖 15](https://img.kyomind.tw/img-20251028-173912.png)

注意：公開和私有存取點 (Public and Private Access Points) 只能二選一，無法同時存在於同一個存取點中。

## S3 Object Lock

![圖 16](https://img.kyomind.tw/img-20251028-174058.png)

## Data Lifecycle Manager for EBS Snapshots

![圖 17](https://img.kyomind.tw/img-20251029-170633.png)

## S3 → EMR → Redshift Spectrum 數據分析架構

![圖 18](https://img.kyomind.tw/img-20251030-145051.png)

## Route 53 Failover Routing Policy

![圖 19](https://img.kyomind.tw/img-20251030-162905.png)

## 直接用 ENI 插拔進行故障轉移(私有ip不變更)

![圖 13](https://img.kyomind.tw/img-20251101-111830.png)

## 合理的 Bastion Host 架構

兩個 VPC，一公一私。 公有 VPC 放 Bastion Host，私有 VPC 放內部資源。Bastion Host 透過 Private IP 連接私有 VPC 的資源。

![圖 14](https://img.kyomind.tw/img-20251101-114637.png)

## DMS 搬家架構

重點：需要一個**中繼的 EC2 instance** 來跑 DMS agent。

![圖 15](https://img.kyomind.tw/img-20251101-141404.png)

## RDS Proxy 管理 Lambda 連線池

![圖 16](https://img.kyomind.tw/img-20251102-113148.png)

## CloudFront + S3 管理靜態資源

![圖 17](https://img.kyomind.tw/img-20251102-113247.png)

## Direct Connect gateways and Transit Gateways

![圖 21](https://img.kyomind.tw/img-20251104-180235.png)

## EC2 狀態表

![圖 22](https://img.kyomind.tw/img-20251107-172210.png)

## Network ACL 設定頁面

![圖 18](https://img.kyomind.tw/img-20251108-105540.png)

## AWS Transcribe → AWS Translate → AWS Comprehend

![圖 20](https://img.kyomind.tw/img-20251108-144632.png)

## File Gateway with NFS and SMB Protocols

![圖 21](https://img.kyomind.tw/img-20251116-173030.png)

注意 File Gateway 是部署在本地端的虛擬機器 (VM) 或硬體設備上，而不是在 AWS 雲端中。

## VPC and Subnet Overview

![圖 22](https://img.kyomind.tw/img-20251116-173415.png)

## Gateway Endpoint for S3

![圖 23](https://img.kyomind.tw/img-20251116-232225.png)

## AKS Cluster Autoscaler

![圖 23](https://img.kyomind.tw/img-20251117-154642.png)

## Multi Region Failover Architecture and DynamoDB Global Tables

![圖 24](https://img.kyomind.tw/img-20251117-170136.png)

## API Gateway Custom Domain with Regional Endpoint

![圖 24](https://img.kyomind.tw/img-20251118-201549.png)

## 實用網路架構

![圖 25](https://img.kyomind.tw/img-20251120-175722.png)

## Amazon RDS Read Replicas

![圖 25](https://img.kyomind.tw/img-20251120-213835.png)

## AWS Transit Gateway Inter-Region Peering

![圖 26](https://img.kyomind.tw/img-20251120-222317.png)

## VPC Endpoint for EC2 to S3

![圖 26](https://img.kyomind.tw/img-20251121-174304.png)

## Egress-Only Internet Gateway

注意：它是**直接附加在 VPC 上**，而不是附加在子網路 (Subnet) 上。

![圖 27](https://img.kyomind.tw/img-20251122-153856.png)

## AWS Shield Advanced

可防禦**所有層級的 DDoS 攻擊** (Layer 3, 4, 7)，並提供專業的 DDoS Response Team 支援。

![圖 28](https://img.kyomind.tw/img-20251122-163202.png)

## CloudHSM vs KMS Comparison

![圖 30](https://img.kyomind.tw/img-20251123-105739.png)

## Direct Connect with Private VIF

![圖 27](https://img.kyomind.tw/img-20251125-120323.png)

## Volume Gateway: Cached vs Stored

![圖 28](https://img.kyomind.tw/img-20251125-172013.png)

## S3 Storage Classes Overview

圖中的「S3 Glacier」對應現在的 Flexible Retrieval，而 Instant Retrieval 是後來新增的，這張圖沒畫進去。

![圖 31](https://img.kyomind.tw/img-20251125-221248.png)

## EBS Architecture Overview

![圖 29](https://img.kyomind.tw/img-20251201-180009.png)
