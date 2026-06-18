# writing-rules (writing-hookify-rules)

> hookify 규칙 파일(`.claude/hookify.*.local.md`)의 작성 문법과 패턴을 안내하는 스킬.

## 📌 용도
hookify 규칙은 YAML 프론트매터를 가진 마크다운 파일로, "감시할 패턴"과 "패턴이 매치될 때 Claude에게 보여줄 메시지"를 정의한다. 규칙은 `.claude/hookify.{rule-name}.local.md` 파일에 저장된다. 이 스킬은 규칙 파일의 구조, 프론트매터 필드, 이벤트 타입, 정규식 작성 요령, 파일 명명/정리 규칙, 생성·수정·비활성화 워크플로우를 제공한다.

규칙 파일 기본 구조:
```markdown
---
name: rule-identifier
enabled: true
event: bash|file|stop|prompt|all
pattern: regex-pattern-here
---

규칙이 트리거될 때 Claude에게 보여줄 메시지.
```

## 🎯 트리거 조건
- **언제 호출되는가**: 사용자가 "create a hookify rule", "write a hook rule", "configure hookify", "add a hookify rule"를 요청하거나 hookify 규칙 문법·패턴에 대한 안내가 필요할 때. `/hookify`, `/hookify:configure`, `/hookify:list` 커맨드가 실행 시 "먼저 이 스킬을 로드"하라고 명시한다.
- **언제 쓰지 않는가**: hookify 규칙 생성/문법과 무관한 일반 작업. (이 스킬 자체는 규칙 문법 참조용이므로, 단순 코드 작성·디버깅 등에는 사용하지 않는다.)

## 💻 사용 예시 / 명령어
이 스킬은 슬래시 커맨드가 아니라 hookify 커맨드들이 내부적으로 로드하는 참조 스킬이다. 직접 호출보다는 `/hookify` 계열 커맨드 실행 시 자동으로 함께 로드된다.

최소 규칙 예시:
```markdown
---
name: my-rule
enabled: true
event: bash
pattern: dangerous_command
---

Warning message here
```

다중 조건 규칙 예시:
```markdown
---
name: warn-env-file-edits
enabled: true
event: file
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.env$
  - field: new_text
    operator: contains
    pattern: API_KEY
---

You're adding an API key to a .env file. Ensure this file is in .gitignore!
```
(모든 condition이 매치되어야 규칙이 트리거됨.)

## 🛠️ 제공 도구 / 주요 파라미터
프론트매터 필드:
- **name** (필수): 규칙 고유 식별자. kebab-case, 동사로 시작 (warn, prevent, block, require, check).
- **enabled** (필수): `true`/`false`. 삭제 없이 토글 가능.
- **event** (필수): 트리거할 훅 이벤트.
  - `bash`: Bash 도구 명령
  - `file`: Edit/Write/MultiEdit 도구
  - `stop`: 에이전트가 멈추려 할 때 (완료 체크)
  - `prompt`: 사용자 프롬프트 제출 시
  - `all`: 모든 이벤트
- **action** (선택): 매치 시 동작. `warn`(메시지만 보여주고 허용, 기본값) / `block`(작업 차단 또는 세션 중단).
- **pattern** (단순 형식): 매치할 정규식 (Python regex 문법). bash는 command, file은 new_text에 매치.

다중 조건(conditions) 형식:
- `field`: 검사 대상 필드. bash → `command`; file → `file_path`, `new_text`, `old_text`, `content`; prompt → `user_prompt`.
- `operator`: `regex_match`, `contains`, `equals`, `not_contains`, `starts_with`, `ends_with`.
- `pattern`: 매치할 패턴/문자열.

정규식 메타문자: `\s`(공백), `\d`(숫자), `\w`(단어문자), `.`(임의문자), `+`(1+), `*`(0+), `?`(0/1), `|`(OR). YAML에서는 따옴표 없는(unquoted) 패턴 사용 권장 (따옴표 시 `\\s`처럼 이중 백슬래시 필요).

패턴 테스트: `python3 -c "import re; print(re.search(r'your_pattern', 'test text'))"`

파일 정리 규칙:
- 위치: `.claude/` 디렉터리
- 명명: `.claude/hookify.{descriptive-name}.local.md`
- gitignore: `.claude/*.local.md` 추가 권장
- 비활성화: 임시 → `enabled: false`; 영구 → 파일 삭제

규칙은 동적으로 읽히므로 **재시작 없이 다음 도구 사용 시 즉시 반영**된다. 예시 파일은 플러그인의 `${CLAUDE_PLUGIN_ROOT}/examples/` 참고 (dangerous-rm, console-log-warning, sensitive-files-warning 등).

## 📦 출처 / 설치 상태
- 플러그인: hookify@claude-plugins-official
- 활성화: ⛔ **OFF** (settings.json enabledPlugins = false) — 켜야 사용 가능
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/hookify/unknown/skills/writing-rules/SKILL.md`
