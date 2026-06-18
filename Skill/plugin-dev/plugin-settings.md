# plugin-settings

> `.claude/plugin-name.local.md` 패턴으로 플러그인별 설정·상태를 YAML frontmatter와 마크다운으로 저장하는 스킬

## 📌 용도
플러그인이 사용자 구성 가능한 설정과 상태를 프로젝트 디렉터리의 `.claude/plugin-name.local.md` 파일에 저장하도록 한다. YAML frontmatter로 구조화된 설정을, 마크다운 본문으로 프롬프트·추가 컨텍스트를 담는다.

**특성**:
- 위치: 프로젝트 루트의 `.claude/plugin-name.local.md`
- 구조: YAML frontmatter + 마크다운 본문
- 용도: 프로젝트별 플러그인 구성·상태
- 사용처: 훅, 명령, 에이전트에서 읽기
- 라이프사이클: 사용자 관리(git 제외, `.gitignore`에 추가)

## 🎯 트리거 조건
- **언제 호출되는가**: "플러그인 설정", "플러그인 구성 저장", "사용자 구성 가능 플러그인", ".local.md 파일", "플러그인 상태 파일", "YAML frontmatter 읽기", "프로젝트별 플러그인 설정", 플러그인 동작을 구성 가능하게 만들고 싶을 때.
- **언제 쓰지 않는가**: 훅/명령/에이전트 로직 자체 작성은 해당 전용 스킬.

## 💻 사용 예시 / 명령어
- "플러그인 설정을 .local.md로 저장하게 해줘"
- "프로젝트마다 다른 동작을 하도록 구성 파일을 읽게 하고 싶어"
- "훅을 켜고 끌 수 있는 설정 플래그를 추가해줘"

## 🛠️ 제공 도구 / 주요 파라미터
**기본 템플릿**:
```markdown
---
enabled: true
mode: standard
max_retries: 3
list_setting: ["item1", "item2"]
---
# Additional Context
프롬프트·지시·문서 등
```

**설정 읽기**:
- **훅(bash)**: frontmatter 파싱 `FRONTMATTER=$(sed -n '/^---$/,/^---$/{ /^---$/d; p; }' "$FILE")`, 필드 추출 `grep '^field:' | sed 's/field: *//'`, 본문 `awk '/^---$/{i++; next} i>=2'`.
- **명령/에이전트**: Read 도구로 읽고 frontmatter 파싱해 동작 커스터마이즈.

**공통 패턴**:
1. **조건부 활성 훅**: 설정 파일로 훅 활성화 제어(hooks.json 수정 없이, quick-exit 패턴).
2. **에이전트 상태 관리**: agent_name, coordinator_session 등 저장(multi-agent-swarm 사례).
3. **구성 기반 동작**: validation_level 등으로 case 분기(ralph-loop 사례).

**베스트 프랙티스**:
- 파일명: `.claude/plugin-name.local.md`(플러그인명 정확히 일치, `.local.md` 접미사)
- `.gitignore`에 `.claude/*.local.md` 추가
- 설정 파일 없을 때 sensible 기본값 제공
- 값 검증(숫자 범위 등)
- **변경 시 Claude Code 재시작 필요**(훅 핫스왑 불가)

**보안**: 사용자 입력 sanitize(따옴표 이스케이프), 파일 경로 traversal 검증, 권한(`chmod 600`, git 제외).

**참조 파일**: `references/parsing-techniques.md`, `references/real-world-examples.md`; `examples/read-settings-hook.sh`, `create-settings-command.md`, `example-settings.md`; `scripts/validate-settings.sh`, `parse-frontmatter.sh`

## 📦 출처 / 설치 상태
- 플러그인: plugin-dev@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/plugin-dev/unknown/skills/plugin-settings/SKILL.md`
