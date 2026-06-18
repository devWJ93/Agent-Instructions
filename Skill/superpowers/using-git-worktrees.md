# using-git-worktrees

> 작업을 격리된 워크스페이스에서 진행하도록, 기존 격리를 먼저 감지하고 네이티브 도구를 우선하며 git worktree는 폴백으로만 쓰는 스킬.

## 📌 용도
작업이 격리된 워크스페이스에서 일어나도록 보장한다. 핵심 원칙은 "기존 격리를 먼저 감지 → 그다음 네이티브 도구 → 그다음 git 폴백 → 절대 하니스와 싸우지 않기"다. 이미 워크트리 안이라면 새로 만들지 않고, 플랫폼의 네이티브 워크트리 도구가 있으면 그것을 쓰며(디렉터리 배치·브랜치 생성·정리를 자동 처리), 없을 때만 수동 `git worktree add`로 폴백한다. 시작 시 "using-git-worktrees 스킬을 사용해 격리 워크스페이스를 설정합니다"라고 알린다.

## 🎯 트리거 조건
- **언제 호출되는가**: 현재 워크스페이스로부터 격리가 필요한 기능 작업을 시작할 때, 또는 구현 계획을 실행하기 전.
- **언제 쓰지 않는가**: Step 0에서 이미 격리됨을 감지하면 생성하지 않음. 사용자가 워크트리를 원하지 않으면 제자리에서 작업.
- **스킬 유형**: Rigid — 감지 우선, 네이티브 우선, ignore 검증 등 순서·규칙 강제.

## 💻 사용 예시 / 호출 방법
- **Step 0 기존 격리 감지**: `GIT_DIR` vs `GIT_COMMON` 비교, `BRANCH` 확인. 서브모듈 가드(`git rev-parse --show-superproject-working-tree`가 경로 반환 시 서브모듈 → 일반 repo 취급). `GIT_DIR != GIT_COMMON`(서브모듈 아님)이면 이미 워크트리 → Step 3로(중첩 생성 금지). 같으면 일반 repo → 사용자 동의 받고 진행.
- **Step 1 워크스페이스 생성**:
  - **1a 네이티브 도구(선호)**: `EnterWorktree`/`WorktreeCreate`/`/worktree`/`--worktree` 등이 있으면 사용하고 Step 3로. 네이티브 도구가 있는데 `git worktree add`를 쓰면 하니스가 못 보는 phantom 상태 발생.
  - **1b git 폴백(네이티브 없을 때만)**: 디렉터리 선택 우선순위 — 사용자 선언 선호 > 기존 `.worktrees`/`worktrees`(둘 다면 `.worktrees`) > 기존 글로벌 `~/.config/superpowers/worktrees/$project` > 기본 `.worktrees/`. 프로젝트-로컬은 `git check-ignore`로 ignore 검증(아니면 .gitignore 추가·커밋). `git worktree add "$path" -b "$BRANCH_NAME"`. 권한 에러(샌드박스)면 제자리 작업 폴백.
- **Step 3 프로젝트 셋업**: package.json→npm install, Cargo.toml→cargo build, requirements.txt→pip install, pyproject.toml→poetry install, go.mod→go mod download 자동 감지·실행.
- **Step 4 클린 베이스라인 검증**: 테스트 실행. 실패 시 보고·질의, 통과 시 ready 보고.

## 🛠️ 제공 내용 / 핵심 절차
- **Quick Reference 표**: 상황별 액션(이미 워크트리→생성 생략, 서브모듈→일반 repo, 네이티브 있음→사용, 둘 다 존재→`.worktrees`, ignore 안 됨→추가·커밋, 권한 에러→제자리, 베이스라인 실패→보고·질의 등).
- **흔한 실수**: 하니스와 싸움(이미 격리 제공하는데 git add), 감지 생략(중첩 워크트리), ignore 검증 생략(워크트리 내용이 추적됨), 디렉터리 위치 가정, 실패 테스트로 진행.
- **Red Flags(절대 금지)**: Step 0이 격리를 감지했는데 워크트리 생성, **네이티브 도구가 있는데 `git worktree add` 사용(1순위 실수)**, Step 1a 건너뛰고 1b로 직행, ignore 검증 없이 프로젝트-로컬 생성, 베이스라인 검증 생략, 실패 테스트로 무단 진행.
- **Always**: Step 0 감지 먼저, 네이티브 우선, 디렉터리 우선순위 준수, 프로젝트-로컬은 ignore 검증, 셋업 자동 실행, 클린 베이스라인 검증.

## 📦 출처 / 설치 상태
- 플러그인: superpowers@claude-plugins-official
- 활성화: ON (글로벌 settings.json enabledPlugins)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/superpowers/5.1.0/skills/using-git-worktrees/SKILL.md`
