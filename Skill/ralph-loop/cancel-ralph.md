# cancel-ralph

> 활성화된 Ralph 루프의 상태 파일을 제거해 루프를 취소하는 명령어.

## 📌 용도

`/cancel-ralph`은 진행 중인 Ralph 루프를 **중단**한다. Ralph 루프는 `.claude/ralph-loop.local.md` 상태 파일이 존재하는 한 Stop 훅이 세션 종료를 계속 가로채므로, 이 명령은 그 **상태 파일을 삭제**해 루프를 끝낸다. 삭제 직전에 현재 반복 번호를 읽어 "몇 번째 반복에서 취소되었는지"를 보고한다.

Ralph 루프는 기본적으로 수동으로 멈출 수 없도록(무한 실행) 설계되어 있어서, 이 명령이 사실상 유일한 정상 중단 수단이다.

## 🎯 트리거 조건

- **언제 호출되는가**
  - 사용자가 `/cancel-ralph`을 입력할 때
  - 활성 Ralph 루프를 더 이상 돌리고 싶지 않을 때
  - 무한 루프(완료 프로미스/최대 반복 미설정)에 빠진 루프를 멈출 때
  - 작업이 충분히 진행되어 루프를 수동 종료하고 싶을 때

- **언제 쓰지 않는가**
  - 활성 Ralph 루프가 없을 때 (실행하면 "No active Ralph loop found." 안내만 나옴)
  - 루프를 멈추지 않고 일시 정지만 원할 때 (해당 기능 없음 — 취소 후 다시 `/ralph-loop` 시작해야 함)

## 💻 사용 예시 / 명령어

```
/cancel-ralph
```

인자 없음. 실행 결과 두 가지:
- 상태 파일 없음 → `"No active Ralph loop found."`
- 상태 파일 있음 → 제거 후 `"Cancelled Ralph loop (was at iteration N)"` (N은 상태 파일의 `iteration:` 값)

## 🛠️ 제공 도구 / 주요 파라미터

**허용 도구(allowed-tools):**
- `Bash(test -f .claude/ralph-loop.local.md:*)` — 상태 파일 존재 확인
- `Bash(rm .claude/ralph-loop.local.md)` — 상태 파일 제거
- `Read(.claude/ralph-loop.local.md)` — 반복 번호 읽기

`hide-from-slash-command-tool: true`로 설정됨.

**절차:**
1. `test -f .claude/ralph-loop.local.md && echo "EXISTS" || echo "NOT_FOUND"`로 상태 파일 존재 확인
2. `NOT_FOUND`이면 → "No active Ralph loop found." 출력
3. `EXISTS`이면 →
   - `.claude/ralph-loop.local.md`를 Read해 프론트매터의 `iteration:` 값(N) 획득
   - `rm .claude/ralph-loop.local.md`로 파일 삭제
   - "Cancelled Ralph loop (was at iteration N)" 보고

**연관 명령:** `/ralph-loop`(루프 시작), `/help`(ralph-loop 플러그인 설명).

## 📦 출처 / 설치 상태

- 플러그인: ralph-loop@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/ralph-loop/1.0.0/commands/cancel-ralph.md`
