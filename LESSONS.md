# OpenClaw Fork 经验

## 架构

- 自建 AI 助手框架前，先调研成熟开源方案（如 OpenClaw），80% 的管道代码是标准组件
- 如果上游项目已内置你需要的功能（channel adapters），用配置代替代码
- Fork + 扩展包模式优于从零实现：upstream 同步 + 自定义 workspace/skills/plugins

## OpenClaw 运维

- `openclaw.json` 严格校验，未知字段会阻止 Gateway 启动，改完用 `openclaw doctor --fix`
- cron jobs 通过 CLI/Dashboard 创建，也可直接编辑 `~/.openclaw/cron/jobs.json`（调度器会热加载）
- CLI 创建 cron 在 Windows 上可能卡住，直接编辑 jobs.json 更可靠
- 升级后必须手动重启 Gateway 进程（kill + run），并检查 `gateway.cmd` 中的版本号是否需要更新
- Windows 上 `openclaw gateway restart` 可能报 60s 超时（插件加载 ~26s），但 Gateway 实际已启动。等完全初始化后 `openclaw gateway status` 会显示 `RPC probe: ok`
- Gateway 间歇性 AJV 栈溢出（`Maximum call stack size exceeded`）是 plugin provider snapshot 递归加载 bug，在 2026.4.9 修复（upstream #61922）。遇到此类问题优先检查是否有新版本
- Gateway RPC 方法（cron.add 等）走 WebSocket，不是 HTTP REST API
- Windows PowerShell 中 `openclaw` 命令可能显示 `>>` 续行提示，用完整 node 路径或 `.cmd` 后缀
- Dashboard 需要带 token 访问：`http://127.0.0.1:18789/?token=<gateway_token>`
- Telegram 频道首次使用需要 pairing：`openclaw pairing approve telegram <code>`

## Agent 路由

- OpenClaw 的多 Agent 路由是 **bindings 配置驱动**（确定性匹配 channel + peer），不是关键词或 LLM 分类
- 没有 bindings 时所有消息走 default agent；需要在 `openclaw.json` 顶层添加 `bindings` 数组
- 路由优先级：exact peer > wildcard peer > account > channel > default
- Cron announce 不经过路由，binding 不影响 cron delivery
- Dashboard 可通过 URL `?session=agent:<agentId>:main` 直接测试任意 agent

## 工具

- npm 包版本经常不准确，安装前最好先 `pnpm view <pkg> versions` 确认
- pnpm 10.x 的 `onlyBuiltDependencies` 需要在根 package.json 的 `pnpm` 字段中配置

## 飞书集成

- 飞书 Bot 必须先加入目标群，才能通过 API 向该群发消息，否则飞书 API 返回 400。在 cron announce 配置飞书群 ID 前，确认 Bot 已经是群成员
- 排查 delivery 失败时，先检查 Bot 是否在目标群，再看其他原因
- 飞书 Lark SDK 的 AxiosError 会吞掉 API 返回的实际错误码（如 230001 bot not in chat），只显示 `status code 400`。排查时需看 `err.response?.data` 而非 error message
- 飞书 user_access_token 有效期仅约 2 小时，必须记录获取时间戳并实现 refresh_token 自动刷新
- 飞书应用新增 API 权限后需要「创建版本」并「发布」才能生效（免审核权限提交即生效）
- 飞书 OAuth user_access_token 的 scope 在授权时确定，刷新 token 不会获得新 scope。应用新增权限后必须让用户重新走 OAuth 授权流程
- 飞书 OAuth 授权的 redirect_uri 必须在「安全设置」中预先配置，否则返回 20029 错误

## 工作流

- 先建骨架、逐包编译验证，再组装集成，比一次性写完所有代码更高效
- 定期同步上游：`git fetch upstream && git merge upstream/main`
