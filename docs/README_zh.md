# codeless-security-check

[English](../README.md) | [日本語](README_ja.md) | [한국어](README_ko.md) | 中文

Claude Code 安全审计技能。在安装前检查技能和 MCP 服务器的安全漏洞。

## 检查内容

- 恶意代码执行 (eval, exec, shell 注入)
- 数据泄露 (凭证、环境变量发送到外部服务器)
- 提示注入 (隐藏指令、Unicode 技巧)
- 过度权限请求 (广泛的文件访问、危险标志)
- 作者可信度验证 (账户年龄、仓库信号)
- 交叉关联分析 (组合多个发现来检测真实威胁)

## 设计理念

**无脚本。无代码。仅 Markdown。**

不包含任何可执行代码。通过向 Claude 提供结构化的检查清单、威胁模式和升级规则，利用 Claude 的推理能力进行分析。

为什么无代码？

- **零攻击面** — 带有脚本的安全工具本身就是一个讽刺的攻击向量。这个技能无法被入侵。
- **未知威胁检测** — 正则扫描器只能发现已知模式。Claude 能推理意图，捕获模式匹配器无法检测的威胁。
- **上下文高效** — 总计 ~13KB。不会占用过多上下文窗口。

## 结构

```
codeless-security-check/
  SKILL.md              # 6步工作流程
  references/
    skill-checklist.md   # .skill 包检查清单
    mcp-checklist.md     # MCP 服务器检查清单
    threat-patterns.md   # 带代码示例的已知攻击模式
```

## 安装

```bash
npx skills add https://github.com/sunu-py-jp/codeless-security-check --skill codeless-security-check
```

或手动安装:

```bash
cp -r codeless-security-check ~/.claude/skills/
```

## 使用方法

向 Claude Code 提问:

- "Check the security of this skill: ~/.claude/skills/some-skill"
- "Is this MCP server safe? https://github.com/org/some-mcp"
- "Review this .skill file for vulnerabilities"

## 审计示例

实际审计结果请参阅 [EXAMPLES_zh.md](EXAMPLES_zh.md):

| 目标 | 类型 | 风险等级 |
|------|------|----------|
| [anthropics/skills — frontend-design](https://github.com/anthropics/skills) | 技能 | SAFE |
| [sunu-py-jp/X-Twitter-MCP](https://github.com/sunu-py-jp/X-Twitter-MCP) | MCP 服务器 | CAUTION |
| [tumf/mcp-shell-server](https://github.com/tumf/mcp-shell-server) | MCP 服务器 | CAUTION |
| [mark3labs/mcp-filesystem-server](https://github.com/mark3labs/mcp-filesystem-server) | MCP 服务器 | SAFE |
| [daymade/claude-code-skills](https://github.com/daymade/claude-code-skills) | 技能合集 | CAUTION |
| [alirezarezvani/claude-skills](https://github.com/alirezarezvani/claude-skills) | 技能合集 | CAUTION |

## 许可证

[MIT](../LICENSE)
