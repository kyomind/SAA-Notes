# Speed Notes for SAA

1. ★ **TCP Health Check** 僅支援 Network Load Balancer 和 Classic Load Balancer，ALB 不支援 TCP health check
   - ALB 只支援 HTTP/HTTPS health check
2. ★★ DataSync vs Storage Gateway — 「遷移」vs「整合」
   - **DataSync**：一次性或定期的資料**遷移/搬遷**（關鍵詞：migrate, move, transfer, accelerate）
   - **Storage Gateway**：混合雲**持續整合**，讓地端像在用雲端（關鍵詞：hybrid, integrate, on-premises access）
   - **判斷技巧**：題目說 "immediately move" → DataSync；題目說「地端需持續存取雲端」→ Storage Gateway
3. ★★ S3 Object Lock — WORM 合規需求的標準答案
   - **用途**：看到「不可修改」「合規」「WORM」→ S3 Object Lock
   - **Governance Mode**：特定權限可覆寫（較彈性）
   - **Compliance Mode**：連 root 都無法刪除（最嚴格，醫療/金融適用）
   - **保留機制**：Retention Period（保留期限）或 Legal Hold（法律保留）
4. ★★ CloudTrail Data Events vs Management Events
   - **Management Events**：控制平面操作（CreateBucket, DeleteBucket 等），**預設啟用**
   - **Data Events**：資料平面操作（GetObject, PutObject 等），**預設不啟用，需手動開啟**
   - **考試重點**：題目要「物件層級稽核」→ 必須啟用 Data Events
5. 😇 DataSync vs Storage Gateway 都能搬資料，差異在「搬完之後」
   - **DataSync**：搬完就結束，任務導向（一次性遷移或定期同步備份）
   - **Storage Gateway**：搬完要持續用，架構導向（地端應用透過 Gateway 持續存取雲端儲存）
