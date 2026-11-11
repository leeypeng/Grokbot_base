---
name: fq-book-network
description: >-
  当用户需要网络连接排障、系统/应用代理入口核对、双代理端口与路径检查、SSH动态或本地转发、Linux
  WireGuard双端分流配置，或网页历史存档恢复时使用。含
  DNS/TLS/proxy/SSH/WireGuard/Wayback。不是免费节点推荐、匿名性保证或通用编程技能；WireGuard公网全隧道网关、NAT与生产路由需另行设计。
---
# fq-book 网络原理与排障（含官方SSH/WireGuard补核） — 全书能力入口

## 触发与不触发

**适用**：与本书能力域相关的咨询与任务（见下方路由表的意图列）。
**不适用**：
- 免费代理或服务商推荐；账号、支付、匿名性保证。
- 公网全隧道网关、生产NAT/防火墙重构或自动执行未知PostUp脚本。
- 将软件安装、静态配置检查等同于现场连通验收。

## 核心原则（常驻速览，概览类问题读到这里即可回答）

1. 先记录事实、路径和完整错误，再列待验证假设；ICMP测试不等于应用连接测试。
2. 参数来自实际环境，示例地址与端口不等于可用值；缺参数只交付草稿。
3. 原书、官方补核和合成演练分开标记；不复制旧教程的不确定推断。
4. 配置前记录原状态，变更后分层验证，失败按清单回滚；没有实测不得报告已连通。
5. SSH/WireGuard只在卡片明确的系统和部署范围内使用；不外传私钥。

## 能力路由（先读本表，按意图加载 1 张能力卡）

| 用户意图 | 先读 | 补读/备注 |
|---|---|---|
| ping不通但网页能开；RST与SSL错误排查 | references/capabilities/evidence-triage.md | — |
| 关闭代理后不能联网；系统代理和应用入口核对 | references/capabilities/proxy-entry.md | references/capabilities/evidence-triage.md |
| 两个代理端口冲突；前置代理路径检查 | references/capabilities/proxy-chain.md | references/capabilities/evidence-triage.md |
| 失效网页恢复；Wayback历史版本与离线保存 | references/capabilities/web-archive.md | — |
| 配置SSH SOCKS动态转发或本地TCP转发及验收回滚 | references/capabilities/ssh-tunnel.md | references/capabilities/evidence-triage.md |
| Linux wg-quick双端分流配置、密钥、AllowedIPs、握手与回滚 | references/capabilities/wireguard-config.md | — |

**非能力类查询**：
- 书名/作者/章节/整书概览 → references/overview.md
- 术语解释 → references/glossary.md
- 决策规则速查（不需要原文依据时） → references/cheatsheet.md
- 完整意图与关键词索引（本表未覆盖的意图先查这里） → references/capability-index.md

## 加载规则

- 每次任务先读本文件，再按路由表加载 **1** 张能力卡；任务明确跨域时最多加载 2 张。
- 概览/书名类问题不加载能力卡，用「核心原则」与 overview.md 回答。
- 路由表与 capability-index.md 都无法命中的意图，明确告知超出本书范围，不要硬套。

## 边界与判停

- 缺目标主机、系统、端口、地址/路由、公钥等必要参数时先询问；私钥由用户在本机生成与保管。
- 主机指纹不匹配、路由冲突、未知配置钩子或现有隧道可能受损时停止变更，保留原配置。
