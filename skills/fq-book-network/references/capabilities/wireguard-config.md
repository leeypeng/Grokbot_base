## R — 作用与来源
为已安装 WireGuard/wg-quick、已具备管理员权限的两台 Linux 主机构造和核对双 peer 分流配置。来源：fq-book 的 vpn/wireguard.md 仅保留公钥交换、配置、激活、测试的结构；字段与操作以 [wg](https://git.zx2c4.com/wireguard-tools/about/src/man/wg.8)、[wg-quick](https://git.zx2c4.com/wireguard-tools/about/src/man/wg-quick.8) 和 [Quick Start](https://www.wireguard.com/quickstart/) 补核。以下是补充编排，不声称为原书完整流程。

## I — 输入与意图
触发：核对 WireGuard 配置、生成双机分流隧道模板、排查有接口无握手。先取得两端 Linux/工具版本、接口名、隧道地址、双方公钥、各端允许的目标 CIDR、监听 UDP 端口、可达端点、已有路由与防火墙条件；只确认私钥已在各端本地生成，不索取真实私钥。任一必需项缺失时停止生成可运行配置，列缺项；双 NAT 且无可达端点不承诺直连。不得借此默认开放公网转发或部署生产网关。

## A1 — 合成演练
以下占位示例并非原书案例：A 能访问 B 的可达 UDP 端点，只需访问彼此隧道地址；A、B 各自保管私钥，向对方交换公钥。B 的 Peer 指向 A 公钥而不是 B 自己。若 A 的 Peer 公钥误填 A 公钥，应指出身份配对错误；接口出现不证明握手成功。

## A2 — 触发与执行入口
触发“配置 WireGuard”“双端分流”“有接口无握手”；若只是普通代理端口冲突，转代理链卡，不使用本卡。以下步骤是 E 执行契约的一部分。

1. 确认目标为双主机分流，列明 A→B 与 B→A 的地址/路由。不自动写 0.0.0.0/0 或 ::/0。检查目标 CIDR 与现有 LAN、其他 VPN、其他 peer 的 AllowedIPs 是否重叠；有冲突先说明影响再调整设计。
2. 每端本地以受限文件权限生成密钥。公钥给对方，私钥仅留本端。最终配置必须保留一个 [Interface] 和本端 PrivateKey；不得机械删除 PrivateKey 或整个 Interface。
本地密钥生成（Linux shell；先进入用户指定的受限工作目录，不覆盖既有密钥）：
```sh
umask 077
wg genkey > privatekey
wg pubkey < privatekey > publickey
``` 
仅交换 publickey 文件内容。配置使用本端私钥时在本机填写，不回传聊天。

3. 双方各保存自己的 wg0.conf（占位符全部替换，文件名采用实际未占用接口名）。

A 端：
```ini
[Interface]
Address = <A_TUNNEL_IPV4>/32
PrivateKey = <A_LOCAL_PRIVATE_KEY>
ListenPort = <A_UDP_PORT>

[Peer]
PublicKey = <B_PUBLIC_KEY>
AllowedIPs = <B_TUNNEL_IPV4>/32
Endpoint = <B_REACHABLE_HOST>:<B_UDP_PORT>
```

B 端：
```ini
[Interface]
Address = <B_TUNNEL_IPV4>/32
PrivateKey = <B_LOCAL_PRIVATE_KEY>
ListenPort = <B_UDP_PORT>

[Peer]
PublicKey = <A_PUBLIC_KEY>
AllowedIPs = <A_TUNNEL_IPV4>/32
```

B 可在认证流量到达后获知 A 的端点；若 B 必须首先发起连接，则补充经确认的 A Endpoint。若要访问对端后方网段，需单独确认该网段、回程路由和转发条件，不能仅把 CIDR 加入模板就宣称可达。AllowedIPs 同时约束来自该 peer 的源地址与发往该 peer 的目标地址。本例双方对等允许对方隧道 /32。
4. 确认 B UDP 监听端口沿实际路径可达；如需调整主机/云/路由器规则，只做用户授权的最小规则并记录回滚，不关闭整个防火墙。A 若在 NAT 后且确有空闲后接收入站需求，可在 A 的 [Peer] 添加 PersistentKeepalive = 25；不需要时省略。
5. 先备份既有同名配置并记录当前路由，勿覆盖正在使用的隧道。配置文件权限设为仅管理员可读。分别执行 `sudo wg-quick up /path/to/wg0.conf`；接口名与路径按实际替换。不要用 wg setconf 直接喂含 Address 等 wg-quick 专有字段的文件。

## E — 输出与完成标准
交付双端模板、逐端字段对照、变更清单和回滚步骤。启动后先记录 `sudo wg show wg0 latest-handshakes` 与 `sudo wg show wg0 transfer`；向对端隧道 IP 发送实际测试流量（例如允许 ICMP 时 ping，否则用用户确认的服务），再读取上述指标。看到近期握手和收发计数变化，并验证目标应用/地址才可称相应路径通过。没有测试流量时握手空白不能单独判故障；握手成功也不等于目标服务可达。无握手：核对双方公钥、私钥配对、端点、UDP路径；握手有流量但目标失败：核对AllowedIPs、路由、回程与应用监听。不从一条超时直接归因封锁。
回滚：分别 `sudo wg-quick down /path/to/wg0.conf`，恢复备份配置与本次单独变更的规则，核对原有连接恢复。没有执行权限或真实网络证据，明确标为“模板核验/待实测”。

## B — 边界
只覆盖 Linux wg-quick 双主机分流，不包含 Windows GUI、移动端、互联网出口 NAT、多网段生产路由或自动开机。默认不设置 DNS、PostUp/Down 脚本、SaveConfig 或全隧道路由。密钥不写入聊天、日志或测试报告；不使用 `wg show ... dump`/`showconf` 采集诊断以免泄密。只给必要的握手与流量指标。其他网络诊断先用相邻的连接排障能力；本卡仅在确认 WireGuard 路径时使用。

