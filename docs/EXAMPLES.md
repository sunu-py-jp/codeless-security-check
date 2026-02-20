# Audit Examples

Real-world security audit results produced by codeless-security-check.

> **Important note regarding the projects listed below**
>
> Every project featured here is a well-crafted, valuable contribution to the Claude Code and MCP ecosystem. We selected them precisely because they are popular, actively maintained, and representative of the kinds of tools developers actually use. **None of these projects contain malicious code or intentional vulnerabilities.**
>
> The "CAUTION" rating does not indicate a flaw in the project itself — it reflects this tool's assessment that *the user* should understand how the software operates (e.g., shell execution, file system access, external API calls) before installing it. These are inherent characteristics of the tool's purpose, not defects. A shell executor *must* execute shells; a Twitter client *must* call the Twitter API. Our audit simply surfaces these behaviors so users can make informed decisions.
>
> We have deep respect for every author and maintainer whose work appears here. Building open-source tools for the community takes significant effort and generosity, and we are grateful for their contributions. Thank you.

---

## anthropics/skills — frontend-design

**Target**: anthropics/skills / frontend-design (Skill)
**Risk Level**: SAFE

### Author Trust
- **Publisher**: Anthropic
- **Account age**: 5+ years (since 2020)
- **Signals**: 72,243 stars, 7,394 forks, 27,351 followers
- **Assessment**: Trusted (known publisher)

### Summary

A pure markdown skill with no scripts, no network access, and no executable code. Contains only design guidelines and aesthetic direction for frontend development. An exemplary demonstration of the codeless skill pattern.

### Findings

#### Critical
- None

#### Warning
- None

#### Info
- `SKILL.md` contains only design guidelines and aesthetic instructions — no executable code, no shell commands, no external references.
- No scripts, no references, no assets beyond static fonts (in sibling canvas-design skill).
- Zero attack surface.

### Recommendation
[x] Safe to install

---

## sunu-py-jp/X-Twitter-MCP

**Target**: sunu-py-jp/X-Twitter-MCP (MCP Server)
**Risk Level**: CAUTION

### Author Trust
- **Publisher**: sunu-py-jp
- **Account age**: < 1 month (since 2026-02)
- **Signals**: 0 stars, 0 forks, 0 followers, MIT license
- **Assessment**: Low trust (new account, no community signals)

### Summary

A TypeScript MCP server providing full Twitter/X API v2 access. Clean implementation using the official `twitter-api-v2` library with proper input validation via Zod schemas. No malicious patterns detected, but the server has powerful capabilities (posting tweets, sending DMs, managing follows) that warrant careful configuration.

### Findings

#### Critical
- None

#### Warning
- `src/client.ts:9-13` — Reads 4 API credentials from environment variables (`X_API_KEY`, `X_API_SECRET`, `X_ACCESS_TOKEN`, `X_ACCESS_TOKEN_SECRET`). These are sent to the Twitter API as intended, but users must ensure credentials are not overly permissioned.
- `src/tools/dm.ts:7-28` — `send_dm` tool can send direct messages to any user. Powerful capability that could be misused if the MCP server is accessible to untrusted clients.
- `src/tools/tweets.ts:6-31` — `post_tweet` tool can publish tweets to the authenticated account. Users should be aware of this write capability.
- Author trust is low (new account with no community signals). Cross-correlation with findings above escalates overall caution.

#### Info
- Well-structured codebase with clear separation of concerns (client, server, tools).
- `src/server.ts:36-51` — `X_ENABLED_GROUPS` and `X_DISABLED_TOOLS` env vars allow granular tool access control. Good security design.
- Input validation via Zod schemas on all tool inputs.
- Dependencies are minimal and well-known (`@modelcontextprotocol/sdk`, `twitter-api-v2`, `zod`).
- No `eval`, `exec`, `child_process`, obfuscated code, or data exfiltration patterns detected.

### Recommendation
[x] Install with caution — Use `X_ENABLED_GROUPS` to restrict to read-only tools (e.g., `tweets:get,timelines:get`) unless write access is explicitly needed. Review API token permissions.

---

## tumf/mcp-shell-server

**Target**: tumf/mcp-shell-server (MCP Server)
**Risk Level**: CAUTION

### Author Trust
- **Publisher**: tumf
- **Account age**: 15+ years (since 2009)
- **Signals**: 166 stars, 42 forks, 97 followers, MIT license
- **Assessment**: Trusted

### Summary

A shell command execution MCP server with command whitelisting. The command validation logic is well-structured with whitelist enforcement, shell operator blocking, and argument escaping. Security depends on how the `ALLOW_COMMANDS` environment variable is configured by the user.

### Findings

#### Critical
- None

#### Warning
- `src/mcp_shell_server/process_manager.py:160` — Uses `asyncio.create_subprocess_shell()` (shell=True equivalent). While commands are validated beforehand, shell interpretation may introduce bypass vectors.
- `src/mcp_shell_server/command_validator.py:24-27` — Security boundary depends on `ALLOW_COMMANDS` env var. Overly broad configuration (e.g., allowing `bash`, `sh`, `python`) would negate all protections.

