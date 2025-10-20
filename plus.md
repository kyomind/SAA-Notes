190. ★ AWS Organizations + SCP 的多帳戶隔離架構
    1. **核心需求**：在同一個組織內隔離 Production 和 Development 環境，限制只有通過稽核的服務能在 Production 使用
    2. **AWS Organizations**：統一管理**多個 AWS 帳戶**的容器
    3. **Organizational Units (OUs)**：邏輯分組（Prod OU、Dev OU）
    4. **SCP (Service Control Policy)**：套用在 OU 層級的**權限邊界**，控制整個 OU 內所有帳戶能用什麼服務
191. 😇 SCP 的運作方式與權限控制機制
    1. **Prod OU 策略**：套用 SCP → 只允許已稽核的服務（例如：EC2、RDS、S3）
    2. **Dev OU 策略**：不套用限制 → 團隊自由實驗任何服務
    3. **權限邊界特性**：**即使 Prod 帳戶內的 IAM 使用者有 Admin 權限，也無法使用被 SCP 禁止的服務**
192. 👀 S3 與檔案系統的類比記憶法
    1. **物件層級**：`arn:aws:s3:::bucket-name` → 資料夾本身（可以 ls、rm -rf）
    2. **檔案層級**：`arn:aws:s3:::bucket-name/*` → 資料夾內的檔案（可以 cat、touch）
    3. **GetObject 操作**：讀取檔案，當然要加 `/*`
193. 👀 IAM Condition Key：aws:**RequestedRegion** 的用途
    1. **功能定義**：限制 **API 呼叫的目標區域**
    2. **應用場景**：例如限制使用者只能在特定 Region 建立或修改資源
194. ★★ EventBridge 權限組態的核心邏輯
    1. **權限判斷原則**：**誰主動 vs 誰被動決定 Policy 類型**
    2. **Lambda 作為目標**：EventBridge → Lambda（EventBridge 主動呼叫）使用 **Resource-Based Policy**（附加在 Lambda 上）
    3. **Kinesis Data Streams 作為目標**：EventBridge → Kinesis（EventBridge 需要推送權限）使用 **Identity-Based Policy**（附加在 EventBridge 的 IAM Role 上）
