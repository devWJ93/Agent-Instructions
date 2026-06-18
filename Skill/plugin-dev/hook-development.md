# hook-development

> Claude Code 플러그인의 이벤트 기반 훅을 생성·구현하는 스킬 (프롬프트 기반 훅 API 중심)

## 📌 용도
Claude Code 이벤트에 반응해 실행되는 이벤트 기반 자동화 스크립트(훅)를 만든다. 도구 호출 검증, 정책 강제, 컨텍스트 추가, 외부 도구 통합에 사용한다.

**훅 유형**:
- **프롬프트 기반 훅(권장)**: LLM 기반 의사결정으로 컨텍스트 인식 검증. `{"type":"prompt","prompt":"...","timeout":30}`. 지원 이벤트: Stop, SubagentStop, UserPromptSubmit, PreToolUse. 자연어 추론, 유연성, 엣지 케이스 처리에 강점.
- **명령 훅**: 결정론적 검사용 bash 실행. `{"type":"command","command":"bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh","timeout":60}`. 빠른 검증, 파일시스템 작업, 성능 중요 검사.

## 🎯 트리거 조건
- **언제 호출되는가**: "훅 생성", "PreToolUse/PostToolUse/Stop 훅 추가", "도구 사용 검증", "프롬프트 기반 훅", "${CLAUDE_PLUGIN_ROOT}", "위험 명령 차단", 또는 훅 이벤트(PreToolUse, PostToolUse, Stop, SubagentStop, SessionStart, SessionEnd, UserPromptSubmit, PreCompact, Notification) 언급 시.
- **언제 쓰지 않는가**: 슬래시 명령(→ command-development), 에이전트(→ agent-development), 스킬(→ skill-development).

## 💻 사용 예시 / 명령어
- "파일 쓰기 전에 안전성 검증하는 훅을 만들어줘"
- ".env 파일 편집을 차단하는 PreToolUse 훅"
- "세션 시작 시 프로젝트 컨텍스트를 로드하는 훅"

## 🛠️ 제공 도구 / 주요 파라미터
**설정 형식**:
- 플러그인 `hooks/hooks.json`: 래퍼 형식 `{"description":"...","hooks":{"PreToolUse":[...]}}`
- 사용자 `.claude/settings.json`: 직접 형식(래퍼 없이 이벤트가 최상위)

**훅 이벤트**: PreToolUse(승인/거부/수정, `permissionDecision: allow|deny|ask`), PostToolUse(피드백, exit 2시 stderr가 Claude로), Stop(완결성 검증, `decision: approve|block`), SubagentStop, UserPromptSubmit(컨텍스트 추가), SessionStart(`$CLAUDE_ENV_FILE`로 env 영속), SessionEnd, PreCompact, Notification.

**입력**(stdin JSON): `session_id`, `cwd`, `hook_event_name` 등 공통 + 이벤트별(`tool_name`, `tool_input`, `tool_result`, `user_prompt`). 프롬프트에서 `$TOOL_INPUT`, `$TOOL_RESULT`, `$USER_PROMPT`로 접근.

**환경변수**: `$CLAUDE_PROJECT_DIR`, `$CLAUDE_PLUGIN_ROOT`(포터빌리티 필수), `$CLAUDE_ENV_FILE`(SessionStart), `$CLAUDE_CODE_REMOTE`.

**Exit 코드**: 0(성공·stdout 표시), 2(차단 에러·stderr가 Claude로), 기타(비차단 에러).

**매처**: 정확 `"Write"`, 다중 `"Read|Write|Edit"`, 와일드카드 `"*"`, 정규식 `"mcp__.*__delete.*"`(대소문자 구분).

**보안**: 입력 검증, 경로 traversal/민감 파일 차단, 모든 변수 따옴표, 타임아웃 설정(명령 60s/프롬프트 30s 기본).

**중요 제약**: 훅은 세션 시작 시 로드되어 핫스왑 불가 — 변경 후 Claude Code 재시작 필요. `/hooks`로 로드 확인, `claude --debug`로 디버깅.

**참조 파일**: `references/patterns.md`, `references/migration.md`, `references/advanced.md`; `examples/validate-write.sh`, `validate-bash.sh`, `load-context.sh`; `scripts/validate-hook-schema.sh`, `test-hook.sh`, `hook-linter.sh`

## 📦 출처 / 설치 상태
- 플러그인: plugin-dev@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/plugin-dev/unknown/skills/hook-development/SKILL.md`
