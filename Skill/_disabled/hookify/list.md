# /hookify:list

> 프로젝트에 설정된 모든 hookify 규칙을 표와 미리보기로 나열하는 커맨드.

## 📌 용도
현재 프로젝트의 `.claude/hookify.*.local.md` 규칙 파일을 모두 찾아, 각 규칙의 name·enabled·event·pattern과 메시지 미리보기를 표 형태로 보여준다. 어떤 규칙이 설정되어 있고 활성 상태인지 한눈에 확인할 때 사용한다. 실행 시 먼저 `hookify:writing-rules` 스킬을 로드한다.

## 🎯 트리거 조건
- **언제 호출되는가**: 설정된 hookify 규칙 전체를 조회하고 싶을 때.
- **언제 쓰지 않는가**: 규칙 생성(→ `/hookify`), 활성/비활성 변경(→ `/hookify:configure`), 사용법 안내(→ `/hookify:help`)일 때.

## 💻 사용 예시 / 명령어
```
/hookify:list
```
- 인자 없음.

## 🛠️ 제공 도구 / 주요 파라미터
allowed-tools: `Glob`, `Read`, `Skill`.

절차:
1. Glob 패턴 `.claude/hookify.*.local.md`로 규칙 파일 검색.
2. 각 파일을 Read하여 프론트매터 필드(name, enabled, event, pattern)와 메시지 미리보기(앞 100자) 추출.
3. 결과를 표로 제시 (Name / Enabled / Event / Pattern / File), 활성/비활성 개수 합계 표시.
4. 규칙별 간단 미리보기(Event, Pattern, Message, Status, File) 출력.
5. 푸터에 수정/비활성/활성/삭제/생성 방법 안내 및 "즉시 반영(재시작 불필요)" 명시.

규칙이 없으면: "No Hookify Rules Configured" 메시지와 함께 `/hookify`로 시작하는 방법, 수동 생성, `/hookify:help` 안내를 표시.

## 📦 출처 / 설치 상태
- 플러그인: hookify@claude-plugins-official
- 활성화: ⛔ **OFF** (settings.json enabledPlugins = false) — 켜야 사용 가능
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/hookify/unknown/commands/list.md`
