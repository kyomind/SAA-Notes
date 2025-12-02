# AWS SAA 重點萃取 Prompt

從用戶指定的來源文件中，萃取標記為重要的條目到 `speed.md`。

## 📋 使用方式

用戶必須提供：
1. **來源文件**：指定要處理的檔案（如 `notes.md`、`notes-01.md`）
2. **行數範圍**：指定要處理的確切項目數（如 `1-100`、`50-200`）

## 🎯 萃取規則

**非常簡單**：只萃取包含 `⭐️` 符號的條目。

- `⭐️` = 用戶自行標記的重點，代表用戶認為重要
- 看到 `⭐️` → 直接複製該條目到 `speed.md`
- 沒有 `⭐️` → 跳過

### ⚠️ 重要：完整複製原則

`⭐️` 可能出現在**主項目**或**子項目**的任何位置（通常在句尾）。

**只要該項目的任何層級出現 `⭐️`，就必須複製「整個」項目，包含：**
- 主項目本身
- 所有子項目（不論子項目是否有 `⭐️`）

## 📝 輸出格式

### 格式要求
- **輸出檔案**：`speed.md`（根目錄）
- **第一層**：改用**無序清單（bullet list）**，以 `-` 開頭
- **第二層以下**：保持原樣（編號 `1.`、`2.` 或 `-` 不變）
- **好處**：第一層方便刪除，子層保留原結構

### 範例

來源（`notes-01.md`）：
```markdown
19. ★★ An **Edge-Optimized API Gateway** is **best for geographically distributed clients.**
    1. API requests are routed to the **nearest CloudFront Edge Location** which improves latency.
    2. The API Gateway still lives in **one** AWS Region.
    3. 這是預設選項，而且自動整合 CloudFront⭐️
20. ★ 目的關鍵需求：在 CloudFront Edge Locations 執行認證
    1. 不要打回 Origin
    2. 降低延遲——**用戶在哪裡，認證就在哪裡執行**
```

輸出（`speed.md`）— 第 19 項有 `⭐️` 在子項目，複製整個項目；第 20 項沒有 `⭐️`，跳過：
```markdown
- ★★ An **Edge-Optimized API Gateway** is **best for geographically distributed clients.**
   1. API requests are routed to the **nearest CloudFront Edge Location** which improves latency.
   2. The API Gateway still lives in **one** AWS Region.
   3. 這是預設選項，而且自動整合 CloudFront⭐️
```
說明：無序清單時，子項的 indent 統一只需要 3 個半型空格。

## ⚡ 注意事項

- 保持原文內容，不要修改或精簡
- 維持 `⭐️` 符號在輸出中
- **只有第一層**轉換為無序清單（`19.` → `-`）
- 第二層以下保持原格式不變
- 新內容**附加**到 `speed.md` 尾端，不覆蓋既有內容
