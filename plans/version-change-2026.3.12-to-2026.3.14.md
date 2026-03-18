# OpenClaw 版本变化对照文档（v2026.3.12 -> 当前构建 2026.3.14）

## 1. 对照范围与结论

- 上次构建基线：`v2026.3.12`
- 当前构建目标：`upstream/main`（`package.json` 版本为 `2026.3.14`）
- 中间已发布版本：`v2026.3.13`（tag `v2026.3.13-1`）

结论：从 `v2026.3.12` 到当前构建，核心变化集中在四个方向。

1. 插件化边界继续前移（provider/channel/runtime 更彻底插件化）。
2. 沙箱与远程执行能力升级（OpenShell + SSH backend）。
3. Web 搜索与浏览器链路增强（Firecrawl、existing-session 能力扩展）。
4. 安全与稳定性密集修复（Webhook、配对授权、路由、会话恢复、平台启动路径）。

## 2. 官方仓库核对依据

以下内容均来自官方仓库 `openclaw/openclaw`：

- 标签对比：`v2026.3.12`、`v2026.3.13-1`、`upstream/main`
- 官方变更文件：`CHANGELOG.md`
- 官方提交历史：`git log v2026.3.12..upstream/main`

官方对照链接：

- 3.12 -> 3.13: https://github.com/openclaw/openclaw/compare/v2026.3.12...v2026.3.13-1
- 3.13 -> 当前 main: https://github.com/openclaw/openclaw/compare/v2026.3.13-1...main
- 3.12 -> 当前 main: https://github.com/openclaw/openclaw/compare/v2026.3.12...main

## 3. 版本增量总览

| 版本段 | 变更条目（Changes） | 修复条目（Fixes） | Breaking |
| --- | ---: | ---: | ---: |
| `v2026.3.13` | 9 | 59 | 1 |
| `upstream/main` 的 `Unreleased`（当前 2026.3.14 构建内容） | 34 | 92 | 4 |

说明：这里是 `CHANGELOG.md` 当前统计值，`Unreleased` 代表“当前 main 构建已经包含、但未打正式 release tag 的内容”。

## 4. 重要功能变化

### 4.1 从 3.12 升到 3.13（已发布）

1. 浏览器 existing-session 与自动化增强。
2. Android 聊天设置界面重构，iOS onboarding 首次引导流程改版。
3. 新增 `OPENCLAW_TZ`，可固定 Docker 时区。
4. Cron 新增 `sessionTarget: "current"` 与 `session:<id>`。
5. Telegram 新增 `--force-document`，可避免图片压缩上传。

### 4.2 从 3.13 到当前 2026.3.14（main）

1. 沙箱体系升级：新增可插拔 sandbox backend，包含 OpenShell（`mirror`/`remote`）与 SSH backend。
2. Web 工具升级：新增 Firecrawl provider 与 `firecrawl_search`/`firecrawl_scrape`。
3. 插件生态升级：支持 Codex/Claude/Cursor bundle 发现与安装，新增 Claude marketplace 安装与更新流。
4. Provider 插件化继续深入：OpenRouter、GitHub Copilot、OpenAI Codex 等运行逻辑迁移到 bundled plugins。
5. 命令面增强：新增 `/btw`、`/plugins`、`/plugin`，并加入 `commands.plugins` gating。
6. 配置能力增强：`config set` 增强 SecretRef / provider builder / JSON 批量赋值 / `--dry-run` 校验。
7. 通道能力增强：Feishu 卡片交互、推理流显示、ACP 会话绑定能力增强，Telegram 新增 topic-edit、silentErrorReplies。
8. 桌面与 UI 改进：Appearance 新增 Roundness 滑杆，聊天视图新增 expand-to-canvas 与会话导航能力。
9. 移动端增强：Android 增加系统跟随暗色主题，Node 侧增加 `callLog.search`。

## 5. 重要 Bug 修复

### 5.1 3.13 的关键修复

1. Dashboard chat 在工具高频回传下的卡顿/重载问题。
2. Gateway RPC 请求悬挂导致 pending promise 泄漏问题。
3. Browser existing-session 生命周期与容错问题。
4. Windows gateway install/stop/status/auth 的多处稳定性问题。
5. Telegram 媒体下载在 IPv4/代理切换下的稳定性问题。
6. macOS daemon onboarding 健康检查误判问题。
7. 安全修复：配对 token 单次使用、webhook secret 校验前置、远程附件路径注入防护等。

### 5.2 当前 2026.3.14（main）的关键修复

1. 插件打包 TDZ 导致插件注册失败（关键启动阻断问题）。
2. Node 25 下 Google 认证链路兼容（gaxios fetch 行为修复）。
3. Gateway 启动时改为优先加载 `dist/extensions`，显著降低冷启动重编译成本。
4. Webhook 路由与插件注册表一致性修复，避免 registry churn 引发 webhook 失效。
5. Control UI 多项稳定性修复（会话路由、会话持久化、日志导入导致白屏等）。
6. WhatsApp reconnect/login 边界修复（重连后增量消息、515 pairing 重启写盘时序）。
7. Telegram 文本分块与 `--force-document` 路径一致性修复。
8. 插件 scoped id、安装优先级、watch-mode manifest 生成问题修复。
9. 多项安全修复（Webhook pre-auth 限制、scope/approval 校验、SecretRef 与配置视图脱敏等）。

## 6. Breaking 变更（升级前必须评估）

1. 只加载一个根 memory 文件：`MEMORY.md` 优先，`memory.md` 仅在前者缺失时使用。
2. 移除 Chrome extension relay 旧路径（转向 existing-session / user 配置）。
3. 移除公开 `openclaw/extension-api`，插件需迁移到 `openclaw/plugin-sdk/*` 与注入 runtime。
4. 消息发现能力要求迁移到 `describeMessageTool(...)`，旧 adapter 方法不再使用。
5. 移除内置 `nano-banana-pro` skill 包装，图像生成改走 `image_generate` 与 `agents.defaults.imageGenerationModel`。

## 7. 升级建议（从 3.12 到当前构建）

1. 先做配置与插件清点。
2. 检查是否依赖 `chrome-relay`、`openclaw/extension-api`、旧 message adapter 方法。
3. 合并根目录 memory 文件，避免双文件注入行为变化。
4. 升级后优先回归以下路径。
5. Gateway 启停、webhook 收发、控制台会话路由、Telegram/Feishu 交互链路、Windows/macOS 启动链路。
6. 对插件仓库单独做一次 SDK 编译检查。
7. 重点核对 `plugin-sdk/*` 导入、runtime 注入 API、marketplace/bundle 安装行为。

## 8. 可执行核验命令

```bash
git fetch upstream --tags
git show v2026.3.12 --no-patch
git show v2026.3.13-1 --no-patch
git show upstream/main:package.json | jq -r '.version'
git log --oneline v2026.3.12..upstream/main | head -n 200
```
