## R — 识别

在已有、获授权的 SSH 服务上，按需建立本地 SOCKS 动态转发或单个 TCP 目标转发，并验证与关闭。不是安装 sshd、远程转发或全局 VPN。

## I — 核心机制与来源

原书 `proxy/SSH-Tunnel.md` 提供“SSH登录→转发→应用代理→测试”的结构。以下命令与边界是官方文档补充，不能署为原书原有步骤。

[ssh(1)](https://man.openbsd.org/ssh)：`-D` 建立 SOCKS4/5 入口，`-L` 将本地 TCP 连接送到服务器侧目标，`-N` 不执行远程命令。目标相对于服务器，监听相对于客户端。

[ssh_config(5)](https://man.openbsd.org/ssh_config)：`ExitOnForwardFailure=yes` 检查转发建立，不能保证目标服务可达；保留主机密钥核验，首次指纹与管理员提供值核对，变化则停止核查。

## A1 — 合成演练（非原书案例，未实际联网部署）

输入：已核实主机指纹的 `alice@ssh.example.net:22`、密钥本地路径、空闲本地端口1080、测试URL `https://example.com/`。预期：输出仅绑定环回地址的命令和目标请求结果记录；不得把仍运行且无输出的 SSH 当成功证明。若1080被占用，记录冲突、停止该尝试，选获确认的其他空闲端口，不终止未知进程。

## A2 — 应用与邻近能力

触发：“有SSH账号，给单个应用开SOCKS”“把远端内部TCP服务转到本机”。先调用此卡。若已有代理但不通，交给入口/代理链排障卡；若要 WireGuard 整机路由，使用 WireGuard 卡。

## E — 输入、执行与验收

必需输入：SSH主机、端口、用户、认证方式（密钥路径或交互认证，不收集密码/私钥正文）、可信主机指纹来源、本地监听端口、测试URL。`-L` 另需服务器可达的目标主机与端口、应用协议。缺任一项，列出缺口并停止生成可直接执行的命令；只可给未执行模板。

以下为 PowerShell 模板，花括号值须以已核实输入替换。先确认 `ssh -V`、`curl.exe --version` 可用，并检查监听端口没有冲突；本轮只编制方案，不执行部署。

```powershell
# 动态SOCKS；保持前台，按认证提示操作
ssh -N -D 127.0.0.1:1080 -p {SSH_PORT} -o ExitOnForwardFailure=yes -o StrictHostKeyChecking=ask {USER}@{SSH_HOST}
# 使用密钥时在目标前加入：-i '{LOCAL_KEY_PATH}'
# 第二个终端验证，替换为已确认的URL
curl.exe --proxy socks5h://127.0.0.1:1080 --noproxy '' --connect-timeout 10 --max-time 30 '{TEST_URL}'
```

[curl官方手册](https://curl.se/docs/manpage.html)：`socks5h` 让代理解析目标名称，`socks5` 使用本地解析；显式代理的测试还须排除 `NO_PROXY` 绕过。记录退出码、HTTP状态及符合预期的内容，不能只检查进程存在。

```powershell
# 单一TCP目标，例：本地18080→服务器可达目标的80端口
ssh -N -L 127.0.0.1:{LOCAL_PORT}:{TARGET_HOST}:{TARGET_PORT} -p {SSH_PORT} -o ExitOnForwardFailure=yes -o StrictHostKeyChecking=ask {USER}@{SSH_HOST}
# 在第二个终端，以实际HTTP服务验证；非HTTP服务使用其原生客户端
curl.exe --noproxy '*' --connect-timeout 10 --max-time 30 http://127.0.0.1:{LOCAL_PORT}/
```

HTTPS目标不能随意改成localhost后忽略证书错误；保留目标域名对应的TLS/SNI验证条件，或换用该服务支持的测试客户端。失败时用 `ssh -v` 定位认证、监听或通道失败，日志删去敏感信息。

完成标准：主机身份已核对；监听仅本机可用；目标应用实际响应符合预期；结果记录分别列“命令配置核对”和“联网验证”，未运行则标“未实测”。结束时在前台 SSH 窗口按 Ctrl+C，确认该监听消失；若改过应用代理，恢复原值。

## B — 边界与停止条件

[sshd_config(5)](https://man.openbsd.org/sshd_config)：服务端 `AllowTcpForwarding`、`PermitOpen` 或 `DisableForwarding` 可限制转发，须由管理员核对；客户端不能假定可绕过。认证成功也不保证转发许可。

不使用 `StrictHostKeyChecking=no`、不删除已变更密钥来强行继续；未知指纹或服务器授权不明时停止。本卡仅监听127.0.0.1，不额外开启 `-g` 或公网入口；不设置agent转发，不改sshd配置，不承诺UDP或整机流量接管。命令模板中的1080/18080是演练值，实际端口须来自用户输入。不得将本轮文档补核表述为真实隧道已连通。

