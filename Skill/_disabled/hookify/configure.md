# /hookify:configure

> 기존 hookify 규칙을 인터랙티브하게 활성/비활성 토글하는 커맨드.

## 📌 용도
이미 만들어진 hookify 규칙들을 대화형 인터페이스로 켜거나 끈다. 규칙 파일을 찾아 현재 상태(enabled)를 읽고, 사용자가 토글할 규칙을 선택하면 해당 파일의 `enabled` 값을 뒤집는다. 규칙을 삭제하지 않고 상태만 전환한다. 실행 시 먼저 `hookify:writing-rules` 스킬을 로드한다.

## 🎯 트리거 조건
- **언제 호출되는가**: 기존 hookify 규칙을 일시적으로 켜거나 끄고 싶을 때.
- **언제 쓰지 않는가**: 새 규칙 생성(→ `/hookify`), 규칙 목록만 보기(→ `/hookify:list`), 규칙 영구 삭제(→ `.local.md` 파일 직접 삭제).

## 💻 사용 예시 / 명령어
```
/hookify:configure
```
- 인자 없음. 실행 시 규칙 목록을 제시하고 AskUserQuestion으로 토글 대상을 multiSelect로 선택받는다.

## 🛠️ 제공 도구 / 주요 파라미터
allowed-tools: `Glob`, `Read`, `Edit`, `AskUserQuestion`, `Skill`.

절차:
1. **규칙 찾기**: Glob 패턴 `.claude/hookify.*.local.md`. 없으면 "`/hookify`로 먼저 규칙을 만들라" 안내.
2. **현재 상태 읽기**: 각 파일에서 프론트매터 `name`, `enabled` 추출.
3. **토글 대상 선택**: AskUserQuestion(multiSelect). 옵션 라벨 형식 `{rule-name} (currently {enabled|disabled})`, 설명은 규칙 메시지/패턴 요약.
4. **선택 파싱**: 선택된 규칙마다 현재 상태를 반대로 토글.
5. **파일 수정**: Read로 읽고 Edit로 `enabled: true` ↔ `enabled: false` 교체(따옴표 유무 모두 처리).
6. **변경 확인**: Enabled/Disabled/Unchanged로 구분해 보여주고 즉시 반영(재시작 불필요)임을 알림.

엣지 케이스: 규칙 없음 → `/hookify`로 생성 안내 / 아무것도 선택 안 함 → 변경 없음 안내 / 파일 읽기·쓰기 오류 → 구체적 오류 표시 후 수동 편집 대안 제시.

## 📦 출처 / 설치 상태
- 플러그인: hookify@claude-plugins-official
- 활성화: ⛔ **OFF** (settings.json enabledPlugins = false) — 켜야 사용 가능
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/hookify/unknown/commands/configure.md`
