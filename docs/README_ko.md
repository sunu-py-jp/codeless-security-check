# codeless-security-check

[English](../README.md) | [日本語](README_ja.md) | 한국어 | [中文](README_zh.md)

Claude Code용 보안 감사 스킬. 설치 전에 스킬과 MCP 서버의 취약점을 검사합니다.

## 검사 항목

- 악성 코드 실행 (eval, exec, 셸 인젝션)
- 데이터 유출 (인증 정보, 환경 변수의 외부 전송)
- 프롬프트 인젝션 (숨겨진 지시, 유니코드 트릭)
- 과도한 권한 요청 (광범위한 파일 접근, 위험한 플래그)
- 작성자 신뢰성 검증 (계정 연령, 저장소 시그널)
- 교차 상관 분석 (여러 탐지 결과를 조합한 위협 판정)

## 설계 철학

**스크립트 없음. 코드 없음. 마크다운만.**

실행 가능한 코드를 전혀 포함하지 않습니다. 구조화된 체크리스트, 위협 패턴, 에스컬레이션 규칙을 Claude에 전달하고, Claude의 추론 능력으로 분석합니다.

왜 코드리스인가?

- **공격 표면 제로** — 스크립트가 있는 보안 도구는 그 자체가 공격 대상이 되는 모순이 있다. 이 스킬은 침해될 수 없다.
- **미지의 위협 탐지** — 정규식 스캐너는 알려진 패턴만 찾을 수 있다. Claude는 의도를 추론하여 패턴 매처로는 감지할 수 없는 위협을 포착한다.
- **컨텍스트 효율** — 총 ~13KB. 컨텍스트 윈도우를 압박하지 않는다.

## 구조

```
codeless-security-check/
  SKILL.md              # 6단계 워크플로우
  references/
    skill-checklist.md   # .skill 패키지용 체크리스트
    mcp-checklist.md     # MCP 서버용 체크리스트
    threat-patterns.md   # 코드 예시가 포함된 알려진 공격 패턴
```

## 설치

```bash
npx skills add https://github.com/sunu-py-jp/codeless-security-check --skill codeless-security-check
```

또는 수동으로:

```bash
cp -r codeless-security-check ~/.claude/skills/
```

## 사용법

Claude Code에 물어보세요:

- "Check the security of this skill: ~/.claude/skills/some-skill"
- "Is this MCP server safe? https://github.com/org/some-mcp"
- "Review this .skill file for vulnerabilities"

## 실행 예시

실제 감사 결과는 [EXAMPLES_ko.md](EXAMPLES_ko.md) 참조:

| 대상 | 유형 | 위험도 |
|------|------|--------|
| [anthropics/skills — frontend-design](https://github.com/anthropics/skills) | 스킬 | SAFE |
| [sunu-py-jp/X-Twitter-MCP](https://github.com/sunu-py-jp/X-Twitter-MCP) | MCP 서버 | CAUTION |
| [tumf/mcp-shell-server](https://github.com/tumf/mcp-shell-server) | MCP 서버 | CAUTION |
| [mark3labs/mcp-filesystem-server](https://github.com/mark3labs/mcp-filesystem-server) | MCP 서버 | SAFE |
| [daymade/claude-code-skills](https://github.com/daymade/claude-code-skills) | 스킬 컬렉션 | CAUTION |
| [alirezarezvani/claude-skills](https://github.com/alirezarezvani/claude-skills) | 스킬 컬렉션 | CAUTION |

## 라이선스

[MIT](../LICENSE)
