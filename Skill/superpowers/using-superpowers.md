# using-superpowers

> 모든 대화 시작 시, 어떤 응답(명확화 질문 포함)보다 먼저 관련 스킬을 찾아 Skill 도구로 호출하도록 규율을 세우는 메타 스킬.

## 📌 용도
스킬을 어떻게 찾고 사용하는지의 기준을 확립한다. "작업에 스킬이 적용될 가능성이 1%라도 있으면 반드시 그 스킬을 호출하라"는 강한 규칙을 강제한다. 스킬이 적용되면 선택의 여지가 없고 반드시 사용해야 하며, 합리화로 빠져나갈 수 없다. 단, 사용자의 명시적 지시(CLAUDE.md/GEMINI.md/AGENTS.md/직접 요청)가 항상 최우선이고, 그다음이 superpowers 스킬, 마지막이 기본 시스템 프롬프트다. 호출한 스킬이 상황에 안 맞으면 안 쓰면 된다.

## 🎯 트리거 조건
- **언제 호출되는가**: 모든 대화를 시작할 때. 사용자 메시지 수신 직후, 그리고 EnterPlanMode 직전(아직 브레인스토밍 안 했으면 brainstorming 먼저).
- **언제 쓰지 않는가(SUBAGENT-STOP)**: 특정 태스크 실행을 위해 서브에이전트로 디스패치된 경우 이 스킬을 건너뛴다.
- **스킬 유형**: Rigid — EXTREMELY-IMPORTANT 블록으로 비협상·비선택 규칙 강제.

## 💻 사용 예시 / 호출 방법
규칙: 관련/요청된 스킬을 어떤 응답·행동보다 먼저 호출한다(명확화 질문 포함). 흐름:
- 사용자 메시지 수신 → "스킬이 적용될 수 있나?"(1%라도 yes면) → Skill 도구 호출 → "Using [skill] to [purpose]" 알림 → 체크리스트 있으면 항목별 TodoWrite 생성 → 스킬을 정확히 따름 → 응답(명확화 포함).
- EnterPlanMode 직전 → 이미 브레인스토밍 했나? 아니면 brainstorming 호출.

**스킬 접근 방법**: Claude Code는 `Skill` 도구(스킬 파일에 Read 도구 절대 사용 금지). Copilot CLI는 `skill` 도구. Gemini CLI는 `activate_skill`. 비-CC 플랫폼 도구 매핑은 `references/copilot-tools.md`, `references/codex-tools.md` 참조.

## 🛠️ 제공 내용 / 핵심 절차
- **지시 우선순위**: ①사용자 명시 지시(최우선) ②superpowers 스킬(기본 동작 오버라이드) ③기본 시스템 프롬프트. CLAUDE.md가 "TDD 쓰지 마"라고 하면 스킬이 "항상 TDD"라 해도 사용자 지시를 따름.
- **Red Flags(합리화이니 STOP)**: "단순한 질문일 뿐", "컨텍스트가 먼저 필요", "코드베이스부터 탐색", "git/파일 빨리 확인", "정보부터 수집", "정식 스킬 필요 없음", "이 스킬 기억함", "태스크 아님", "스킬이 과함", "한 가지만 먼저", "생산적인 느낌", "그 의미는 안다" → 모두 스킬을 먼저 확인/호출하라는 신호.
- **스킬 우선순위**: ①프로세스 스킬 먼저(brainstorming, debugging — 접근 방식 결정) ②구현 스킬 다음(frontend-design, mcp-builder). "X를 만들자" → brainstorming 먼저. "이 버그 고쳐" → debugging 먼저.
- **스킬 유형**: Rigid(TDD·debugging — 정확히 따름, 규율을 적응시켜 없애지 말 것) / Flexible(패턴 — 맥락에 맞게 원칙 적용). 스킬 자체가 어느 쪽인지 알려준다.
- **사용자 지시**: 지시는 무엇(WHAT)을 말할 뿐 어떻게(HOW)가 아니다. "X 추가"·"Y 고쳐"가 워크플로우 생략을 뜻하지 않는다.

## 📦 출처 / 설치 상태
- 플러그인: superpowers@claude-plugins-official
- 활성화: ON (글로벌 settings.json enabledPlugins)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/superpowers/5.1.0/skills/using-superpowers/SKILL.md`
