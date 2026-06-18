# command-development

> Claude Code 슬래시 명령의 구조·YAML frontmatter·동적 인자·사용자 인터랙션 패턴을 안내하는 스킬

## 📌 용도
자주 쓰는 프롬프트를 마크다운 파일로 정의한 슬래시 명령을 만든다. 재사용성·일관성·공유·효율성을 제공한다.

> **참고**: `.claude/commands/` 디렉터리는 legacy 형식이다. 새 스킬은 `.claude/skills/<name>/SKILL.md` 형식을 사용한다(둘은 동일하게 로드되며 파일 레이아웃만 다름). 권장 형식은 skill-development 참조.

**핵심**: 명령은 사용자가 아니라 **Claude를 위한 지시문**이다. `/command-name` 호출 시 명령 내용이 Claude의 지시가 되므로, 사용자에게 설명하는 글이 아니라 Claude에게 무엇을 하라고 지시하는 directive로 작성한다.

## 🎯 트리거 조건
- **언제 호출되는가**: "슬래시 명령 생성/추가", "커스텀 명령 작성", "command arguments 정의", "command frontmatter", "interactive command", "AskUserQuestion in command", file references 사용 요청 시.
- **언제 쓰지 않는가**: 자율 에이전트(→ agent-development), 스킬(→ skill-development), 이벤트 자동화(→ hook-development). 새 플러그인은 commands 대신 skills 권장.

## 💻 사용 예시 / 명령어
- "PR 리뷰용 슬래시 명령을 만들어줘"
- "명령에 인자 두 개를 받게 하고 싶어"
- "git status를 가져와서 컨텍스트로 쓰는 명령"

## 🛠️ 제공 도구 / 주요 파라미터
**파일 위치**: 프로젝트 `.claude/commands/`, 개인 `~/.claude/commands/`, 플러그인 `plugin-name/commands/`.

**YAML frontmatter 필드**:
- `description`: `/help`에 표시(60자 이하 권장)
- `allowed-tools`: 도구 제한 (`Read, Write, Edit`, `Bash(git:*)`)
- `model`: `haiku`/`sonnet`/`opus`
- `argument-hint`: 인자 문서화 (`[pr-number] [priority]`)
- `disable-model-invocation`: SlashCommand 도구의 프로그래밍 호출 차단

**동적 인자**:
- `$ARGUMENTS`: 모든 인자를 단일 문자열로
- `$1`, `$2`, `$3`: 위치 인자
- 혼합: `Deploy $1 to $2 with options: $3`

**파일 참조 (@ 구문)**: `@$1`, `@src/old.js`, `@package.json` — Claude가 명령 처리 전 파일을 읽음.

**Bash 실행**: 인라인 백틱 `` !`git diff --name-only` ``으로 동적 컨텍스트 수집(상세 구문은 `references/plugin-features-reference.md`).

**플러그인 특화**: `${CLAUDE_PLUGIN_ROOT}`로 포터블 경로 참조(`!`bash ${CLAUDE_PLUGIN_ROOT}/scripts/x.sh``, `@${CLAUDE_PLUGIN_ROOT}/config/settings.json`).

**컴포넌트 통합**: 에이전트 실행(Task 도구), 스킬 활용(이름 언급으로 트리거), 훅 연계, 멀티 컴포넌트 워크플로.

**검증 패턴**: 인자 검증, 파일 존재 확인(`test -f`), 플러그인 리소스 검증, 에러 핸들링(`2>&1 || echo "FAILED"`).

**참조 파일**: `references/frontmatter-reference.md`, `references/plugin-features-reference.md`, `references/interactive-commands.md`, `examples/simple-commands.md`, `examples/plugin-commands.md`

## 📦 출처 / 설치 상태
- 플러그인: plugin-dev@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/plugin-dev/unknown/skills/command-development/SKILL.md`
