# 23-EBS 核心特性與考試陷阱

2025-10-16

## SAA 必考重點

**1. 可用性與複製**
- 自動在同一 AZ 內複製（防單點故障）
- **只能掛載到同 AZ 的 EC2**

**2. Multi-Attach 限制**
- 僅支援 **io1/io2 (Provisioned IOPS SSD)**
- 必須同 AZ + Nitro-based instance
- 其他 volume type 不支援

**3. 生命週期**
- 可獨立於 EC2 存在
- Terminate instance 時可選擇保留 EBS

**4. 彈性調整**
- Volume type、大小、IOPS 可線上修改
- 無需停機

**5. 加密與 SLA**
- AES-256 加密
- 99.999% 可用性

## 考試陷阱

❌ 跨 AZ 掛載（除非用 Multi-Attach... 但 Multi-Attach 也要同 AZ）
❌ gp3/st1 支援 Multi-Attach
✅ io1/io2 + 同 AZ + Nitro = Multi-Attach

**記憶口訣：「EBS 不跨 AZ；多掛只給 io」**
