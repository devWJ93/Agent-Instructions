# /hookify:help

> hookify 플러그인의 동작 원리와 사용법을 설명하는 도움말 커맨드.

## 📌 용도
hookify 플러그인이 어떻게 작동하는지, 규칙을 어떻게 만들고 쓰는지 설명한다. hookify는 `hooks.json`을 직접 편집하지 않고도, 감시할 패턴을 정의한 간단한 마크다운 설정 파일(`.claude/hookify.*.local.md`)로 커스텀 훅을 만들 수 있게 해준다. 이 커맨드는 훅 시스템·설정 파일·규칙 생성 방법·패턴 문법·문제 해결·시작 가이드를 한 화면에 정리해 보여준다.

## 🎯 트리거 조건
- **언제 호출되는가**: hookify 플러그인의 개념·동작·사용법을 알고 싶을 때.
- **언제 쓰지 않는가**: 실제 규칙 생성(→ `/hookify`), 목록 조회(→ `/hookify:list`), 활성/비활성 토글(→ `/hookify:configure`)일 때.

## 💻 사용 예시 / 명령어
```
/hookify:help
```
- 인자 없음. 출력에 포함되는 다른 커맨드 안내:
  - `/hookify` — 대화 분석 또는 명시적 지시로 훅 생성
  - `/hookify:help` — 이 도움말
  - `/hookify:list` — 설정된 훅 목록
  - `/hookify:configure` — 기존 훅 활성/비활성 토글

## 🛠️ 제공 도구 / 주요 파라미터
allowed-tools: `Read`.

설명하는 핵심 내용:
- **훅 시스템**: hookify는 PreToolUse / PostToolUse / Stop / UserPromptSubmit 이벤트에서 동작하는 범용 훅을 설치하고, `.claude/hookify.*.local.md` 설정을 읽어 현재 작업과 매치되는 규칙이 있는지 검사한다.
- **설정 파일 필드**: `name`, `enabled`, `event`(bash/file/stop/prompt/all), `pattern`(정규식). 메시지 본문이 트리거 시 Claude에게 보이는 내용.
- **규칙 생성 3가지 방법**: `/hookify <지시>` / 수동 파일 생성 / 인자 없는 `/hookify`(대화 분석).
- **패턴 문법**: Python regex (`\s`, `\.`, `|`, `+`, `*`, `\d`, `[abc]` 등).
- **중요 사항**: 재시작 불필요(즉시 반영), block/warn 선택(`action` 필드), 규칙 파일은 git-ignore 권장, `enabled: false` 또는 파일 삭제로 비활성화.
- **문제 해결**: 훅 미트리거 시 위치·enabled·정규식 점검(`python3 -c "import re; ..."`), import 오류 시 `python3 --version` 확인, 패턴 미매치 시 이스케이프 점검.

## 📦 출처 / 설치 상태
- 플러그인: hookify@claude-plugins-official
- 활성화: ⛔ **OFF** (settings.json enabledPlugins = false) — 켜야 사용 가능
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/hookify/unknown/commands/help.md`
