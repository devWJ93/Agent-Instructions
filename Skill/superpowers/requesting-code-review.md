# requesting-code-review

> 코드 리뷰어 서브에이전트를 디스패치해, 문제가 연쇄되기 전에 작업 산출물을 검토받는 스킬.

## 📌 용도
이슈가 눈덩이처럼 커지기 전에 잡아내기 위해 코드 리뷰어 서브에이전트를 보낸다. 리뷰어에게는 세션 히스토리가 아니라 정밀하게 가공된 컨텍스트만 준다. 이렇게 하면 리뷰어가 사고 과정이 아닌 작업 산출물에 집중하고, 메인 컨텍스트도 후속 작업용으로 보존된다. 핵심 원칙은 "일찍, 자주 리뷰"다.

## 🎯 트리거 조건
- **언제 호출되는가(필수)**: subagent-driven-development의 각 태스크 후, 주요 기능 완료 후, main 머지 전. (선택이지만 유용: 막혔을 때, 리팩토링 전 기준선 확인, 복잡한 버그 수정 후.)
- **언제 쓰지 않는가**: "단순하니까"라며 건너뛰는 것은 금지(Red Flag). 리뷰가 불필요한 상황은 명시되지 않음.
- **스킬 유형**: Flexible — 워크플로우 통합 가이드. 단, "리뷰 건너뛰기 금지" 등 일부 규칙은 강제.

## 💻 사용 예시 / 호출 방법
1. **git SHA 확보**: `BASE_SHA=$(git rev-parse HEAD~1)`(또는 origin/main), `HEAD_SHA=$(git rev-parse HEAD)`.
2. **리뷰어 서브에이전트 디스패치**: Task 도구를 `general-purpose` 타입으로, `code-reviewer.md` 템플릿을 채워 사용. 플레이스홀더 — `{DESCRIPTION}`(만든 것 요약), `{PLAN_OR_REQUIREMENTS}`(해야 할 일), `{BASE_SHA}`, `{HEAD_SHA}`.
3. **피드백 대응**: Critical 즉시 수정, Important 진행 전 수정, Minor 나중 기록, 리뷰어가 틀리면 근거 들어 반박.

예시: Task 2(검증 함수 추가) 완료 → SHA 확보 → 리뷰어 디스패치(DESCRIPTION/PLAN/BASE/HEAD) → 리뷰어 반환(강점: 깨끗한 아키텍처·실제 테스트 / 이슈: Important 진행 표시 누락, Minor 매직넘버 100) → 진행 표시 수정 → Task 3로.

## 🛠️ 제공 내용 / 핵심 절차
- **참조 템플릿**: `requesting-code-review/code-reviewer.md`.
- **워크플로우 통합**: Subagent-Driven은 매 태스크 후 리뷰(이슈 누적 전 수정). Executing Plans는 각 태스크/자연스러운 체크포인트에서. Ad-Hoc은 머지 전·막혔을 때.
- **Red Flags(절대 금지)**: "단순하니까" 리뷰 생략, Critical 무시, Important 미해결 진행, 타당한 기술 피드백에 말다툼.
- **리뷰어가 틀렸을 때**: 기술적 근거로 반박, 동작 증명 코드/테스트 제시, 명확화 요청.

## 📦 출처 / 설치 상태
- 플러그인: superpowers@claude-plugins-official
- 활성화: ON (글로벌 settings.json enabledPlugins)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/superpowers/5.1.0/skills/requesting-code-review/SKILL.md`
