# OpenClaw Fork 经验

## 架构

- 自建 AI 助手框架前，先调研成熟开源方案（如 OpenClaw），80% 的管道代码是标准组件
- 如果上游项目已内置你需要的功能（channel adapters），用配置代替代码
- Fork + 扩展包模式优于从零实现：upstream 同步 + 自定义 workspace/skills/plugins

## OpenClaw 运维

- `openclaw.json` 严格校验，未知字段会阻止 Gateway 启动，改完用 `openclaw doctor --fix`
- cron jobs 通过 CLI/Dashboard 创建，不在 JSON 配置中定义
- 升级后必须手动重启 Gateway 进程（kill + run）
- Windows PowerShell 中 `openclaw` 命令可能显示 `>>` 续行提示，用完整 node 路径或 `.cmd` 后缀
- Dashboard 需要带 token 访问：`http://127.0.0.1:18789/?token=<gateway_token>`
- Telegram 频道首次使用需要 pairing：`openclaw pairing approve telegram <code>`

## 工具

- npm 包版本经常不准确，安装前最好先 `pnpm view <pkg> versions` 确认
- pnpm 10.x 的 `onlyBuiltDependencies` 需要在根 package.json 的 `pnpm` 字段中配置

## 工作流

- 先建骨架、逐包编译验证，再组装集成，比一次性写完所有代码更高效
- 定期同步上游：`git fetch upstream && git merge upstream/main`
