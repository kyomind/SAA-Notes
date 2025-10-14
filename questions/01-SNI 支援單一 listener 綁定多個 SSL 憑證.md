# 01-SNI 支援單一 listener 綁定多個 SSL 憑證

2025-10-14

## 核心概念

**SNI 讓一個 listener 可以綁定多個 SSL 憑證**，根據 client 請求的 hostname 來決定用哪張憑證。

## 使用場景

假設你要在同一個 Load Balancer 上服務：
- `api.example.com` → 用憑證 A
- `admin.example.com` → 用憑證 B
- `app.example.com` → 用憑證 C

SNI 讓你在**一個 HTTPS listener (port 443)** 上就能處理這三個不同的憑證。

## ALB vs NLB 的 SNI 支援

| Load Balancer | 支援 SNI |
|---|---|
| Application Load Balancer | ✅ 支援 |
| Network Load Balancer | ✅ 支援 |
| Classic Load Balancer | ❌ 不支援 |

兩者都支援，所以題目說「both ALB and NLB」是對的。

## 其他選項為何不對？

- **TLS Termination**：這是指 LB 解密 HTTPS 流量的功能，不是關於多憑證
- **SSL Security Policies**：這是設定加密演算法強度，跟多憑證無關
- **Host Headers**：這是 ALB 的 HTTP routing 功能，NLB 不支援（Layer 4 沒有 HTTP header）

## 記憶點

**多個網域 + 一個 listener = SNI**

這在實務上很常用，省錢又方便（不用開多個 listener）。
#題庫

> Network Load Balancer是l4，也支援嗎？⭐️⭐️⭐️

**對，NLB 支援 SNI。**

這看起來很矛盾，因為 NLB 是 Layer 4，但實際上：

## NLB 如何支援 SNI？

SNI 資訊在 **TLS handshake 的 ClientHello** 訊息裡，這發生在 TCP 連線建立之後、加密通道建立之前。

NLB 會：
1. 讀取 TLS ClientHello 封包（還沒加密）
2. 解析出 SNI 欄位（要連哪個 hostname）
3. 選對應的 SSL 憑證回應

所以雖然 NLB 是 Layer 4，但它會「==偷看==」TLS handshake 這段來做憑證選擇，不影響它的 Layer 4 本質。

## 差異點

| 功能 | ALB | NLB |
|---|---|---|
| SNI 支援 | ✅ | ✅ |
| Host-based routing | ✅ | ❌ |
| Path-based routing | ✅ | ❌ |

NLB 只能用 SNI 選憑證，**==不能用 hostname 做 routing==**⭐️（因為沒有解析 HTTP header）。

ALB 兩者都行：SNI 選憑證 + Host header 做路由。

