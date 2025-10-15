# 17-AWS 容器服務：ECS、Fargate、EKS

2025-10-12

這份筆記彙整我們對話中關於 **ECS、Fargate、EKS** 的核心概念與 SAA 考試重點，並補充部分延伸說明以強化架構理解。

---

## 🐳 Amazon ECS（Elastic Container Service）

### ✅ ECS 是什麼？

* **AWS 的容器編排服務**，類似 Kubernetes。
* 幫助你部署、管理、擴展 Docker 容器。

### ECS 的兩種運行模式

| 運行模式       | 特點                                           |
| -------------- | ---------------------------------------------- |
| ECS on EC2     | 你管理底層 EC2 instance、彈性高，但需要維運。  |
| ECS on Fargate | 無伺服器，AWS 幫你處理底層資源，專注容器本身。 |

> 🧠 **記憶技巧**：ECS = 編排平台，Fargate = 執行模式。

### Task 與 Service

* **Task**：最小的執行單位，可包含多個容器。
* **Service**：確保特定數量的 Task 長期運行，可搭配 Auto Scaling。

### 水平擴展（Horizontal Scaling）

* 一個 Task 是一組容器。
* 多個 Task 可同時執行，實現負載擴展。

### 資料持久化

| 解法 | 適用場景與服務                                       |
| ---- | ---------------------------------------------------- |
| EBS  | 僅限單一 EC2，**不適用 ECS 多容器共享**。            |
| EFS  | **適合 ECS 與 Fargate 容器共享資料**，支援多點掛載。 |

> 📌 **考試提醒**：看到 ECS + 持久化，多半選 EFS。

### Auto Scaling 支援

* 可根據 CPU、Memory 使用率或 ALB 請求數，自動擴展 Task 數量。
* 彈性雖不如 EC2 Auto Scaling Group 細緻，但已足夠常見需求。

---

## 🚀 AWS Fargate

### ✅ Fargate 是什麼？

* 一種 ECS/EKS 的執行模式，屬於 **Serverless Container Runtime**。
* 你不需要佈建、管理底層 EC2，AWS 幫你處理資源配置與彈性調整。

### 優點

* 完全免管理伺服器。
* 依容器需求自動分配資源。
* 搭配 ECS 使用特別方便。

### 限制

* 成本相對較高，**缺乏對底層基礎設施的細緻控制**。
* 若你想自定機器規格或控制成本極致化，可能選 ECS on EC2 較合適。

---

## ☸️ Amazon EKS（Elastic Kubernetes Service）

### ✅ EKS 是什麼？

* AWS 的 **託管 Kubernetes 服務**，適合已有 Kubernetes 經驗的團隊。
* 幫你管理控制平面（Master node），你負責部署應用與 Worker node。

### 執行選項

| 執行方式       | 說明                                           |
| -------------- | ---------------------------------------------- |
| EKS on EC2     | 自行管理節點，彈性高，適合需要高度自定的場景。 |
| EKS on Fargate | 節點無需管理，適合短命工作負載、簡化架構管理。 |

### 遷移好處

* **簡化維運**：不用再維護控制平面、升級、安全修補。
* **整合 AWS 生態系**：IAM、CloudWatch、Auto Scaling 一條龍支援。
* **全球部署與擴展能力更強**。

### 成本考量

* 控制平面會產生固定費用（$0.10/hour per cluster）。
* 若搭配 EC2/Fargate 執行節點，還需另外計費。

> 📌 **考試提醒**：EKS 是 K8s 託管服務，考題常問其差異與何時選 ECS。

---

## 📚 額外補充：ECR（Elastic Container Registry）

* **Docker 映像檔儲存服務**。
* 類似 DockerHub，但與 ECS/EKS 整合度高。
* 可設為私有/公開、支援 IAM 權限控管。

---

## ✅ 考試選題技巧與判斷標準

### ECS vs EKS 選擇邏輯

| 場景                         | 建議服務      |
| ---------------------------- | ------------- |
| 快速部署、熟 Docker 不熟 K8s | ECS + Fargate |
| 已有 K8s 經驗，需搬上 AWS    | EKS           |
| 需細節控管或成本極致優化     | ECS on EC2    |
| 容器短命任務、自動擴展為主   | Fargate       |

### 出現關鍵字選擇指引

* **共享檔案系統、多容器存取** → 選 **EFS**
* **不想管理基礎架構** → 選 **Fargate**
* **Kubernetes、控制平面託管** → 選 **EKS**
* **需要完整節點控制** → 選 **ECS on EC2 或 EKS on EC2**

---

> 🧠 最後提醒：不要死背規格，考試考的是你能不能根據場景選出正確的 AWS 服務與架構組合。

學會「**這是什麼問題？AWS 提供了什麼解法？我該怎麼選？**」，你就能自然通過 SAA 考試。
