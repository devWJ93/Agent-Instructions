# update-config

> `settings.json` / `settings.local.json`을 직접 편집해 Claude Code 하네스(harness)를 설정하는 빌트인 스킬. 권한·환경변수·hook·자동 동작을 다룬다.

## 📌 용도
Claude Code의 **동작 자체를 바꾸는 설정 파일**을 수정한다. Claude의 "기억/선호"로는 불가능한, 하네스 레벨의 영구 설정을 담당한다.

핵심 개념: **"앞으로 X 할 때마다 Y 해라" 같은 자동 동작은 Claude의 메모리로 충족할 수 없다.** 이런 자동화는 하네스가 실행하는 **hook**으로만 구현 가능하다. Claude는 매 턴 메모리를 "참고"할 뿐 강제 실행하지 않기 때문이다. 따라서 다음과 같은 요청은 전부 이 스킬이 처리한다.

- **권한(permissions)**: 특정 명령/도구를 자동 허용·거부·확인 목록에 추가. ("npm 명령 허용", "bq 권한을 글로벌 settings에 추가", "이 권한을 user settings로 옮겨라")
- **환경변수(env)**: Claude Code 프로세스에 주입할 환경변수 설정. ("DEBUG=true 설정")
- **hook**: 특정 이벤트(도구 사용 전후, 세션 시작/종료, Claude 응답 종료 등)에 자동 실행할 동작 등록. ("Claude가 응답을 끝내면 X를 표시해라", "커밋 전마다 린트 돌려라")
- **hook 트러블슈팅**: 등록한 hook이 안 먹을 때 원인 진단.
- **settings.json / settings.local.json 관련 모든 변경**.

## 🎯 트리거 조건
- **언제 호출되는가**
  - "앞으로 ~할 때마다 / 매번 / ~하기 전에·후에 ~해줘" 같은 **자동 반복 동작** 요청 (→ hook 필요)
  - "~ 허용해줘 / 권한 추가 / 권한을 다른 settings로 이동" 같은 권한 변경
  - "환경변수 X=Y로 설정" 같은 env 변경
  - hook이 동작하지 않을 때의 디버깅
  - settings.json / settings.local.json 파일을 손대야 하는 모든 작업
- **언제 쓰지 않는가**
  - theme, model 같은 **단순 설정**은 이 스킬 대신 `/config` 명령을 안내 (대화형 UI가 더 빠름)
  - 1회성 작업(자동 반복이 아닌 것)은 hook이 필요 없으므로 그냥 직접 수행
  - 프로젝트 코드 자체의 수정과는 무관

## 💻 사용 예시 / 명령어
- "npm 명령은 매번 허용으로 해줘" → permissions.allow에 npm 패턴 추가
- "DEBUG=true 환경변수 설정해줘" → env에 키 추가
- "claude가 멈출 때(Stop) 알림을 띄워줘" → Stop hook 등록
- "이 bq 권한을 프로젝트가 아니라 글로벌(user) settings로 옮겨줘" → 권한 항목을 파일 간 이동
- "내가 만든 PreToolUse hook이 안 도는데 봐줘" → hook 설정 진단

## 🛠️ 제공 도구 / 주요 파라미터
- **대상 파일**:
  - `settings.json` (프로젝트 공유 설정)
  - `settings.local.json` (프로젝트 로컬·개인 설정, git 제외 권장)
  - user(글로벌) settings (`~/.claude/settings.json`)
- **주요 키 영역**: `permissions`(allow/deny/ask), `env`, `hooks`
- **hook 이벤트(예)**: PreToolUse, PostToolUse, Stop, SubagentStop, SessionStart, SessionEnd, UserPromptSubmit, PreCompact, Notification
- **권한 스코프 구분**: 프로젝트 settings ↔ user settings 간 이동 지원

## 📦 출처 / 설치 상태
- 분류: **빌트인(내장) 스킬** — 별도 SKILL.md 파일 없이 하네스에 내장
- 경로: 없음 (내장). 수정 대상은 `~/.claude/settings.json`, 프로젝트 `.claude/settings.json` / `.claude/settings.local.json`
