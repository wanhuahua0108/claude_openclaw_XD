# OpenClaw Fork - Session Handoff

## 最新状态（2026-04-07 Session 2）

### 项目背景

从自建 Claw 项目迁移到 Fork OpenClaw 上游。
原因：自建项目 ~80% 代码是标准 AI 框架管道，OpenClaw 已全部内置且更成熟。

### 已完成

1. **Fork OpenClaw** → <https://github.com/wanhuahua0108/claude_openclaw_XD>
2. **Clone 到本地** → `d:\AI\Claude\claude_openclaw_XD\`（已配置 upstream remote）
3. **升级 OpenClaw** 从 2026.3.7 → **2026.4.5**
4. **Telegram 频道** — 已配置、已配对（user id: 8182698647）、已验证收发消息
5. **飞书频道** — 已有配置，插件已注册（doc/chat/wiki/drive/bitable）
6. **配置修复** — `openclaw doctor --fix` 迁移旧配置格式，手动清理残留字段
7. **多 Agent 路由** — 日常助手（Sonnet）+ 编码助手（Opus）
8. **4 个自定义 Skills** — chinese_assistant, daily_briefing, content_creator, feishu_workspace
9. **Workspace 人设** — IDENTITY.md, SOUL.md, BOOT.md, USER.md, TOOLS.md, HEARTBEAT.md, MEMORY.md, AGENTS.md
10. **清理与重命名** — 删除旧自建项目（本地 + GitHub），GitHub 仓库 `openclaw` → `claude_openclaw_XD`，git remote 已更新
11. **Dashboard i18n** — 确认 OpenClaw Dashboard 已内置 13 语言支持（含简体中文），可在 Overview 页面切换

### 当前运行状态

- Gateway: `http://127.0.0.1:18789` (v2026.4.5)
- Dashboard: `http://127.0.0.1:18789/?token=<gateway_token>`
- Telegram: 已连接，dmPolicy=pairing, groupPolicy=open
- 飞书: 已连接，websocket 模式

### 待手动完成

- **本地目录重命名**: `ren "D:\AI\Claude\openclaw" "claude_openclaw_XD"`（Claude Code 进程锁定，需关闭会话后执行）

### 下一步

1. **Dashboard 测试** — 通过 Dashboard UI 测试各模块（Channels/Sessions/Skills/Agents/Cron Jobs）
2. **Cron Job** — 创建每日简报定时任务
3. **企业微信** — 在 openclaw.json 中启用 WeChat 频道
4. **Fork 管理** — 定期 `git fetch upstream && git merge upstream/main`
5. **上游贡献** — 将通用改进 PR 回 openclaw/openclaw

### 关键文件

| 文件 | 用途 |
|------|------|
| `~/.openclaw/openclaw.json` | 主配置（channels, agents, plugins） |
| `~/.openclaw/.env` | API keys（ANTHROPIC_API_KEY, TELEGRAM_BOT_TOKEN） |
| `~/.openclaw/workspace/IDENTITY.md` | Claw 身份 |
| `~/.openclaw/workspace/SOUL.md` | 爆款操盘手人设 |
| `~/.openclaw/workspace/BOOT.md` | Gateway 启动检查 |
| `~/.openclaw/workspace/skills/` | 4 个自定义 skills |
| `d:\AI\Claude\claude_openclaw_XD\` | Fork 本地副本（开发用） |

### 旧项目

已删除（本地 `claude_openclaw` 目录 + GitHub `claude_openclaw_XD` 旧仓库均已清理）。

## GitHub

- Fork: <https://github.com/wanhuahua0108/claude_openclaw_XD>
- Upstream: <https://github.com/openclaw/openclaw>
