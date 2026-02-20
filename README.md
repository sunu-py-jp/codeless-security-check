# codeless-security-check

English | [日本語](docs/README_ja.md) | [한국어](docs/README_ko.md) | [中文](docs/README_zh.md)

Security audit skill for Claude Code. Evaluates skills and MCP servers for vulnerabilities before installation.

## What it checks

- Malicious code execution (eval, exec, shell injection)
- Data exfiltration (credentials, env vars sent to external servers)
- Prompt injection (hidden instructions, unicode tricks)
- Excessive permissions (broad file access, dangerous flags)
- Author trustworthiness (account age, repo signals)
- Cross-correlation (combining findings to detect real threats)

## Design philosophy

**No scripts. No code. Markdown only.**

This skill contains zero executable code. It works by giving Claude structured checklists, threat patterns, and escalation rules — then letting Claude's reasoning do the analysis.

Why codeless?

- **Zero attack surface** — A security tool with its own scripts is an ironic attack vector. This skill can't be compromised.
- **Novel threat detection** — Regex scanners only find known patterns. Claude can reason about intent and catch threats no pattern matcher would.
- **Context efficient** — ~13KB total. Fits comfortably in the context window alongside everything else.

## Structure

```
codeless-security-check/
  SKILL.md              # 6-step workflow
  references/
    skill-checklist.md   # Checklist for .skill packages
    mcp-checklist.md     # Checklist for MCP servers
    threat-patterns.md   # Known attack patterns with code examples
```

## Install

```bash
npx skills add https://github.com/sunu-py-jp/codeless-security-check --skill codeless-security-check
```

Or manually:

```bash
cp -r codeless-security-check ~/.claude/skills/
```

## Usage

Ask Claude Code:

- "Check the security of this skill: ~/.claude/skills/some-skill"
- "Is this MCP server safe? https://github.com/org/some-mcp"
- "Review this .skill file for vulnerabilities"

## Examples

See [EXAMPLES.md](docs/EXAMPLES.md) for real-world audit results:

| Target | Type | Risk Level |
|--------|------|------------|
| [anthropics/skills — frontend-design](https://github.com/anthropics/skills) | Skill | SAFE |
| [sunu-py-jp/X-Twitter-MCP](https://github.com/sunu-py-jp/X-Twitter-MCP) | MCP Server | CAUTION |
| [tumf/mcp-shell-server](https://github.com/tumf/mcp-shell-server) | MCP Server | CAUTION |
| [mark3labs/mcp-filesystem-server](https://github.com/mark3labs/mcp-filesystem-server) | MCP Server | SAFE |
| [daymade/claude-code-skills](https://github.com/daymade/claude-code-skills) | Skill Collection | CAUTION |
| [alirezarezvani/claude-skills](https://github.com/alirezarezvani/claude-skills) | Skill Collection | CAUTION |

## License

[MIT](LICENSE)
