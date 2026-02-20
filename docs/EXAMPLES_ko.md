# 감사 실행 예시

codeless-security-check로 생성된 실제 보안 감사 결과입니다.

> **아래 나열된 프로젝트에 대한 중요 안내**
>
> 여기에 소개된 모든 프로젝트는 Claude Code 및 MCP 생태계에 대한 우수한 기여입니다. 인기 있고 활발히 유지보수되며 개발자들이 실제로 사용하는 도구의 대표적인 예이기 때문에 선정했습니다. **이 프로젝트들 중 악성 코드나 의도적인 취약점을 포함하는 것은 없습니다.**
>
> "CAUTION" 등급은 프로젝트 자체의 결함을 나타내지 않습니다. 셸 실행, 파일 시스템 접근, 외부 API 호출 등 소프트웨어의 동작을 *사용자가* 이해한 후 설치해야 한다는 평가입니다. 이는 도구의 목적에 내재된 특성이지 결함이 아닙니다. 셸 실행 MCP는 셸을 실행해야 하고, Twitter 클라이언트는 Twitter API를 호출해야 합니다. 이 감사는 이러한 동작을 가시화하여 사용자가 충분한 정보를 바탕으로 판단할 수 있도록 합니다.
>
> 여기에 소개된 모든 저자와 유지보수자에게 깊은 존경을 표합니다. 커뮤니티를 위한 오픈소스 도구를 구축하고 유지하는 것은 상당한 노력과 관대함이 필요한 일입니다. 그 기여에 진심으로 감사드립니다.

---

## anthropics/skills — frontend-design

**대상**: anthropics/skills / frontend-design (스킬)
**위험도**: SAFE

### 작성자 신뢰도
- **게시자**: Anthropic
- **계정 연령**: 5년 이상 (2020년~)
- **시그널**: 72,243 stars, 7,394 forks, 27,351 followers
- **평가**: 신뢰 (알려진 게시자)

### 요약

스크립트, 네트워크 접근, 실행 가능 코드가 없는 순수 마크다운 스킬. 프론트엔드 개발을 위한 디자인 가이드라인과 미적 방향성만 포함. 코드리스 스킬 패턴의 모범적 구현.

### 발견사항

#### Critical (즉각적 위협)
- 없음

#### Warning (잠재적 위험)
- 없음

#### Info (참고)
- `SKILL.md`에는 디자인 가이드라인과 미적 지시만 포함 — 실행 코드, 셸 명령, 외부 참조 없음.
- 스크립트, 참조, 정적 폰트 외 에셋 없음.
- 공격 표면 제로.

### 권장사항
[x] 안전하게 설치 가능

---

## sunu-py-jp/X-Twitter-MCP

**대상**: sunu-py-jp/X-Twitter-MCP (MCP 서버)
**위험도**: CAUTION

### 작성자 신뢰도
- **게시자**: sunu-py-jp
- **계정 연령**: 1개월 미만 (2026년 2월~)
- **시그널**: 0 stars, 0 forks, 0 followers, MIT license
- **평가**: 낮은 신뢰 (신규 계정, 커뮤니티 시그널 없음)

### 요약

Twitter/X API v2 전체 접근을 제공하는 TypeScript MCP 서버. 공식 `twitter-api-v2` 라이브러리를 사용한 클린한 구현과 Zod 스키마를 통한 적절한 입력 검증. 악성 패턴은 감지되지 않았으나, 강력한 기능(트윗 게시, DM 전송, 팔로우 관리)은 신중한 설정이 필요.

### 발견사항

#### Critical
- 없음

#### Warning
- `src/client.ts:9-13` — 환경 변수에서 4개 API 인증 정보 읽기 (`X_API_KEY`, `X_API_SECRET`, `X_ACCESS_TOKEN`, `X_ACCESS_TOKEN_SECRET`). 의도대로 Twitter API에 전송되지만 인증 정보 권한 범위에 주의 필요.
- `src/tools/dm.ts:7-28` — `send_dm` 도구로 모든 사용자에게 DM 전송 가능. 신뢰할 수 없는 클라이언트가 접근할 경우 오용 가능성이 있는 강력한 기능.
- `src/tools/tweets.ts:6-31` — `post_tweet` 도구로 인증된 계정에서 트윗 게시 가능. 이 쓰기 기능에 주의 필요.
- 작성자 신뢰도가 낮음 (커뮤니티 시그널 없는 신규 계정). 위 발견사항과의 교차 상관으로 전체 주의 수준 상승.

