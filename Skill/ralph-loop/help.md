# help

> Ralph Loop 플러그인의 개념·명령어·핵심 메커니즘을 사용자에게 설명하는 도움말 명령어.

## 📌 용도

`/help`(ralph-loop 플러그인 제공)은 Ralph Loop가 무엇이고 어떻게 동작하는지, 어떤 명령어가 있는지를 **사용자에게 설명**하는 문서형 명령어다. 별도 작업을 수행하지 않고, 다음 내용을 안내한다.

- **Ralph Loop란**: Geoffrey Huntley가 정립한 Ralph Wiggum 기법 — 같은 프롬프트를 반복 주입하는 연속 AI 루프 방법론. "self-referential"은 출력을 입력으로 먹는 게 아니라 **파일·git 히스토리에 남은 이전 작업을 다음 반복이 보는 것**.
- **각 반복 흐름**: 동일 프롬프트 수신 → 작업·파일 수정 → 종료 시도 → Stop 훅이 가로채 같은 프롬프트 재주입 → 이전 작업을 보며 점진 개선.
- **사용 가능한 명령어**: `/ralph-loop`, `/cancel-ralph`.
- **핵심 개념**: 완료 프로미스(`<promise>` 태그), 자기참조 메커니즘.
- **언제 쓰고 안 쓰는지**, 그리고 더 알아볼 링크.

## 🎯 트리거 조건

- **언제 호출되는가**
  - 사용자가 `/help`(ralph-loop 컨텍스트)를 입력할 때
  - Ralph Loop 사용법·개념을 처음 익히거나 명령어 레퍼런스가 필요할 때
  - `/ralph-loop` 옵션과 완료 프로미스 동작을 확인하고 싶을 때

- **언제 쓰지 않는가**
  - 실제로 루프를 시작/취소하려는 경우 (각각 `/ralph-loop`, `/cancel-ralph` 사용)
  - Claude Code 전반의 도움말이 필요한 경우 (이 명령은 ralph-loop 플러그인 전용 설명)

## 💻 사용 예시 / 명령어

```
/help
```

인자 없음. 실행하면 Ralph Loop 설명, 명령어 목록, 완료 프로미스/자기참조 개념, 사용 적합/부적합 사례, 예시를 출력한다.

설명에 포함되는 예시:
```
# 시작
/ralph-loop "Refactor the cache layer" --max-iterations 20
/ralph-loop "Add tests" --completion-promise "TESTS COMPLETE"

# 취소
/cancel-ralph

# 완료 신호 (Claude가 출력해야 하는 태그)
<promise>TASK COMPLETE</promise>

# 인터랙티브 버그 픽스 예시
/ralph-loop "Fix the token refresh logic in auth.ts. Output <promise>FIXED</promise> when all tests pass." --completion-promise "FIXED" --max-iterations 10
```

## 🛠️ 제공 도구 / 주요 파라미터

별도 도구·인자 없음 (`description`만 가진 순수 설명형 명령). 명령은 다음을 설명하도록 지시한다.

**`/ralph-loop <PROMPT> [OPTIONS]`** — 현재 세션에서 루프 시작.
- `--max-iterations <n>`: 자동 중단 전 최대 반복.
- `--completion-promise <text>`: 완료를 알리는 문구.
- 동작: 상태 파일 생성 → 작업 → 종료 시 Stop 훅이 가로챔 → 같은 프롬프트 재주입 → 프로미스 감지 또는 최대 반복까지 반복.

**`/cancel-ralph`** — 활성 루프 취소(상태 파일 제거, 반복 횟수와 함께 보고).

**완료 프로미스(Completion Promises):** 완료를 알리려면 Claude가 `<promise>TASK COMPLETE</promise>` 태그를 출력해야 하며, Stop 훅이 이 태그를 찾는다. 태그(또는 `--max-iterations`)가 없으면 무한 실행.

**자기참조 메커니즘:** 같은 프롬프트 반복 + 작업이 파일에 영속 + 각 반복이 이전 시도를 봄 + 목표를 향해 점진 누적.

**사용 적합/부적합:**
- 적합: 성공 기준이 명확한 작업, 반복·정제가 필요한 작업, 자기 교정형 개발, 그린필드 프로젝트.
- 부적합: 인간 판단·설계 결정 필요, 일회성 작업, 성공 기준 불명확, 프로덕션 디버깅.

**더 알아보기:** 원 기법 `https://ghuntley.com/ralph/`, Ralph Orchestrator `https://github.com/mikeyobrien/ralph-orchestrator`.

## 📦 출처 / 설치 상태

- 플러그인: ralph-loop@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/ralph-loop/1.0.0/commands/help.md`
