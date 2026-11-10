# fq-book — 阶段 0：结构与蒸馏范围

- 标题：《这本书能让你连接互联网》；维护者：hoochanlon，含多位转载作者。
- 来源：https://github.com/hoochanlon/fq-book ，master 分支，2026-11-07 下载。持续维护文档，不能确定统一出版年。
- 原文快照：work/fq-book-source/fq-book-master。已读目录、前言及 DNS、证书、软件协议、VPN、SSH、WireGuard、连接重置、代理类型、代理链、网页存档章节；其余章节尚未逐篇核读。
- 用途：编译 single 模式的专用入口 fq-book-network，供网络原理查阅和连接问题定位；首次试点。

## 结构
主旨：以原理解释网络访问路径，并结合客户端配置、代理和隧道帮助读者理解连接问题。
1. 阅读须知与快速开始。
2. DNS、hosts 与浏览器入口。
3. 代理客户端、SSH 与 VPN。
4. DNS/TLS/TCP、NAT、虚拟网络的技术解释。
5. 网页存档、检索和分布式内容访问。
6. 服务示例、历史工具与补充经验。
关系：先操作入口，再机制解释，最后专题补充；不是完整的现代网络标准教材。

## 术语与核心命题
- DNS：域名到地址的解析环节，书中围绕解析异常及替代解析讨论。
- 代理：经中间端点转发请求，书中包含应用配置、系统入口和代理链。
- VPN：以隧道与路由改变流量路径，不能与应用代理混同。
- SNI：TLS 连接用于标识目标名称的信息；与直接访问 IP 的差异相关。
- RST：TCP 重置信号；书中同时介绍正常协议用途与干扰情形。
- SSH 隧道：通过 SSH 客户端提供本地转发入口。
核心论证：工具会变化，因此理解路径与协议比记忆界面更能迁移；客户端入口、解析、连接及路由是不同环节；成功 ping 不能独立证明网页访问成功；具体协议选型取决于设备、网络与目标。

## 批判与边界
- 文档包含 2018、2019 年截图及历史服务，不能承诺链接、价格、免费额度和界面仍有效。
- DNS 与证书错误的因果解释、协议分层、VPN 性能排序等有简化或过度概括；未经当前一手资料验证，不转为诊断结论。
- 第三方转载与图片可能承载关键步骤；未检查图片的配置细节列待核查。
- 不把站点推荐、免费代理清单、注册/支付经验变成自动执行规则；原文禁止商用的声明与转载归属须保留，避免整书重分发。
- 区分原文支持的流程、当前官方文档修正和新编排的排障步骤，不把补充知识冒充原书方法。

## 关键任务与候选去向
|ID|任务|来源|拟交付|条件/缺口|
|---|---|---|---|---|
|T1|定位 DNS、TCP、TLS、应用层的异常|dns&hosts/dns.md；abc/4dns.md；abc/dc_zhenshu.md；a123/rst-syscall.md|证据与下一步检查表|来源存在简化，须验证；不单凭症状定因|
|T2|区分应用代理、系统代理、隧道|abc/connection.md；abc/proxy-s-x.md；abc/4vpn.md|路径选择卡|现代实现差异待查|
|T3|检查代理链的入口与转发关系|append/ss-relay.md；proxy/SSH-Tunnel.md|逐跳路径及验证卡|端口、客户端版本必须来自用户环境|
|T4|理解 VPN/SSH/WireGuard 配置结构|abc/4vpn.md；proxy/SSH-Tunnel.md；vpn/wireguard.md|配置核对卡|历史提供商步骤不直接执行|
|T5|寻找失效页面的历史版本|web/web-archive.md|存档检索卡|存档结果与当前事实区分|
|T6|其余工具、NAT、IPFS、服务与附录|_sidebar.md|后续覆盖审计与参考索引|未全量核读，不承诺完整覆盖|

优先级：T1、T2、T3；T4、T5 次之。预计 4–6 张能力卡，一个入口，不为章节逐个创建技能。

## 验收边界
原书提取与官方补充分别归属。已通过文本任务与格式检查，不代表真实网络部署。

## 本次补核
用户明确要求SSH/WireGuard形成完整配置能力。SSH限定已有获授权服务上的SOCKS动态转发及-L TCP转发；WireGuard限定已安装工具的Linux双peer分流。现有服务器搭建、全隧道网关及NAT不在此版本范围。正式能力以卡片为准。



# SSH 官方补核来源

补核日期：2026-11-07。状态：已读取官方网页，完成命令与边界的静态文档核对；没有SSH服务器输入，未实际连接。

|来源|定位|支持内容|
|---|---|---|
|https://man.openbsd.org/ssh|选项 -D、-L、-N、-i、-p、-v|动态SOCKS、本地TCP转发、仅转发模式、认证与调试参数|
|https://man.openbsd.org/ssh_config|ExitOnForwardFailure；StrictHostKeyChecking|转发启动检查范围；首次及变更主机密钥处理|
|https://man.openbsd.org/sshd_config|AllowTcpForwarding；PermitOpen；DisableForwarding|服务端转发权限与目标限制|
|https://curl.se/docs/manpage.html|--socks5-hostname；proxy scheme socks5h；--noproxy|远端名称解析及显式绕过控制|

原书来源：`work/fq-book-source/fq-book-master/docs/proxy/SSH-Tunnel.md`，只有图文式结构。能力卡明确标出官方扩展，不将其算作原书的完整部署方法。合成演练以example.net/example.com占位，无实际凭据、无第三方会话访问。

引用预算：未逐字复制官方段落；仅保留必要选项标识与自行构造命令。卡片按来源分配窄事实，避免整段手册转载。后续打包不要再复制这些网页全文。


# WireGuard 补核来源

核查日期：2026-11-07。公开一手文档已实际读取，未在此 Windows 会话搭建真实 Linux 隧道。

- https://www.wireguard.com/quickstart/ ：本地密钥生成与公钥导出；无流量时安静；NAT 后按需 keepalive，25 秒是建议而非强制。原书“公钥登记—导入—激活”结构仍可参考，外部服务可用性未验证。
- https://git.zx2c4.com/wireguard-tools/about/src/man/wg.8 ：Interface 私钥、Peer 对方公钥、AllowedIPs 双重语义、Endpoint 与运行指标。纠正旧书 vpn/wireguard.md:15-23 的歧义：不能把“删除重复片段”泛化成删除本地 PrivateKey/[Interface]；最终配置保留本端私钥和唯一 Interface。私钥不传给服务器/代理助手。
- https://git.zx2c4.com/wireguard-tools/about/src/man/wg-quick.8 ：Address 与自动路由、up/down、默认路由的额外行为、SaveConfig 会覆盖配置。卡片故意采用互相 /32 分流模板，避免无意接管默认路由；未包含网关 NAT/转发完成承诺。

双端对照、缺输入停止、路由冲突检查、最小变更和回滚为本次技能的操作编排；不能标成原书逐字流程。代表用例只能验证配置推理，实际可达性须在目标网络观察。
