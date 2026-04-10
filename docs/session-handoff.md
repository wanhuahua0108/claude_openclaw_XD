# OpenClaw Fork - Session Handoff

## 最新状态（2026-04-10 Session 12）

### 项目背景

从自建 Claw 项目迁移到 Fork OpenClaw 上游。
原因：自建项目 ~80% 代码是标准 AI 框架管道，OpenClaw 已全部内置且更成熟。

### 已完成

1. **Fork OpenClaw** → <https://github.com/wanhuahua0108/claude_openclaw_XD>
2. **Clone 到本地** → `d:\AI\Claude\claude_openclaw_XD\`（已配置 upstream remote）
3. **升级 OpenClaw** 从 2026.3.7 → 2026.4.5 → **2026.4.9**
4. **Telegram 频道** — 已配置、已配对（user id: 8182698647）、已验证收发消息
5. **飞书频道** — 已有配置，插件已注册（doc/chat/wiki/drive/bitable）
6. **配置修复** — `openclaw doctor --fix` 迁移旧配置格式，手动清理残留字段
7. **多 Agent 路由** — 4 个 Agent：日常助手（Sonnet）、编码助手（Opus）、内容创作（Sonnet）、研究助手（Sonnet）
8. **7 个自定义 Skills** — chinese_assistant, daily_briefing, content_creator, feishu_workspace, feishu_calendar, weekly_report, hot_topic_scan
9. **Workspace 人设** — IDENTITY.md, SOUL.md, BOOT.md, USER.md, TOOLS.md, HEARTBEAT.md, MEMORY.md, AGENTS.md
10. **清理与重命名** — 删除旧自建项目（本地 + GitHub），GitHub 仓库 `openclaw` → `claude_openclaw_XD`，git remote 已更新，本地目录已重命名
11. **Dashboard i18n** — 确认 OpenClaw Dashboard 已内置 13 语言支持（含简体中文），可在 Overview 页面切换
12. **同步上游 Fork** — 已同步两次：初次 2026-04-07 + Session 5 再次同步（37 commits，含 plugin boundary、dreaming、dedup refactors 等）
13. **3 个 Cron Jobs** — daily-briefing(09:00)、hot-topic-scan(10:00)、weekly-report(周五18:00)，全部 announce 到飞书群 HH Email推送(oc_6cb183ccb932ea6b4330a58c22f0d387)
14. **Dashboard 全模块测试** — 聊天、频道（Telegram 在线）、代理（4个）、技能（7 个自定义）、定时任务（3 个）均正常
15. **企业微信插件安装** — `@wecom/wecom-openclaw-plugin` v2026.4.3 已安装，Bot 模式（WebSocket），待配置凭证（Bot ID + Secret）
16. **Cron Delivery 修复** — 飞书 Bot「HH小助手」加入「HH Email推送」群后，delivery 从 400 error 恢复为 delivered。daily-briefing 手动触发验证通过，完整简报已发送到群
17. **Cron 自动触发验证** — 确认 daily-briefing (09:00) 和 hot-topic-scan (10:00) 按时自动触发，内容生成正常。4/9 09:00 前因 Bot 不在群导致 delivery 失败，修复后 hot-topic-scan 错误状态已手动清理
18. **Dashboard Agent 测试** — 4 个 Agent 全部通过 Dashboard 独立测试：main(Sonnet)、coder(Opus)、creator(Sonnet)、researcher(Sonnet)，各 Agent 正确识别自身身份和模型
19. **Agent 路由 Bindings** — 已配置 3 条路由规则：Telegram DM → coder(Opus)、飞书群 HH Email → creator、飞书 DM → main。Gateway 重启后配置生效，待频道实际测试验证
20. **Channel Routing 端到端验证（Session 9）** — 3 条路由规则全部验证通过：Telegram(OK, 449ms)、Feishu(OK, WebSocket)、3 个 Agent 均正确响应并识别自身身份
21. **Cron 投递状态确认（Session 9）** — 确认 4/8-4/9 的 AxiosError 400 根因是 Bot 未入群（Session 7 已修复）。修复后 daily-briefing 投递成功，hot-topic-scan 明天应恢复正常
22. **飞书日历集成完成（Session 10）** — 全流程完成：
    - 飞书安全设置添加 redirect_uri `http://localhost:3000/callback`
    - OAuth 重新授权含 calendar scope（calendar:calendar:read, calendar:calendar.event:read, calendar:calendar.event:create, calendar:calendar.free_busy:read）
    - feishu-calendar.js 脚本修复（getPrimaryCalendar API 响应解析）
    - 脚本验证通过：今日/本周/JSON 模式均正常（日历：万方）
    - feishu_calendar skill 创建并在 Dashboard 确认加载（Workspace Skills 第 7 个）
    - daily_briefing skill 更新：日程提醒板块改用日历脚本
    - TOOLS.md 更新：新增 feishu_calendar，更新权限列表
