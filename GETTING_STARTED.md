<!-- 이 문서는 Agent-Instructions 저장소를 처음 적용하는 절차를 안내한다. -->
# Getting Started

이 저장소는 설치 프로그램이 아니라 AI 에이전트가 읽고 따를 운영 지침이다. 링크만 전달해도 어느 정도 적용할 수 있지만, AI별로 웹 접근·파일 읽기·MCP 지원 범위가 다르므로 아래 순서로 안내하면 가장 안정적이다.

## 1. AI에게 바로 줄 프롬프트

```text
이 저장소를 읽고 내 작업 지침으로 적용해줘.
먼저 README.md를 읽고, 그 다음 AGENTS.md와 PORTABILITY.md를 읽어.
Claude 전용 기능은 네 환경의 동등 기능으로 번역하고, 없는 기능은 건너뛰어.
이 저장소는 토큰이나 개인 설정 파일을 포함하지 않는 공개용 운영 지침 카탈로그야.
https://github.com/devWJ93/Agent-Instructions
```

AI가 GitHub 링크를 직접 열 수 없다면 저장소를 내려받은 뒤, 루트의 `README.md`, `AGENTS.md`, `PORTABILITY.md` 내용을 순서대로 제공한다.

## 2. 적용 순서

1. `README.md`를 읽어 저장소 구조와 의사결정 지도를 파악한다.
2. `AGENTS.md`를 현재 세션의 행동 규칙으로 적용한다.
3. Claude Code가 아닌 환경이라면 `PORTABILITY.md`를 읽고 Claude 전용 기능을 번역하거나 제외한다.
4. 작업 중 특정 스킬이나 MCP가 필요하면 `Skill/README.md`와 `MCP/README.md`에서 해당 문서를 찾아 읽는다.
5. 실제 토큰, 인증 헤더, 개인 설정 파일은 각 사용자 환경에서만 설정하고 저장소에는 넣지 않는다.

## 3. AI별 사용법

### Claude Code

- `README.md`와 `AGENTS.md`를 프로젝트 지침으로 읽게 한다.
- 스킬과 MCP는 이 저장소에서 자동 설치되지 않는다. 필요한 항목은 각 원본 플러그인이나 MCP 서버 문서를 기준으로 별도 설치한다.
- Claude 전용 규칙은 그대로 적용할 수 있지만, 사용자 환경에 없는 MCP나 플러그인은 건너뛴다.

### Codex

- `AGENTS.md`의 행동 규칙은 그대로 적용한다.
- Claude 전용 도구명은 Codex의 파일 읽기, 검색, 셸, MCP 도구로 번역한다.
- `PORTABILITY.md`의 대체 표를 먼저 확인한다.

### Cursor, Gemini, Copilot 등

- Agent Skills를 직접 지원하지 않으면 `Skill/` 문서는 자동 트리거가 아니라 참조 문서로 사용한다.
- MCP를 지원하는 클라이언트는 `PORTABILITY.md`의 `mcpServers` 예시를 자기 설정 파일에 맞게 옮긴다.
- OAuth나 토큰이 필요한 MCP는 각 사용자 계정으로 직접 인증한다.

## 4. Codex에서 헷갈리기 쉬운 부분

Codex CLI는 `AGENTS.md`를 자동으로 찾지만, 모든 링크 문서를 자동으로 펼쳐 읽는 방식은 아니다.

- Codex는 실행 또는 TUI 세션 시작 시점에 `~/.codex/AGENTS.override.md`가 있으면 그것을, 없으면 `~/.codex/AGENTS.md`를 전역 지침으로 읽는다.
- 프로젝트에서는 Git 루트부터 현재 작업 디렉터리까지 `AGENTS.override.md`, `AGENTS.md`, 설정된 fallback 파일을 순서대로 찾고, 디렉터리마다 최대 1개만 포함한다.
- 이 instruction chain은 실행 시작 시 구성된다. 전역 지침이나 복제 문서를 고친 뒤에는 새 Codex 세션을 시작해야 반영이 확실하다.
- `AGENTS.md` 안의 `README.md를 읽어라`, `rules/01을 보라` 같은 문구는 행동 지시다. 해당 파일 내용이 자동으로 프롬프트에 모두 포함된다는 뜻은 아니다.
- 이 저장소의 `rules/*.md`는 운영 지침 문서다. Codex의 `~/.codex/rules/*.rules`는 명령 승인 정책 파일이며, 둘은 다른 기능이다.
- `~/.codex/Agent-Instructions`처럼 전역에 복제해서 쓰는 경우, GitHub 저장소를 업데이트해도 로컬 복제본은 자동 갱신되지 않는다. 복제본을 다시 동기화하거나 `git pull` 가능한 위치로 관리해야 한다.

현재 Codex가 실제로 어떤 지침을 읽는지 확인하려면 새 세션에서 아래처럼 요청한다.

```text
현재 활성화된 instruction sources를 나열하고, 전역 AGENTS.md와 프로젝트 AGENTS.md의 핵심 규칙을 요약해줘.
링크된 README나 rules 문서를 아직 읽지 않았다면 읽었는지 여부도 구분해서 말해줘.
```

Codex CLI 명령으로는 다음 식의 확인도 가능하다.

```powershell
codex --ask-for-approval never "Summarize the current instructions and list which instruction files are active."
```

## 5. 이 저장소가 하는 것과 하지 않는 것

하는 것.

- AI 에이전트의 작업 순서, 검증 규칙, 도구 선택 기준을 문서화한다.
- 스킬과 MCP의 용도, 트리거, 연결 정보를 카탈로그로 제공한다.
- Claude 전용 개념을 다른 AI 환경으로 옮기는 기준을 제공한다.

하지 않는 것.

- 토큰, `.env`, 개인 설정 파일을 제공하지 않는다.
- MCP 서버나 Agent Skill 실물을 재배포하지 않는다.
- 모든 AI 환경에서 자동 설치를 보장하지 않는다.

## 6. 공개 저장소 체크리스트

- [ ] `SECURITY.md`의 금지 항목을 확인했다.
- [ ] 실제 토큰·인증 헤더·개인 설정 파일을 커밋하지 않았다.
- [ ] 개인 프로젝트 지식이나 내부망 주소를 포함하지 않았다.
- [ ] 다른 AI에서 사용할 때 `PORTABILITY.md`의 번역 기준을 안내했다.
