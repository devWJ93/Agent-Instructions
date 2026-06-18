# ralph-loop

> 같은 프롬프트를 Stop 훅으로 반복 주입해 현재 세션 안에서 자기참조 반복 개발 루프(Ralph Wiggum 기법)를 시작하는 명령어.

## 📌 용도

`/ralph-loop`은 Geoffrey Huntley가 정립한 **Ralph Wiggum 기법**을 Claude Code 세션 내부에서 구현한다. 외부 `while true` 배시 루프 없이, **Stop 훅이 세션 종료를 가로채** 같은 프롬프트를 다시 주입하는 방식으로 동작한다.

```bash
# 개념 (외부 루프 형태):
while :; do
  cat PROMPT.md | claude-code --continue
done
```

각 반복(iteration)에서:
1. Claude가 **동일한 프롬프트**를 받음
2. 작업하며 파일을 수정
3. 종료 시도
4. Stop 훅이 종료를 차단하고 같은 프롬프트를 다시 주입
5. Claude가 **파일과 git 히스토리에 남은 자신의 이전 작업**을 봄
6. 완료(또는 최대 반복)까지 점진적으로 개선

"자기참조"란 Claude가 자기 출력을 입력으로 먹는 게 아니라, **이전 작업이 파일에 영속되어 다음 반복이 그것을 보는 것**을 의미한다. "deterministically bad in an undeterministic world" — 실패가 예측 가능하므로 프롬프트 튜닝으로 체계적 개선이 가능하다는 철학.

## 🎯 트리거 조건

- **언제 호출되는가**
  - 사용자가 `/ralph-loop "<프롬프트>" [옵션]`을 입력할 때
  - 성공 기준이 명확하고 **자동 검증(테스트, 린터)** 가능한 작업
  - 반복·정제가 필요한 작업 (예: 테스트를 통과시키기, TDD)
  - 그린필드 프로젝트, 자리를 비워도 되는 작업

- **언제 쓰지 않는가**
  - 인간의 판단·설계 결정이 필요한 작업
  - 일회성(one-shot) 작업
  - 성공 기준이 불명확한 작업
  - 프로덕션 디버깅 (타겟 디버깅을 쓸 것)

  ⚠️ 이 루프는 기본적으로 **수동으로 멈출 수 없고 무한 실행**된다. 반드시 `--max-iterations`나 `--completion-promise`로 종료 조건을 걸 것.

## 💻 사용 예시 / 명령어

기본 형태 (`argument-hint: PROMPT [--max-iterations N] [--completion-promise TEXT]`):
```
/ralph-loop "<프롬프트>" --max-iterations <n> --completion-promise "<텍스트>"
```

예시:
```
/ralph-loop "Refactor the cache layer" --max-iterations 20
/ralph-loop "Add tests" --completion-promise "TESTS COMPLETE"
/ralph-loop Build a todo API --completion-promise 'DONE' --max-iterations 20
/ralph-loop --max-iterations 10 Fix the auth bug
/ralph-loop Refactor cache layer        # 종료 조건 없음 → 무한 실행
```

완전한 예시 (완료 프로미스 + 안전 한계 동시 사용 권장):
```
/ralph-loop "Build a REST API for todos. Requirements: CRUD, input validation, tests. Output <promise>COMPLETE</promise> when done." --completion-promise "COMPLETE" --max-iterations 50
```

**완료 신호(Completion Promise):** 루프를 끝내려면 Claude가 정확히 다음 태그를 출력해야 한다.
```
<promise>TASK COMPLETE</promise>
```
Stop 훅이 이 태그 안의 텍스트를 `--completion-promise` 값과 **정확한 문자열로 비교**한다. 글로빙(`*`, `?`) 아님. 따라서 "SUCCESS" vs "BLOCKED" 같은 다중 종료 조건엔 쓸 수 없고, `--max-iterations`가 주 안전장치다.

**상태 모니터링:**
```bash
grep '^iteration:' .claude/ralph-loop.local.md   # 현재 반복 횟수
head -10 .claude/ralph-loop.local.md             # 전체 상태
```

## 🛠️ 제공 도구 / 주요 파라미터

**허용 도구(allowed-tools):** `Bash(${CLAUDE_PLUGIN_ROOT}/scripts/setup-ralph-loop.sh:*)` — 명령은 셋업 스크립트만 실행한다. `hide-from-slash-command-tool: true`로 설정됨.

**옵션:**
- `--max-iterations <n>` — N회 후 자동 중단 (기본: 0 = 무제한). 양의 정수 또는 0만 허용 (소수·음수·텍스트 거부).
- `--completion-promise <text>` — 완료를 알리는 문구 (다중 단어는 따옴표 필수).
- `-h`, `--help` — 도움말.

**동작 메커니즘:**
1. `scripts/setup-ralph-loop.sh`가 인자를 파싱해 **상태 파일** `.claude/ralph-loop.local.md` 생성. 이 파일은 YAML 프론트매터(`active`, `iteration`, `session_id`, `max_iterations`, `completion_promise`, `started_at`) + 본문(프롬프트)로 구성.
2. 사용자가 작업하다 종료를 시도하면 `hooks/stop-hook.sh`(Stop 훅)가 가로챔.
3. 훅은 상태 파일을 읽어:
   - **세션 격리**: `session_id`가 현재 세션과 다르면 차단하지 않음 (프로젝트 단위 파일이 다른 세션 종료를 막지 않도록).
   - 최대 반복 도달 시 → 상태 파일 삭제 후 종료 허용.
   - 트랜스크립트(JSONL)에서 마지막 assistant 텍스트 블록을 추출해 `<promise>` 태그를 찾고, 완료 프로미스와 정확히 일치하면 → 상태 파일 삭제 후 종료 허용.
   - 아니면 `iteration`을 +1 갱신하고 `{"decision":"block","reason":"<프롬프트>","systemMessage":"🔄 Ralph iteration N..."}` JSON을 출력해 **같은 프롬프트를 재주입**.
4. 상태 파일 손상(숫자 필드 비정상, 프롬프트 누락, 트랜스크립트 없음) 시 안전하게 루프 중단.

**핵심 규칙(CRITICAL):** 완료 프로미스가 설정되면, 그 문장이 **완전하고 명백히 참일 때만** 출력해야 한다. 막혔다고 느껴도, 루프를 탈출하려고 거짓 프로미스를 내면 안 된다. 루프는 진짜 완료까지 계속되도록 설계됨.

**Windows 주의:** Stop 훅은 배시 스크립트라 Git for Windows가 필요. `bash`가 WSL bash로 잘못 잡히면 훅이 실패할 수 있으므로, 캐시된 `hooks/hooks.json`에서 Git Bash 경로(`"C:/Program Files/Git/bin/bash.exe"`)를 명시하는 워크어라운드가 있다. (`Git/bin/bash.exe` 사용, `Git/usr/bin/bash.exe` 아님.)

## 📦 출처 / 설치 상태

- 플러그인: ralph-loop@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/ralph-loop/1.0.0/commands/ralph-loop.md`