#### Info
- 클라이언트, 서버, 도구가 명확히 분리된 잘 구조화된 코드베이스.
- `src/server.ts:36-51` — `X_ENABLED_GROUPS`와 `X_DISABLED_TOOLS` 환경 변수로 세밀한 도구 접근 제어 가능. 우수한 보안 설계.
- 모든 도구 입력에 Zod 스키마를 통한 입력 검증.
- 최소한의 잘 알려진 의존성만 사용 (`@modelcontextprotocol/sdk`, `twitter-api-v2`, `zod`).
- `eval`, `exec`, `child_process`, 난독화 코드, 데이터 유출 패턴 미감지.

### 권장사항
[x] 주의하여 설치 — 쓰기 접근이 명시적으로 필요하지 않은 한 `X_ENABLED_GROUPS`로 읽기 전용 도구 (예: `tweets:get,timelines:get`)로 제한할 것. API 토큰 권한 확인 필요.

---

## tumf/mcp-shell-server

**대상**: tumf/mcp-shell-server (MCP 서버)
**위험도**: CAUTION

### 작성자 신뢰도
- **게시자**: tumf
- **계정 연령**: 15년 이상 (2009년~)
- **시그널**: 166 stars, 42 forks, 97 followers, MIT license
- **평가**: 신뢰

### 요약

명령어 화이트리스트 방식의 셸 명령 실행 MCP 서버. 화이트리스트 강제, 셸 연산자 차단, 인수 이스케이프를 통한 적절한 명령어 검증 로직. 보안은 사용자의 `ALLOW_COMMANDS` 환경 변수 설정에 의존.

### 발견사항

#### Critical
- 없음

#### Warning
- `src/mcp_shell_server/process_manager.py:160` — `asyncio.create_subprocess_shell()` (shell=True 동등) 사용. 명령어가 사전 검증되지만 셸 해석에 의한 우회 가능성 존재.
- `src/mcp_shell_server/command_validator.py:24-27` — 보안 경계가 `ALLOW_COMMANDS` 환경 변수에 의존. 과도하게 넓은 설정 (예: `bash`, `sh`, `python` 허용)은 모든 보호를 무효화.

#### Info
- 명령어 화이트리스트 검증과 셸 연산자 차단 (`; && ||`)이 적절히 구현됨.
- 파이프 명령어 검증 — 파이프라인 내 각 명령어가 개별적으로 화이트리스트 확인됨.
- 인수 이스케이프에 `shlex.quote()` 사용.
- 데이터 유출, 난독화 코드, 프롬프트 인젝션 미감지.

### 권장사항
[x] 주의하여 설치 — 보안은 엄격한 `ALLOW_COMMANDS` 설정에 의존. 인터프리터 (`bash`, `sh`, `python`)를 화이트리스트에 포함하지 말 것.

---

## mark3labs/mcp-filesystem-server

**대상**: mark3labs/mcp-filesystem-server (MCP 서버)
**위험도**: SAFE

### 작성자 신뢰도
- **게시자**: mark3labs
- **계정 연령**: 9년 이상 (2016년~)
- **시그널**: 599 stars, 98 forks, 198 followers, MIT license
- **평가**: 신뢰

### 요약

Go로 구현된 우수한 파일시스템 MCP 서버. 심볼릭 링크 해석을 포함한 경로 검증으로 모든 작업이 허용 디렉토리 내에 적절히 샌드박스화. 네트워크 통신이나 데이터 유출 벡터 미발견.

### 발견사항

#### Critical
- 없음

#### Warning
- 없음

#### Info
- `filesystemserver/handler/helper.go:42-86` — `validatePath()`가 절대 경로 변환, 허용 디렉토리 확인, 심볼릭 링크 해석으로 트래버설 공격 방지.
- `filesystemserver/handler/helper.go:15-40` — `isPathInAllowedDirs()`가 후행 구분자 검사로 접두사 공격 방지 (예: `/tmp/foo`가 `/tmp/foobar`에 매칭되지 않음).
- `filesystemserver/handler/delete_file.go:96` — `os.RemoveAll()`을 통한 재귀적 삭제 가능하나 명시적 `recursive=true` 플래그 필요.
- 네트워크 호출, 외부 데이터 전송, 난독화 코드 없음.

### 권장사항
[x] 안전하게 설치 가능

---

## daymade/claude-code-skills

