# 审计示例

codeless-security-check 生成的实际安全审计结果。

> **关于以下项目的重要说明**
>
> 此处展示的每个项目都是对 Claude Code 和 MCP 生态系统的优秀贡献。我们之所以选择它们，正是因为它们受欢迎、积极维护，且是开发者实际使用的工具的代表性示例。**这些项目中没有任何一个包含恶意代码或故意的安全漏洞。**
>
> "CAUTION" 评级并不表示项目本身存在缺陷——它反映的是本工具的评估：*用户*应在安装前了解软件的运作方式（如 shell 执行、文件系统访问、外部 API 调用）。这些是工具用途的固有特性，而非缺陷。Shell 执行 MCP *必须*执行 shell；Twitter 客户端*必须*调用 Twitter API。我们的审计只是让这些行为可见，以便用户做出知情的决定。
>
> 我们对此处出现的每一位作者和维护者表示深深的敬意。为社区构建开源工具需要付出巨大的努力和慷慨，我们对他们的贡献深表感谢。

---

## anthropics/skills — frontend-design

**目标**: anthropics/skills / frontend-design（技能）
**风险等级**: SAFE

### 作者可信度
- **发布者**: Anthropic
- **账户年龄**: 5年以上（2020年起）
- **信号**: 72,243 stars, 7,394 forks, 27,351 followers
- **评估**: 可信（已知发布者）

### 摘要

纯 Markdown 技能，无脚本、无网络访问、无可执行代码。仅包含前端开发的设计指南和美学方向。无代码技能模式的典范实现。

### 发现

#### Critical（即时威胁）
- 无

#### Warning（潜在风险）
- 无

#### Info（备注）
- `SKILL.md` 仅包含设计指南和美学指示——无可执行代码、shell 命令或外部引用。
- 无脚本、无引用、静态字体外无其他资源。
- 零攻击面。

### 建议
[x] 可安全安装

---

## sunu-py-jp/X-Twitter-MCP

**目标**: sunu-py-jp/X-Twitter-MCP（MCP 服务器）
**风险等级**: CAUTION

### 作者可信度
- **发布者**: sunu-py-jp
- **账户年龄**: 不到1个月（2026年2月起）
- **信号**: 0 stars, 0 forks, 0 followers, MIT license
- **评估**: 低信任（新账户，无社区信号）

### 摘要

提供 Twitter/X API v2 完整访问的 TypeScript MCP 服务器。使用官方 `twitter-api-v2` 库的干净实现，通过 Zod 模式进行适当的输入验证。未检测到恶意模式，但服务器具有强大功能（发推、发送私信、管理关注），需要谨慎配置。

### 发现

#### Critical
- 无

#### Warning
- `src/client.ts:9-13` — 从环境变量读取4个 API 凭证（`X_API_KEY`、`X_API_SECRET`、`X_ACCESS_TOKEN`、`X_ACCESS_TOKEN_SECRET`）。按预期发送到 Twitter API，但用户需确保凭证权限范围适当。
- `src/tools/dm.ts:7-28` — `send_dm` 工具可向任何用户发送私信。如果 MCP 服务器被不受信任的客户端访问，可能被滥用。
- `src/tools/tweets.ts:6-31` — `post_tweet` 工具可在已认证账户发布推文。用户应注意此写入功能。
- 作者信任度低（无社区信号的新账户）。与上述发现的交叉关联提升了整体注意级别。

#### Info
- 客户端、服务器、工具分离清晰的良好代码结构。
- `src/server.ts:36-51` — `X_ENABLED_GROUPS` 和 `X_DISABLED_TOOLS` 环境变量允许细粒度工具访问控制。优秀的安全设计。
- 所有工具输入均通过 Zod 模式验证。
- 依赖最少且知名（`@modelcontextprotocol/sdk`、`twitter-api-v2`、`zod`）。
- 未检测到 `eval`、`exec`、`child_process`、混淆代码或数据泄露模式。

### 建议
[x] 谨慎安装 — 除非明确需要写入访问，使用 `X_ENABLED_GROUPS` 限制为只读工具（如 `tweets:get,timelines:get`）。检查 API 令牌权限。

---

## tumf/mcp-shell-server

**目标**: tumf/mcp-shell-server（MCP 服务器）
**风险等级**: CAUTION

### 作者可信度
- **发布者**: tumf
- **账户年龄**: 15年以上（2009年起）
- **信号**: 166 stars, 42 forks, 97 followers, MIT license
- **评估**: 可信

### 摘要

采用命令白名单机制的 shell 命令执行 MCP 服务器。通过白名单强制、shell 操作符阻止和参数转义实现适当的命令验证逻辑。安全性取决于用户对 `ALLOW_COMMANDS` 环境变量的配置。

### 发现

#### Critical
- 无

#### Warning
- `src/mcp_shell_server/process_manager.py:160` — 使用 `asyncio.create_subprocess_shell()`（等同于 shell=True）。虽然命令已预先验证，但 shell 解释可能引入绕过向量。
- `src/mcp_shell_server/command_validator.py:24-27` — 安全边界依赖于 `ALLOW_COMMANDS` 环境变量。过于宽泛的配置（如允许 `bash`、`sh`、`python`）将使所有保护失效。

