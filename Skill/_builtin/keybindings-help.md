# keybindings-help

> `~/.claude/keybindings.json`을 편집해 Claude Code의 키보드 단축키를 커스터마이즈하는 빌트인 스킬.

## 📌 용도
Claude Code 터미널 UI의 **키 바인딩을 사용자 정의**한다. 기본 단축키를 다른 키로 재할당(rebind)하거나, 여러 키를 순차로 누르는 **chord(연속) 바인딩**을 추가하는 등 입력 동작을 바꾼다. 대상 파일은 `~/.claude/keybindings.json` 한 개다.

## 🎯 트리거 조건
- **언제 호출되는가**
  - "단축키 바꿔줘 / 재할당해줘 / rebind 해줘"
  - "ctrl+s를 다른 동작에 연결해줘"
  - "chord 단축키(연속 키 입력) 추가해줘"
  - "제출(submit) 키를 바꾸고 싶어"
  - keybindings.json을 직접 손봐야 하는 모든 작업
- **언제 쓰지 않는가**
  - 권한/환경변수/hook 등 동작 설정은 `update-config`(settings.json) 담당
  - 에디터(VSCode 등) 자체의 단축키와는 무관 — 어디까지나 Claude Code TUI 한정

## 💻 사용 예시 / 명령어
- "ctrl+s를 rebind 해줘"
- "chord 단축키 하나 추가해줘 (예: ctrl+k 다음 ctrl+d)"
- "submit 키를 enter 대신 ctrl+enter로 바꿔줘"
- "기본 키맵을 내 취향대로 커스터마이즈하고 싶어"

## 🛠️ 제공 도구 / 주요 파라미터
- **대상 파일**: `~/.claude/keybindings.json`
- **바인딩 유형**:
  - 단일 키 재할당 (rebind)
  - chord 바인딩 (키 시퀀스 → 동작)
- 액션과 키 조합의 매핑 구조를 편집

## 📦 출처 / 설치 상태
- 분류: **빌트인(내장) 스킬**
- 경로: 없음 (내장). 수정 대상 파일은 `~/.claude/keybindings.json`
