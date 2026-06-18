# finishing-a-development-branch

> 구현이 끝나고 테스트가 통과한 뒤, 작업을 어떻게 통합할지(머지/PR/유지/폐기) 구조화된 선택지로 안내하고 워크스페이스를 정리하는 스킬.

## 📌 용도
개발 작업 완료를 안내한다. 핵심 흐름은 "테스트 검증 → 환경 감지 → 옵션 제시 → 선택 실행 → 정리"다. 먼저 프로젝트 테스트 스위트를 돌려 통과를 확인하고(실패 시 중단), 워크스페이스 상태(일반 repo / 워크트리 / detached HEAD)를 감지해 알맞은 메뉴를 보여준다. 그 다음 정확히 4개(또는 detached HEAD는 3개) 옵션을 제시하고, 사용자가 선택한 워크플로우를 실행한 뒤, 자기가 만든 워크트리만 정리한다. 시작 시 "finishing-a-development-branch 스킬을 사용해 이 작업을 완료합니다"라고 알린다.

## 🎯 트리거 조건
- **언제 호출되는가**: 구현이 완료되고 모든 테스트가 통과하여, 작업을 머지/PR/유지/폐기 중 어떻게 통합할지 결정해야 할 때. `executing-plans`·`subagent-driven-development`의 마지막 단계.
- **언제 쓰지 않는가**: 아직 구현이 끝나지 않았거나 테스트가 실패 중일 때(먼저 고칠 것).
- **스킬 유형**: Rigid — 테스트 통과 전 진행 금지, 정확한 옵션 수 제시, Discard는 타이핑 확인 필수, 워크트리 정리 규칙 엄격.

## 💻 사용 예시 / 호출 방법
프로세스:
1. **테스트 검증** — `npm test`/`cargo test`/`pytest`/`go test ./...`. 실패 시 중단.
2. **환경 감지** — `git rev-parse --git-dir` vs `--git-common-dir` 비교. 일반 repo / named-branch 워크트리 / detached HEAD 구분.
3. **베이스 브랜치 결정** — `git merge-base HEAD main/master` 또는 사용자에게 질의.
4. **옵션 제시** — 일반/워크트리: 4개(① 로컬 머지 ② 푸시+PR ③ 그대로 유지 ④ 폐기). detached HEAD: 3개(① 새 브랜치 푸시+PR ② 유지 ③ 폐기).
5. **선택 실행** — 머지는 "머지 성공 확인 후" 워크트리 제거·브랜치 삭제 순서. PR은 워크트리 보존(PR 반복 작업용).
6. **워크스페이스 정리** — 옵션 1·4에서만. `.worktrees/`·`worktrees/`·`~/.config/superpowers/worktrees/` 아래의 자기가 만든 워크트리만 제거.

## 🛠️ 제공 내용 / 핵심 절차
- **옵션별 빠른 참조**: ①머지 로컬(머지O·브랜치삭제O) ②PR(푸시O·워크트리유지O) ③유지(워크트리유지O) ④폐기(강제삭제, 워크트리정리O).
- **흔한 실수**: 테스트 검증 생략 / 개방형 질문("뭐 할까요?") / 옵션 2에서 워크트리 제거 / 워크트리 제거 전 브랜치 삭제(`git branch -d` 실패) / 워크트리 내부에서 `git worktree remove` 실행 / 하니스 소유 워크트리 정리 / Discard 확인 없음.
- **Red Flags(절대 금지)**: 실패 테스트로 진행, 결과 검증 없이 머지, 확인 없이 작업 삭제, 명시 요청 없는 force-push, 머지 성공 확인 전 워크트리 제거, 자기가 안 만든 워크트리 정리(provenance 체크), 워크트리 내부에서 remove 실행.
- **Always**: 옵션 제시 전 테스트 검증, 메뉴 전 환경 감지, 정확히 4(또는 3)개 옵션, 옵션 4는 "discard" 타이핑 확인, 옵션 1·4만 워크트리 정리, remove 전 main repo 루트로 cd, remove 후 `git worktree prune`.

## 📦 출처 / 설치 상태
- 플러그인: superpowers@claude-plugins-official
- 활성화: ON (글로벌 settings.json enabledPlugins)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/superpowers/5.1.0/skills/finishing-a-development-branch/SKILL.md`
