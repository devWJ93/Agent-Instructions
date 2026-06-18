# agent-development

> Claude Code 플러그인용 서브에이전트의 구조·시스템 프롬프트·트리거 조건 설계를 안내하는 스킬

## 📌 용도
복잡한 다단계 작업을 독립적으로 처리하는 자율 서브에이전트를 만든다. 에이전트는 자율 작업을 위한 것이고, 명령은 사용자 시작 액션을 위한 것이다. YAML frontmatter를 가진 마크다운 파일 형식이며, description 필드와 예시로 트리거되고, 시스템 프롬프트가 동작을 정의한다.

## 🎯 트리거 조건
- **언제 호출되는가**: "에이전트 생성/추가", "서브에이전트 작성", "agent frontmatter", "when to use description", "agent examples/tools/colors", "자율 에이전트" 요청 시. 에이전트 구조·시스템 프롬프트·트리거·베스트 프랙티스 가이드 필요 시.
- **언제 쓰지 않는가**: 슬래시 명령(→ command-development), 스킬(→ skill-development), 훅(→ hook-development) 작성.

## 💻 사용 예시 / 명령어
- "코드 리뷰 에이전트를 만들어줘"
- "이 에이전트의 description을 어떻게 써야 트리거가 잘 돼?"
- "에이전트에 어떤 tools를 줘야 해?"

## 🛠️ 제공 도구 / 주요 파라미터
**에이전트 파일 형식**(`agents/agent-name.md`):
```markdown
---
name: agent-identifier        # 필수: 소문자·숫자·하이픈, 3~50자, 영숫자로 시작/끝
description: Use this agent when [조건]. Typical triggers include [...]. See "When to invoke"...  # 필수, 가장 중요
model: inherit                # 필수: inherit(권장)/sonnet/opus/haiku
color: blue                   # 필수: blue/cyan/green/yellow/magenta/red
tools: ["Read", "Write"]      # 선택: 생략 시 전체 도구 (최소 권한 원칙)
---
You are [역할]...
## When to invoke
- **[시나리오].** [설명.]
```

**frontmatter 필드**:
- **description**(필수, 핵심): 에이전트 등록 시 컨텍스트에 로드되어 디스패치 결정에 사용. 트리거 조건 + 2~4개 시나리오 prose 요약 + body의 "When to invoke" 섹션 포인터 포함. 능동(자기 호출)·수동(사용자 요청) 트리거 모두 커버.
- **color 가이드**: blue/cyan(분석·리뷰), green(성공 작업), yellow(검증), red(보안), magenta(생성).
- **tools 세트**: 읽기 전용 `["Read","Grep","Glob"]`, 코드 생성 `["Read","Write","Grep"]`, 테스트 `["Read","Bash","Grep"]`.

**시스템 프롬프트**(마크다운 body): 2인칭("You are...")으로 작성. 책임/분석 프로세스/품질 기준/출력 형식/엣지 케이스 구조. 10,000자 이하(이상적 500~3,000자).

**생성 방법**: ① AI 보조 생성(Claude Code 추출 프롬프트 패턴, JSON 반환), ② 수동 생성. 검증은 `scripts/validate-agent.sh`, 트리거 테스트는 `scripts/test-agent-trigger.sh`.

**검증 규칙**: identifier 3~50자, description 10~5,000자(이상적 200~1,000자), 시스템 프롬프트 20~10,000자.

**참조 파일**: `references/system-prompt-design.md`, `references/triggering-examples.md`, `references/agent-creation-system-prompt.md`, `examples/agent-creation-prompt.md`, `examples/complete-agent-examples.md`

## 📦 출처 / 설치 상태
- 플러그인: plugin-dev@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/plugin-dev/unknown/skills/agent-development/SKILL.md`