#### Info
- 命令白名单验证和 shell 操作符阻止（`; && ||`）已正确实现。
- 管道命令已验证——管道中的每个命令都单独进行白名单检查。
- 使用 `shlex.quote()` 进行参数转义。
- 未检测到数据泄露、混淆代码或提示注入。

### 建议
[x] 谨慎安装 — 安全性依赖严格的 `ALLOW_COMMANDS` 配置。不要将解释器（`bash`、`sh`、`python`）加入白名单。

---

## mark3labs/mcp-filesystem-server

**目标**: mark3labs/mcp-filesystem-server（MCP 服务器）
**风险等级**: SAFE

### 作者可信度
- **发布者**: mark3labs
- **账户年龄**: 9年以上（2016年起）
- **信号**: 599 stars, 98 forks, 198 followers, MIT license
- **评估**: 可信

### 摘要

用 Go 实现的优秀文件系统 MCP 服务器。通过包含符号链接解析的路径验证，将所有操作正确沙箱化到允许的目录中。未发现网络通信或数据泄露向量。

### 发现

#### Critical
- 无

#### Warning
- 无

#### Info
- `filesystemserver/handler/helper.go:42-86` — `validatePath()` 转换为绝对路径、检查允许目录、解析符号链接以防止遍历攻击。
- `filesystemserver/handler/helper.go:15-40` — `isPathInAllowedDirs()` 使用尾部分隔符检查防止前缀攻击（如 `/tmp/foo` 不会匹配 `/tmp/foobar`）。
- `filesystemserver/handler/delete_file.go:96` — 可通过 `os.RemoveAll()` 进行递归删除，但需要显式 `recursive=true` 标志。
- 无网络调用、无外部数据传输、无混淆代码。

### 建议
[x] 可安全安装

---

## daymade/claude-code-skills

**目标**: daymade/claude-code-skills（技能合集）
**风险等级**: CAUTION

### 作者可信度
- **发布者**: daymade
- **账户年龄**: 12年以上（2013年起）
- **信号**: 592 stars, 67 forks, 160 followers, MIT license
- **评估**: 可信

### 摘要

涵盖广泛用例的30多个 Claude Code 技能大型合集。部分技能包含访问外部 API 和从环境变量读取 API 凭证的脚本。未检测到恶意模式。合集规模较大，建议使用前逐个审查技能。

### 发现

#### Critical
- 无

#### Warning
- `twitter-reader/scripts/fetch_tweet.py:24,34-36` — 从环境变量读取 `JINA_API_KEY`，通过 curl 发送到 `r.jina.ai`。合法 API 使用但涉及凭证外部传输。
- `cloudflare-troubleshooting/scripts/check_cloudflare_config.py:38-87` — 使用 `requests` 库向外部端点发送 HTTP 请求。读取 Cloudflare API 凭证。
- `cloudflare-troubleshooting/scripts/fix_ssl_mode.py:52-95` — 通过 API 修改 Cloudflare SSL 配置。对外部服务具有写入访问权限。

#### Info
- 多个技能使用 `subprocess`（markdown-tools、cli-demo-generator、youtube-downloader、skill-creator）——均为合法使用，无 shell 注入风险。
- `macos-cleaner/scripts/safe_delete.py:99` — 保护敏感目录（.ssh、credentials）免被删除。
- 未检测到混淆代码、base64 负载或提示注入模式。
- 大型合集——建议安装前逐个审查技能。

### 建议
[x] 谨慎安装 — 使用前逐个审查技能。具有外部 API 访问的技能（twitter-reader、cloudflare-troubleshooting）需要通过环境变量提供 API 凭证。

---

## alirezarezvani/claude-skills

**目标**: alirezarezvani/claude-skills（技能合集）
**风险等级**: CAUTION

### 作者可信度
- **发布者**: alirezarezvani
- **账户年龄**: 12年以上（2013年起）
- **信号**: 1,889 stars, 228 forks, 213 followers, MIT license
- **评估**: 可信

### 摘要

涵盖工程、商业、营销和合规领域的最大 Claude Code 技能合集（50多个技能）。脚本主要执行本地分析和代码生成。未发现恶意模式。合集规模使完整审查不切实际——按需审查个别技能。

### 发现

#### Critical
- 无

#### Warning
- `engineering-team/senior-backend/scripts/api_load_tester.py:26-28` — 使用 `urllib` 向用户指定的端点发送 HTTP 请求。用于负载测试但可指向任意 URL。
- 合集包含50多个技能中的100多个 Python 脚本——完全手动审查不切实际。建议安装个别技能而非整个合集。

#### Info
- `engineering-team/senior-security/scripts/secret_scanner.py:384` — 使用 `__import__('datetime')`，非常规模式但无害（仅导入 datetime）。
- `engineering/tech-debt-tracker/assets/sample_codebase/src/payment_processor.py` — 包含 `requests.post()` 调用，但这是示例/演示文件，非可执行技能代码。
- `engineering/migration-architect/scripts/rollback_generator.py` — 包含 curl 命令，但这些是生成脚本的模板字符串，非直接执行。
- 未检测到数据泄露、混淆代码或提示注入模式。

### 建议
[x] 谨慎安装 — 建议安装个别技能而非整个合集。使用前审查脚本，特别是 engineering-team/senior-backend 中的脚本。
