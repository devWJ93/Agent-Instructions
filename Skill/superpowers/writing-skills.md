# writing-skills

> 새 스킬을 만들거나 기존 스킬을 수정·검증할 때, 프로세스 문서화에 TDD(RED-GREEN-REFACTOR)를 적용하는 스킬.

## 📌 용도
스킬 작성은 곧 프로세스 문서화에 적용된 TDD다. 테스트 케이스(서브에이전트 압박 시나리오)를 쓰고, 실패를 지켜보고(스킬 없는 기준선 동작), 스킬(문서)을 쓰고, 통과를 지켜보고(에이전트가 준수), 리팩토링(허점 봉쇄)한다. 핵심 원칙은 "스킬 없이 에이전트가 실패하는 걸 보지 않았다면, 그 스킬이 옳은 것을 가르치는지 알 수 없다"이다. 철의 법칙: **실패 테스트 없이는 스킬 금지** — 신규 스킬뿐 아니라 기존 스킬 수정에도 적용. **필수 배경**으로 `test-driven-development`를 먼저 이해해야 한다.

## 🎯 트리거 조건
- **언제 호출되는가**: 새 스킬 생성, 기존 스킬 편집, 배포 전 스킬 검증.
- **언제 쓰지 않는가(스킬을 만들지 말 것)**: 일회성 해법, 다른 곳에 잘 문서화된 표준 관행, 프로젝트 한정 관례(CLAUDE.md에), 기계적 제약(regex/검증으로 강제 가능하면 자동화 — 문서는 판단이 필요한 것만).
- **스킬 유형**: Rigid — TDD와 동일한 철의 법칙, 테스트 없는 배포 금지를 강제.

## 💻 사용 예시 / 호출 방법
RED-GREEN-REFACTOR for Skills:
- **RED(기준선)**: 스킬 없이 압박 시나리오를 서브에이전트로 실행, 정확한 동작·합리화(verbatim)·어떤 압박이 위반을 유발했는지 문서화.
- **GREEN(최소 스킬)**: 그 구체적 합리화를 다루는 스킬 작성(가상 케이스용 추가 내용 금지). 같은 시나리오를 스킬과 함께 실행해 준수 확인.
- **REFACTOR(허점 봉쇄)**: 새 합리화 발견 시 명시적 반박 추가, 방탄될 때까지 재테스트. (`@testing-skills-with-subagents.md`에 압박 유형·시나리오 작성법.)

SKILL.md 구조: frontmatter(`name`·`description` 필수, 최대 1024자) + Overview(핵심 원칙) + When to Use(증상·미사용 시점) + Core Pattern(before/after) + Quick Reference(표) + Implementation + Common Mistakes + (선택)Real-World Impact.

## 🛠️ 제공 내용 / 핵심 절차
- **CSO(Claude Search Optimization)**: ①Rich Description — "Use when..."으로 시작, **트리거 조건만** 기술하고 워크플로우/프로세스를 요약하지 말 것(요약하면 Claude가 스킬 본문 대신 description을 따르는 함정. 실제로 "리뷰 between tasks"라는 요약이 2단계 리뷰를 1번으로 만들었음). 3인칭, 문제(race condition)를 기술하되 언어별 증상(setTimeout)은 피함, 기술 한정이면 명시. ②키워드 커버리지(에러 메시지·증상·동의어·도구). ③서술적 명명(능동·동사 우선, gerund: `creating-skills`). ④토큰 효율(자주 로드되는 스킬은 <200단어, 상세는 tool help/교차참조로).
- **교차참조**: 스킬 이름만 + 명시 마커(`**REQUIRED SUB-SKILL:** Use superpowers:...`). `@` 링크 금지(즉시 force-load로 컨텍스트 소모).
- **스킬 유형별 테스트**: 규율형(학술 질문+압박 시나리오, 최대 압박 하 준수), 기법형(적용·변형·누락 정보 테스트), 패턴형(인식·적용·반례), 참조형(검색·적용·갭).
- **방탄화**: 모든 허점을 명시적으로 봉쇄("reference 보관 금지"·"적응 금지"·"보기 금지"·"삭제는 삭제"), "문구 vs 정신" 논쟁 차단("문구를 어기는 것은 정신을 어기는 것"), 합리화 표 구축, Red Flags 목록 생성, 위반 직전 증상을 description에 추가.
- **플로우차트**: 비자명한 결정점·멈추기 쉬운 루프·"A vs B"에만. 참조/코드/선형 지시에는 금지(표/리스트/번호). 스타일은 `@graphviz-conventions.dot`, 렌더링은 `render-graphs.js`.
- **STOP 규칙**: 어떤 스킬을 쓴 뒤 반드시 멈추고 배포 프로세스 완료 — 배치로 여러 스킬 만들기/테스트 전 다음 스킬로 이동 금지. 배포 전 테스트는 각 스킬마다 필수.
- **체크리스트**(TodoWrite로 각 항목): RED(시나리오·기준선·패턴), GREEN(명명·frontmatter·description·키워드·예시·준수 확인), REFACTOR(새 합리화·반박·표·red flags·재테스트), Quality, Deployment(커밋·PR).
- **참조 파일**: `anthropic-best-practices.md`, `persuasion-principles.md`, `testing-skills-with-subagents.md`, `graphviz-conventions.dot`.

## 📦 출처 / 설치 상태
- 플러그인: superpowers@claude-plugins-official
- 활성화: ON (글로벌 settings.json enabledPlugins)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/superpowers/5.1.0/skills/writing-skills/SKILL.md`
