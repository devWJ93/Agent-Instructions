# executing-plans

> 작성된 구현 계획을, 별도 세션에서 리뷰 체크포인트를 두며 태스크 단위로 실행하는 스킬.

## 📌 용도
이미 작성된 구현 계획(plan)을 로드하고, 비판적으로 검토한 뒤, 모든 태스크를 순서대로 실행하고 완료를 보고한다. 시작 시 "executing-plans 스킬을 사용해 이 계획을 구현합니다"라고 알린다. 서브에이전트를 쓸 수 없는 환경에서의 인라인(같은 세션 배치) 실행 방식이며, 서브에이전트가 가능하다면 품질이 훨씬 높은 `subagent-driven-development`를 대신 쓰도록 안내한다. 계획의 각 태스크는 잘게 쪼개진 단계로 구성되어 있어 그대로 따라가면 된다.

## 🎯 트리거 조건
- **언제 호출되는가**: 작성된 구현 계획이 있고, 이를 리뷰 체크포인트를 두며 (별도/인라인) 세션에서 실행할 때. `writing-plans`가 만든 계획의 실행 단계.
- **언제 쓰지 않는가**: 서브에이전트 지원 플랫폼(Claude Code/Codex)이라면 `subagent-driven-development`를 우선. 아직 계획이 없으면 먼저 brainstorming/writing-plans.
- **스킬 유형**: Rigid에 가까움 — 계획 단계를 정확히 따르고 검증을 건너뛰지 않으며, 막히면 추측하지 말고 멈춘다.

## 💻 사용 예시 / 호출 방법
프로세스:
1. **계획 로드·검토** — 계획 파일 읽기 → 비판적 검토(질문/우려 식별) → 우려 있으면 시작 전 사용자에게 제기, 없으면 TodoWrite 생성 후 진행.
2. **태스크 실행** — 각 태스크: in_progress 표시 → 단계를 정확히 따름 → 명시된 검증 실행 → completed 표시.
3. **개발 완료** — 모든 태스크 완료·검증 후 "finishing-a-development-branch 스킬을 사용합니다" 알리고 **필수 서브스킬** `finishing-a-development-branch` 실행(테스트 검증, 옵션 제시, 선택 실행).

## 🛠️ 제공 내용 / 핵심 절차
- **즉시 멈추고 도움을 요청해야 할 때**: 블로커(의존성 누락, 테스트 실패, 지시 불명확), 계획에 시작을 막는 치명적 공백, 지시 이해 불가, 검증 반복 실패. → 추측하지 말고 명확화 요청.
- **이전 단계로 돌아갈 때**: 파트너가 피드백 기반으로 계획 수정 시, 근본 접근을 재고할 때 → 검토(Step 1)로 복귀.
- **기억할 것**: 계획 비판적 검토 우선 / 단계 정확히 따름 / 검증 건너뛰지 않기 / 계획이 지시하면 스킬 참조 / 막히면 멈추고 추측 금지 / **명시적 동의 없이 main·master 브랜치에서 구현 시작 금지**.
- **연계 필수 스킬**: `using-git-worktrees`(격리 워크스페이스), `writing-plans`(이 계획 생성), `finishing-a-development-branch`(완료 처리).

## 📦 출처 / 설치 상태
- 플러그인: superpowers@claude-plugins-official
- 활성화: ON (글로벌 settings.json enabledPlugins)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/superpowers/5.1.0/skills/executing-plans/SKILL.md`
