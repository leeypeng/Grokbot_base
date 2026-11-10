# 能力索引（完整版）

| capability_id | 标题 | 重要度 | 意图 | 关键词 | 能力卡 |
|---|---|---|---|---|---|
| cap.fq-book.evidence-triage | 故障证据整理 | high | ping不通但网页能开；RST与SSL错误排查 | network error、DNS、TLS | capabilities/evidence-triage.md |
| cap.fq-book.proxy-entry | 代理入口与残留配置 | high | 关闭代理后不能联网；系统代理和应用入口核对 | PAC、proxy、监听 | capabilities/proxy-entry.md |
| cap.fq-book.proxy-chain | 代理链端口检查 | high | 两个代理端口冲突；前置代理路径检查 | proxy chain、端口冲突 | capabilities/proxy-chain.md |
| cap.fq-book.web-archive | 网页存档检索 | high | 失效网页恢复；Wayback历史版本与离线保存 | Wayback、archive | capabilities/web-archive.md |
| cap.fq-book.ssh-tunnel | SSH转发完整配置 | high | 配置SSH SOCKS动态转发或本地TCP转发及验收回滚 | SSH、SOCKS、-D、-L | capabilities/ssh-tunnel.md |
| cap.fq-book.wireguard-config | WireGuard双端配置 | high | Linux wg-quick双端分流配置、密钥、AllowedIPs、握手与回滚 | WireGuard、wg-quick、AllowedIPs | capabilities/wireguard-config.md |
