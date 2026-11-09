---
name: Codex/GitHub 网络恢复
description: >-
  Use when Codex shows Reconnecting/Reason Network, GitHub clone is slow or
  times out, OpenAI TLS fails through Clash, or git/curl to foreign hosts hangs
  on this Windows host.
---
# Codex / GitHub network recovery (Clash Verge)

## When
- Codex: `Reconnecting 5/5` + `Reason Network`
- GitHub `git clone` / ZIP download hangs or times out
- OpenAI hosts TLS fail while google/cloudflare work through the same proxy

## Fixed facts on this host
- Proxy: Clash Verge + mihomo, mixed port `127.0.0.1:7897`
- Codex user env file: `C:\Users\Administrator\.codex\.env` (UTF-8 **no BOM**)
- Codex sandbox token cannot write under Administrator; create/edit `.env` **outside** sandbox
- Clash `fake-ip` answers in `198.18.x.x` are normal — not DNS hijack evidence

## `.env` contents
```
HTTP_PROXY=http://127.0.0.1:7897
HTTPS_PROXY=http://127.0.0.1:7897
NO_PROXY=localhost,127.0.0.1,::1
```
After changing `.env`, fully quit and relaunch Codex / VS Code (close window ≠ restart).

## Git / agent Shell for GitHub
Before clone or GitHub HTTPS:
1. Confirm listener: `Get-NetTCPConnection -LocalPort 7897 -State Listen`
2. Set process env `HTTP_PROXY`/`HTTPS_PROXY`/`NO_PROXY` as above
3. Optionally `git config --global http.proxy http://127.0.0.1:7897` (and `https.proxy`)
4. Prefer `git clone --depth 1` or codeload ZIP through the proxy

## Verify (isolate layers)
1. Proxy listening (mihomo owns 7897)
2. Non-OpenAI: `curl.exe -x http://127.0.0.1:7897 -sS -m 15 -o NUL -w "%{http_code} %{time_total}s" https://www.google.com/` → 2xx/3xx ~3s
3. GitHub: same form against `https://github.com/` → 200
4. OpenAI TLS: same against `https://chatgpt.com/backend-api/me` — if instant `curl: (35)` while step 2 passes → **exit node** problem, not Codex config
5. Soak: session ≥30 min without Reconnecting

## Node policy
- If OpenAI TLS resets: switch **within region** first; avoid rapid SG↔JP↔US hopping (account risk)
- Prefer 专线 over 高速 BGP when OpenAI is blocked
- Exclude nodes marked Error; do not reuse a node proven to RST OpenAI TLS
- Keep one working exit for the whole session

## Do not
- Blame Codex config when only OpenAI fails
- Treat Reason Network as root cause (retry label only)
- Edit unrelated project files for a network incident
- Enable global TUN unless required — `.env` is enough for Codex

## Shortcut
Ordinary sites fast + OpenAI/GitHub fail → check mihomo matched node / dial error, then change exit node — not local DNS and not app reinstall.