**대상**: daymade/claude-code-skills (스킬 컬렉션)
**위험도**: CAUTION

### 작성자 신뢰도
- **게시자**: daymade
- **계정 연령**: 12년 이상 (2013년~)
- **시그널**: 592 stars, 67 forks, 160 followers, MIT license
- **평가**: 신뢰

### 요약

다양한 사용 사례를 포괄하는 30개 이상의 Claude Code 스킬 대규모 컬렉션. 일부 스킬은 외부 API에 접근하고 API 인증 정보를 환경 변수에서 읽는 스크립트를 포함. 악성 패턴 미감지. 컬렉션 규모가 크므로 사용 전 개별 스킬 리뷰 권장.

### 발견사항

#### Critical
- 없음

#### Warning
- `twitter-reader/scripts/fetch_tweet.py:24,34-36` — 환경 변수에서 `JINA_API_KEY`를 읽어 curl로 `r.jina.ai`에 전송. 정당한 API 사용이나 인증 정보의 외부 전송 수반.
- `cloudflare-troubleshooting/scripts/check_cloudflare_config.py:38-87` — `requests` 라이브러리로 외부 엔드포인트에 HTTP 요청. Cloudflare API 인증 정보 읽기.
- `cloudflare-troubleshooting/scripts/fix_ssl_mode.py:52-95` — API를 통해 Cloudflare SSL 설정 변경. 외부 서비스에 대한 쓰기 접근.

#### Info
- 여러 스킬이 `subprocess` 사용 (markdown-tools, cli-demo-generator, youtube-downloader, skill-creator) — 모두 정당한 사용이며 셸 인젝션 위험 없음.
- `macos-cleaner/scripts/safe_delete.py:99` — 민감한 디렉토리 (.ssh, credentials)를 삭제로부터 보호.
- 난독화 코드, base64 페이로드, 프롬프트 인젝션 패턴 미감지.
- 대규모 컬렉션 — 설치 전 개별 스킬 리뷰 권장.

### 권장사항
[x] 주의하여 설치 — 사용 전 개별 스킬을 리뷰할 것. 외부 API 접근이 있는 스킬 (twitter-reader, cloudflare-troubleshooting)은 환경 변수를 통한 API 인증 정보 필요.

---

## alirezarezvani/claude-skills

**대상**: alirezarezvani/claude-skills (스킬 컬렉션)
**위험도**: CAUTION

### 작성자 신뢰도
- **게시자**: alirezarezvani
- **계정 연령**: 12년 이상 (2013년~)
- **시그널**: 1,889 stars, 228 forks, 213 followers, MIT license
- **평가**: 신뢰

### 요약

엔지니어링, 비즈니스, 마케팅, 컴플라이언스 분야를 포괄하는 최대 Claude Code 스킬 컬렉션 (50개 이상). 스크립트는 주로 로컬 분석과 코드 생성 수행. 악성 패턴 미발견. 컬렉션 규모상 전체 리뷰는 비현실적 — 필요에 따라 개별 스킬 리뷰 권장.

### 발견사항

#### Critical
- 없음

#### Warning
- `engineering-team/senior-backend/scripts/api_load_tester.py:26-28` — `urllib`로 사용자 지정 엔드포인트에 HTTP 요청 전송. 부하 테스트 목적이나 임의의 URL에 지정 가능.
- 50개 이상 스킬에 걸쳐 100개 이상의 Python 스크립트 포함 — 완전한 수동 리뷰 비현실적. 전체 컬렉션이 아닌 개별 스킬 설치 권장.

#### Info
- `engineering-team/senior-security/scripts/secret_scanner.py:384` — `__import__('datetime')` 사용. 비일반적 패턴이나 무해 (datetime만 임포트).
- `engineering/tech-debt-tracker/assets/sample_codebase/src/payment_processor.py` — `requests.post()` 호출 포함하나 실행 가능한 스킬 코드가 아닌 샘플/데모 파일.
- `engineering/migration-architect/scripts/rollback_generator.py` — curl 명령 포함하나 직접 실행이 아닌 생성 스크립트용 템플릿 문자열.
- 데이터 유출, 난독화 코드, 프롬프트 인젝션 패턴 미감지.

### 권장사항
[x] 주의하여 설치 — 전체 컬렉션이 아닌 개별 스킬 설치 권장. 특히 engineering-team/senior-backend의 스크립트는 사용 전 리뷰 필요.