23. **OAuth 工具脚本（Session 10）** — `~/.openclaw/workspace/scripts/feishu-oauth.js` 创建，支持自动启动回调服务器 + 浏览器授权 + token 保存，显式请求 calendar scope
24. **AJV 栈溢出修复（Session 11）** — 升级 OpenClaw 2026.4.5 → 2026.4.9 修复了 Gateway config 读取时 ajv schema 验证器的递归栈溢出。根因：plugin provider snapshot 递归加载（upstream #61922, #61938, #61946, #61951）。升级后 Gateway 干净启动，无 `Maximum call stack size exceeded`，插件只注册一次（之前注册 3 次）
25. **Cron 恢复确认（Session 11）** — Gateway 升级后 3 个 cron job 全部恢复 ok 状态。weekly-report 在重启后自动补跑（missed job recovery），daily-briefing 和 hot-topic-scan 下次按时触发
26. **Cron 自动触发全面验证（Session 12）** — 3 个 cron job 在 4/10 全部自动触发并投递成功：
    - hot-topic-scan 10:00 准时触发（升级 2026.4.9 后首次正常自动触发）
    - daily-briefing 14:07 触发（Gateway 重启后补跑）
    - weekly-report 18:09 触发（missed job recovery 补跑）
    - 全部 `consecutiveErrors: 0`，`lastDeliveryStatus: delivered`
    - **AJV 栈溢出问题彻底关闭**

### 当前运行状态

- Gateway: `http://127.0.0.1:18789` (v2026.4.9) — **运行正常，RPC probe ok**
- Dashboard: `http://127.0.0.1:18789/?token=<gateway_token>`
- Telegram: 已配置，polling 模式，dmPolicy=pairing, groupPolicy=open
- 飞书: 已配置，websocket 模式
- 企业微信: 插件已安装，待配置凭证
- Cron: 已启用，3 个任务（daily-briefing 09:00 / hot-topic-scan 10:00 / weekly-report 周五18:00 → 飞书 HH Email推送）
- Gateway 启动方式: `openclaw gateway start`（Windows 计划任务）

### 暂缓

- **企业微信** — 插件已安装（`@wecom/wecom-openclaw-plugin` v2026.4.3），但需要企业微信管理后台账号。用户当前微信未关联企业，注册需营业执照+审核。Agent 模式还需公网回调 URL。条件成熟后只需填凭证+重启 Gateway 即可启用。

### 已知问题

- **Gateway restart 超时** — Windows 上 `openclaw gateway restart` 偶尔报 60s 超时，但实际 Gateway 已正常启动。原因是启动阶段（~26s 加载插件）CLI 健康检查计时器超时。可忽略，等完全启动后 `openclaw gateway status` 即可确认 `RPC probe: ok`。
- ~~**Config read 栈溢出（Session 10）**~~ — **已修复（Session 11）**，Cron 自动触发验证通过（Session 12）。彻底关闭。
- **wecom-openclaw-plugin SDK 兼容警告** — `OPENCLAW_PLUGIN_SDK_COMPAT_DEPRECATED` 警告，不影响功能，需等 wecom 插件作者迁移到新 SDK subpath imports。
- **Skills symlink 警告** — 9 个 openclaw-managed skills 因 symlink 解析到 `~/.agents/skills/` 而被跳过。这些 skills 通过 `.agents` 路径加载，功能不受影响。

### 下一步（最高优先）

1. **Fork 同步** — 当前 fork 落后 upstream，定期 `git fetch upstream && git merge upstream/main`
2. **Security audit** — `openclaw security audit` 报告 5 个 CRITICAL（groupPolicy=open），建议收紧 Telegram/飞书的 groupPolicy 为 allowlist
3. **wecom 插件升级** — 等待 `@wecom/wecom-openclaw-plugin` 发布兼容 2026.4.9 SDK 的新版本
4. **企业微信（待条件成熟）** — 注册企业微信 → 创建自建应用 → 获取凭证 → 填入配置

### 关键文件

| 文件 | 用途 |
|------|------|
| `~/.openclaw/openclaw.json` | 主配置（channels, agents, plugins） |
| `~/.openclaw/.env` | API keys（ANTHROPIC_API_KEY, TELEGRAM_BOT_TOKEN） |
| `~/.openclaw/workspace/IDENTITY.md` | Claw 身份 |
| `~/.openclaw/workspace/SOUL.md` | 爆款操盘手人设 |
| `~/.openclaw/workspace/BOOT.md` | Gateway 启动检查 |
| `~/.openclaw/workspace/TOOLS.md` | 可用工具能力列表 |
| `~/.openclaw/workspace/skills/` | 7 个自定义 skills |
| `~/.openclaw/workspace/scripts/feishu-calendar.js` | 飞书日历读取脚本 |
| `~/.openclaw/workspace/scripts/feishu-oauth.js` | 飞书 OAuth 授权工具 |
| `~/.openclaw/workspace/feishu_user_token.json` | 飞书 user token（含 calendar scope） |
| `~/.openclaw/cron/jobs.json` | 3 个 cron 任务定义 |
| `~/.openclaw/delivery-queue/` | Delivery 队列（stale 文件已清理，Session 11 后为空） |
| `d:\AI\Claude\claude_openclaw_XD\` | Fork 本地副本（开发用） |

### 旧项目

已删除（本地 `claude_openclaw` 目录 + GitHub `claude_openclaw_XD` 旧仓库均已清理）。

## GitHub

- Fork: <https://github.com/wanhuahua0108/claude_openclaw_XD>
- Upstream: <https://github.com/openclaw/openclaw>
