# subagent-driven-development

> 구현 계획을 같은 세션에서 실행하되, 태스크마다 새 서브에이전트를 디스패치하고 2단계 리뷰(스펙 준수 → 코드 품질)를 거치는 스킬.

## 📌 용도
계획의 각 태스크를 신선한(fresh) 서브에이전트에게 위임하고, 매 태스크 후 두 단계 리뷰를 수행한다: 먼저 스펙 준수 리뷰, 그 다음 코드 품질 리뷰. 서브에이전트는 메인 세션 컨텍스트/히스토리를 절대 상속하지 않으며, 컨트롤러가 필요한 것만 정밀하게 구성해 전달한다. 핵심 원칙은 "태스크당 새 서브에이전트 + 2단계 리뷰(스펙→품질) = 고품질·빠른 반복"이다. 태스크 사이에 사용자에게 확인하지 않고 모든 태스크를 연속 실행한다("계속할까요?" 프롬프트는 시간 낭비). 멈추는 유일한 이유는 해결 불가한 BLOCKED, 진행을 막는 모호함, 또는 전체 완료다.

## 🎯 트리거 조건
- **언제 호출되는가**: 구현 계획이 있고, 태스크가 대체로 독립적이며, 현재 세션에 머물러 실행할 때.
- **언제 쓰지 않는가**: 계획 없음(→ 수동 실행 또는 먼저 brainstorm), 태스크가 강하게 결합됨, 병렬 세션을 원함(→ `executing-plans`).
- **스킬 유형**: Rigid — 리뷰 순서/루프, 단일 구현 서브에이전트, main 브랜치 보호 등 엄격한 규칙.

## 💻 사용 예시 / 호출 방법
프로세스(태스크별 반복):
1. 계획을 한 번 읽어 모든 태스크의 전문(full text)·컨텍스트 추출, TodoWrite 생성.
2. **구현 서브에이전트 디스패치**(`./implementer-prompt.md`). 질문하면 답하고 컨텍스트 제공.
3. 구현자가 구현·테스트·커밋·셀프리뷰.
4. **스펙 리뷰어 디스패치**(`./spec-reviewer-prompt.md`) → 스펙 불일치면 구현자가 수정 후 재리뷰.
5. 스펙 ✅ 후에만 **코드 품질 리뷰어 디스패치**(`./code-quality-reviewer-prompt.md`) → 미승인이면 수정 후 재리뷰.
6. 둘 다 통과 시 TodoWrite 완료 표시. 다음 태스크로.
7. 모든 태스크 후 **최종 코드 리뷰어**(전체 구현) 디스패치 → `finishing-a-development-branch`.

## 🛠️ 제공 내용 / 핵심 절차
- **모델 선택**: 역할별 최소 충분 모델. 1~2파일·완전한 스펙의 기계적 작업 → 싸고 빠른 모델. 다중 파일·통합 → 표준 모델. 아키텍처/설계/리뷰 → 가장 강력한 모델.
- **구현자 상태 4가지**: DONE(스펙 리뷰로) / DONE_WITH_CONCERNS(우려 읽고 정확성·스코프 문제면 먼저 처리) / NEEDS_CONTEXT(컨텍스트 보충 후 재디스패치) / BLOCKED(컨텍스트 문제면 보충, 추론 필요면 더 강한 모델, 너무 크면 분할, 계획이 틀렸으면 사람에게 에스컬레이션). 변경 없이 같은 모델로 재시도 금지.
- **프롬프트 템플릿**: `implementer-prompt.md`, `spec-reviewer-prompt.md`, `code-quality-reviewer-prompt.md`.
- **Red Flags(절대 금지)**: 동의 없이 main/master에서 구현 시작, 리뷰(스펙/품질) 생략, 미해결 이슈로 진행, 구현 서브에이전트 병렬 디스패치(충돌), 서브에이전트에게 계획 파일 읽히기(전문 제공할 것), scene-setting 컨텍스트 생략, 서브에이전트 질문 무시, 스펙 준수 "close enough" 수용, 리뷰 루프 생략, 셀프리뷰로 실제 리뷰 대체, **스펙 ✅ 전에 품질 리뷰 시작(순서 오류)**, 어느 리뷰든 미해결 이슈로 다음 태스크 이동.
- **품질 게이트**: 셀프리뷰 → 2단계 리뷰(스펙→품질) → 리뷰 루프로 수정 확정. 스펙 준수는 과/소 구현 방지, 코드 품질은 잘 만들어졌는지 보장.
- **연계 필수 스킬**: `using-git-worktrees`, `writing-plans`, `requesting-code-review`(리뷰어 템플릿), `finishing-a-development-branch`. 서브에이전트는 `test-driven-development` 사용. 대안: 병렬 세션은 `executing-plans`.

## 📦 출처 / 설치 상태
- 플러그인: superpowers@claude-plugins-official
- 활성화: ON (글로벌 settings.json enabledPlugins)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/superpowers/5.1.0/skills/subagent-driven-development/SKILL.md`
