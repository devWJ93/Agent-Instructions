# /hookify

> 대화 분석이나 명시적 지시로부터 원치 않는 행동을 막는 hookify 규칙을 생성하는 커맨드.

## 📌 용도
사용자가 겪은 문제 행동을 분석하여, 이를 방지하는 hookify 규칙 파일을 만든다. 인자로 받은 명시적 지시 또는 최근 대화 분석을 통해 막을 행동을 찾아내고, 사용자 확인을 거쳐 `.claude/hookify.{rule-name}.local.md` 파일을 생성한다. 실행 시 먼저 `hookify:writing-rules` 스킬을 로드해 규칙 형식을 이해한 뒤 진행한다.

## 🎯 트리거 조건
- **언제 호출되는가**: 원치 않는 행동(위험한 명령, 디버그 코드, 완료 전 누락 단계 등)을 막는 규칙을 만들고 싶을 때. 인자 있으면 명시적 지시 기반, 인자 없으면 대화 분석 기반.
- **언제 쓰지 않는가**: 기존 규칙의 활성/비활성 토글(→ `/hookify:configure`), 규칙 목록 확인(→ `/hookify:list`), 사용법 안내(→ `/hookify:help`)일 때.

## 💻 사용 예시 / 명령어
```
/hookify                                          # 인자 없음 → 대화 분석으로 문제 행동 탐지
/hookify Don't use console.log in production files
/hookify Don't use rm -rf without asking me first
```
- 인자(argument-hint): "Optional specific behavior to address" — 다룰 특정 행동을 선택적으로 지정.

## 🛠️ 제공 도구 / 주요 파라미터
allowed-tools: `Read`, `Write`, `AskUserQuestion`, `Task`, `Grep`, `TodoWrite`, `Skill`.

절차:
1. **행동 정보 수집**: `$ARGUMENTS`가 있으면 그 지시 + 최근 대화(마지막 10-15개 사용자 메시지) 분석. 비어 있으면 Task 도구로 conversation-analyzer 에이전트(general-purpose)를 띄워, 명시적 회피 요청·정정/되돌림·불만 반응·반복 문제를 찾아 category/tool/pattern/context/severity 구조로 반환받는다.
2. **사용자에게 제시**: AskUserQuestion으로 (1) 어떤 행동을 규칙화할지 multiSelect, (2) 각 행동을 block할지 warn할지, (3) 트리거 패턴 확인.
3. **규칙 파일 생성**: 확정된 행동마다 `.claude/hookify.{rule-name}.local.md` 작성. name은 kebab-case·동사 시작(block/warn/prevent/require). 단순 패턴 또는 conditions(다중 조건) 형식 사용. action은 `warn`(기본)/`block`.
4. **생성 및 확인**: 파일은 반드시 현재 작업 디렉터리의 `.claude/` 폴더에 생성(플러그인 디렉터리가 아님). 없으면 `mkdir -p .claude`. 생성 후 목록으로 검증하고 "재시작 없이 즉시 활성화"임을 알림. 진행 상황은 TodoWrite로 추적.

## 📦 출처 / 설치 상태
- 플러그인: hookify@claude-plugins-official
- 활성화: ⛔ **OFF** (settings.json enabledPlugins = false) — 켜야 사용 가능
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/hookify/unknown/commands/hookify.md`
