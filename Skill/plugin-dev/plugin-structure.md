# plugin-structure

> Claude Code 플러그인의 디렉터리 레이아웃·매니페스트·컴포넌트 구성·자동 발견을 안내하는 스킬

## 📌 용도
표준화된 디렉터리 구조와 자동 컴포넌트 발견을 따르는 잘 구성된 플러그인을 만든다. 관례적 디렉터리 레이아웃, `.claude-plugin/plugin.json` 매니페스트 기반 구성, 컴포넌트 기반 조직(commands, agents, skills, hooks), `${CLAUDE_PLUGIN_ROOT}` 포터블 경로, 명시적 vs 자동 발견 로딩을 다룬다.

## 🎯 트리거 조건
- **언제 호출되는가**: "플러그인 생성/스캐폴드", "플러그인 구조 이해", "컴포넌트 구성", "plugin.json 설정", "${CLAUDE_PLUGIN_ROOT} 사용", "commands/agents/skills/hooks 추가", "자동 발견 구성" 요청 시.
- **언제 쓰지 않는가**: 개별 컴포넌트 작성 세부는 각 전용 스킬(skill-development 등).

## 💻 사용 예시 / 명령어
- "플러그인 디렉터리 구조를 잡아줘"
- "plugin.json에 어떤 필드가 필요해?"
- "스킬이 자동으로 발견되게 하려면 어디에 둬야 해?"

## 🛠️ 제공 도구 / 주요 파라미터
**디렉터리 구조**:
```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # 필수: 매니페스트
├── commands/                # 슬래시 명령 (.md)
├── agents/                  # 서브에이전트 (.md)
├── skills/
│   └── skill-name/SKILL.md  # 스킬별 필수
├── hooks/hooks.json         # 이벤트 핸들러
├── .mcp.json                # MCP 서버
└── scripts/                 # 헬퍼 스크립트
```

**핵심 규칙**:
1. `plugin.json`은 반드시 `.claude-plugin/`에.
2. 컴포넌트 디렉터리는 플러그인 루트(`.claude-plugin/` 내부 금지).
3. 사용하는 컴포넌트만 디렉터리 생성.
4. 모든 이름은 kebab-case.

**매니페스트(plugin.json)**:
- 필수: `name`(kebab-case, 고유)
- 권장: `version`(semver), `description`, `author`, `homepage`, `repository`, `license`, `keywords`
- 커스텀 경로: `commands`/`agents`/`hooks`/`mcpServers`(상대 경로, `./` 시작, 기본 디렉터리를 대체가 아닌 보완)

**컴포넌트 자동 발견**: commands/agents의 모든 `.md`, skills 하위의 모든 `SKILL.md`, hooks.json, .mcp.json을 스캔. 발견 타이밍: 설치 시 등록, 활성화 시 사용 가능, 재시작 불필요(변경은 다음 세션 반영).

**${CLAUDE_PLUGIN_ROOT}**: 모든 인트라 플러그인 경로 참조에 사용. 하드코딩 절대경로(`/Users/...`), 작업 디렉터리 상대경로(`./scripts/...`), 홈 단축(`~/...`) 사용 금지.

**네이밍**: 명령/에이전트/스킬 kebab-case, 스크립트 descriptive kebab-case, 설정은 표준명(hooks.json, .mcp.json, plugin.json).

**일반 패턴**: 최소 플러그인(단일 명령), 풀 피처 플러그인(전체 컴포넌트), 스킬 중심 플러그인.

**트러블슈팅**: 컴포넌트 미로드(위치·확장자·frontmatter 확인), 경로 해석 에러(${CLAUDE_PLUGIN_ROOT}), 자동 발견 실패(루트 위치 확인), 플러그인 충돌(고유 이름).

**참조 파일**: `references/`, `examples/` 디렉터리

## 📦 출처 / 설치 상태
- 플러그인: plugin-dev@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/plugin-dev/unknown/skills/plugin-structure/SKILL.md`
