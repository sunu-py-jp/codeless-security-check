# security-check

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
security-check/
  SKILL.md              # 6-step workflow
  references/
    skill-checklist.md   # Checklist for .skill packages
    mcp-checklist.md     # Checklist for MCP servers
    threat-patterns.md   # Known attack patterns with code examples
```

## Install

```bash
npx skills add sunu-py-jp/security-check-skill --skill security-check
```

Or manually:

```bash
cp -r security-check ~/.claude/skills/
```

## Usage

Ask Claude Code:

- "Check the security of this skill: ~/.claude/skills/some-skill"
- "Is this MCP server safe? https://github.com/org/some-mcp"
- "Review this .skill file for vulnerabilities"

## License

[MIT](LICENSE)
