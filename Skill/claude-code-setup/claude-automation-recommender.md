# claude-automation-recommender

> 코드베이스를 분석해 맞춤형 Claude Code 자동화(훅·서브에이전트·스킬·플러그인·MCP 서버)를 추천하는 스킬

## 📌 용도
코드베이스 패턴을 분석해 모든 확장 옵션에 걸쳐 맞춤형 Claude Code 자동화를 추천한다. **이 스킬은 읽기 전용** — 코드베이스를 분석해 추천을 출력할 뿐, 파일을 생성/수정하지 않는다. 사용자가 직접 구현하거나 Claude에게 별도로 요청해 만든다.

## 🎯 트리거 조건
- **언제 호출되는가**: 자동화 추천 요청, Claude Code 설정 최적화, Claude Code 워크플로 개선 언급, 프로젝트에 Claude Code를 처음 설정하는 방법, 어떤 Claude Code 기능을 써야 할지 물을 때.
- **언제 쓰지 않는가**: 실제 컴포넌트 생성·구현은 별도 요청(이 스킬은 추천만). 특정 플러그인 사용법은 해당 플러그인 스킬.

## 💻 사용 예시 / 명령어
- "우리 프로젝트에 어떤 Claude Code 자동화를 쓰면 좋을까?"
- "Claude Code 설정을 최적화하고 싶어"
- "이 코드베이스에 맞는 MCP 서버 추천해줘"
- "어떤 훅을 설정하면 도움이 될까?"

## 🛠️ 제공 도구 / 주요 파라미터
**출력 가이드라인**: 유형별 1~2개씩 추천(압도 방지). 특정 유형 요청 시 해당 유형 3~5개 집중. 레퍼런스 리스트를 넘어 웹 검색으로 코드베이스 특화 추천. 더 원하면 카테고리별 추가 요청 가능함을 안내.

**자동화 유형**: 훅(도구 이벤트 자동 액션), 서브에이전트(병렬 전문 리뷰어), 스킬(패키지화된 전문성·워크플로), 플러그인(스킬 모음), MCP 서버(외부 도구 통합).

**3단계 워크플로**:
1. **코드베이스 분석**: 프로젝트 타입·도구 감지(`package.json`, `pyproject.toml` 등), 의존성 확인, 기존 `.claude/` 설정 확인, 구조 분석. 언어/프레임워크, 프론트/백엔드 스택, DB, 외부 API, 테스트, CI/CD, 이슈 트래킹, 문서 패턴 캡처.

2. **추천 생성**(카테고리별):
   - **MCP 서버**: 인기 라이브러리→context7, UI 테스트→Playwright, Supabase/PostgreSQL→DB MCP, GitHub→GitHub MCP, Linear, AWS, Slack, Sentry, Docker 등.
   - **스킬**: 플러그인 빌드→skill-development, git→commit, 프론트엔드→frontend-design 등 + 커스텀 스킬(api-doc, create-migration, gen-test 등).
   - **훅**: Prettier→PostToolUse 포맷, ESLint→린트, TypeScript→타입체크, `.env`→PreToolUse 차단 등.
   - **서브에이전트**: 대형 코드베이스→code-reviewer, auth/payments→security-reviewer, API→api-documenter 등.
   - **플러그인**: anthropic-agent-skills, frontend-design, mcp-builder 등.

3. **추천 리포트 출력**: 코드베이스 프로필 + 카테고리별 1~2개(이유·설치/생성 위치·invocation 포함). 관련 없는 카테고리는 건너뜀.

**invocation 제어**: `disable-model-invocation: true`(User-only), `user-invocable: false`(Claude-only), 기본(둘 다).

**참조 파일**: `references/mcp-servers.md`, `skills-reference.md`, `hooks-patterns.md`, `subagent-templates.md`, `plugins-reference.md`
**tools**: Read, Glob, Grep, Bash

## 📦 출처 / 설치 상태
- 플러그인: claude-code-setup@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/claude-code-setup/1.0.0/skills/claude-automation-recommender/SKILL.md`