#### Info
- Command whitelist validation and shell operator blocking (`; && ||`) are properly implemented.
- Pipe commands are validated — each command in the pipeline must be individually whitelisted.
- `shlex.quote()` used for argument escaping.
- No data exfiltration, obfuscated code, or prompt injection detected.

### Recommendation
[x] Install with caution — Security depends on strict `ALLOW_COMMANDS` configuration. Do not whitelist interpreters (`bash`, `sh`, `python`).

---

## mark3labs/mcp-filesystem-server

**Target**: mark3labs/mcp-filesystem-server (MCP Server)
**Risk Level**: SAFE

### Author Trust
- **Publisher**: mark3labs
- **Account age**: 9+ years (since 2016)
- **Signals**: 599 stars, 98 forks, 198 followers, MIT license
- **Assessment**: Trusted

### Summary

A well-implemented filesystem MCP server in Go. Path validation with symlink resolution properly sandboxes all operations to allowed directories. No network communication or data exfiltration vectors found.

### Findings

#### Critical
- None

#### Warning
- None

#### Info
- `filesystemserver/handler/helper.go:42-86` — `validatePath()` converts to absolute path, checks against allowed directories, and resolves symlinks to prevent traversal attacks.
- `filesystemserver/handler/helper.go:15-40` — `isPathInAllowedDirs()` uses trailing separator check to prevent prefix attacks (e.g., `/tmp/foo` won't match `/tmp/foobar`).
- `filesystemserver/handler/delete_file.go:96` — Recursive delete via `os.RemoveAll()` available but requires explicit `recursive=true` flag.
- No network calls, no external data transmission, no obfuscated code.

### Recommendation
[x] Safe to install

---

## daymade/claude-code-skills

**Target**: daymade/claude-code-skills (Skill Collection)
**Risk Level**: CAUTION

### Author Trust
- **Publisher**: daymade
- **Account age**: 12+ years (since 2013)
- **Signals**: 592 stars, 67 forks, 160 followers, MIT license
- **Assessment**: Trusted

### Summary

A large collection of 30+ Claude Code skills covering a wide range of use cases. Several skills include scripts that access external APIs and read environment variables for API credentials. No malicious patterns detected. The breadth of the collection means each skill should be reviewed individually before use.

### Findings

#### Critical
- None

#### Warning
- `twitter-reader/scripts/fetch_tweet.py:24,34-36` — Reads `JINA_API_KEY` from environment and sends it to `r.jina.ai` via curl. Legitimate API usage but involves sending credentials externally.
- `cloudflare-troubleshooting/scripts/check_cloudflare_config.py:38-87` — Makes HTTP requests to external endpoints using `requests` library. Reads Cloudflare API credentials.
- `cloudflare-troubleshooting/scripts/fix_ssl_mode.py:52-95` — Modifies Cloudflare SSL configuration via API. Has write access to external services.

#### Info
- Multiple skills use `subprocess` (markdown-tools, cli-demo-generator, youtube-downloader, skill-creator) — all usage appears legitimate with no shell injection risks.
- `macos-cleaner/scripts/safe_delete.py:99` — Protects sensitive directories (.ssh, credentials) from deletion.
- No obfuscated code, base64 payloads, or prompt injection patterns detected.
- Large collection — individual skill review recommended before installation.

### Recommendation
[x] Install with caution — Review individual skills before use. Skills with external API access (twitter-reader, cloudflare-troubleshooting) require API credentials via environment variables.

---

## alirezarezvani/claude-skills

**Target**: alirezarezvani/claude-skills (Skill Collection)
**Risk Level**: CAUTION

### Author Trust
- **Publisher**: alirezarezvani
- **Account age**: 12+ years (since 2013)
- **Signals**: 1,889 stars, 228 forks, 213 followers, MIT license
- **Assessment**: Trusted

### Summary

The largest Claude Code skill collection (50+ skills) covering engineering, business, marketing, and compliance domains. Scripts primarily perform local analysis and code generation. No malicious patterns found. The collection's size makes full review impractical — review individual skills as needed.

### Findings

#### Critical
- None

#### Warning
- `engineering-team/senior-backend/scripts/api_load_tester.py:26-28` — Uses `urllib` to send HTTP requests to user-specified endpoints. Intended for load testing but could be pointed at any URL.
- Collection contains 100+ Python scripts across 50+ skills — complete manual review is impractical. Install individual skills rather than the entire collection.

#### Info
- `engineering-team/senior-security/scripts/secret_scanner.py:384` — Uses `__import__('datetime')` which is an unusual pattern but benign (only imports datetime).
- `engineering/tech-debt-tracker/assets/sample_codebase/src/payment_processor.py` — Contains `requests.post()` calls but this is a sample/demo file, not executable skill code.
- `engineering/migration-architect/scripts/rollback_generator.py` — Contains curl commands but these are template strings for generated scripts, not directly executed.
- No data exfiltration, obfuscated code, or prompt injection patterns detected.

### Recommendation
[x] Install with caution — Install individual skills rather than the full collection. Review scripts before use, especially those in engineering-team/senior-backend.
