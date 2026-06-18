# code-review

> 현재 diff를 **정확성(버그) + 재사용/단순화/효율성** 관점에서 리뷰하는 빌트인 스킬. effort 레벨로 깊이를 조절하고, PR 코멘트나 워킹트리 적용까지 가능.

## 📌 용도
작업 중인 변경(diff)을 대상으로 코드 리뷰를 수행한다. 두 축으로 본다.
1. **Correctness 버그** — 실제로 잘못 동작할 수 있는 결함 탐지
2. **정리(cleanup)** — 재사용(reuse), 단순화(simplification), 효율성(efficiency) 관점의 개선점

effort 레벨로 탐색 범위와 확신도 트레이드오프를 조절한다. 낮을수록 "확신 높은 소수의 발견"만, 높을수록 "불확실한 것까지 포함한 넓은 커버리지"를 제공한다.

## 🎯 트리거 조건
- **언제 호출되는가**
  - "코드 리뷰해줘", "이 diff 봐줘", "버그 없는지 검토해줘"
  - PR 올리기 전/후 변경 검토
  - 변경에 숨은 결함이나 개선점을 찾고 싶을 때
- **언제 쓰지 않는가**
  - **버그를 찾지 않고 품질 정리만** 원하면 `simplify` (code-review와 역할 분리됨)
  - 앱을 실제 실행해 동작을 확인하려면 `verify` / `run`
  - 보안 취약점 전용 리뷰는 `security-review`

## 💻 사용 예시 / 명령어
- `/code-review` — 기본 레벨로 현재 diff 리뷰
- `/code-review high` — 넓은 커버리지(불확실한 발견 포함 가능)
- `/code-review max` / `/code-review ultra` — 최대 깊이 / 클라우드 멀티에이전트 심층 리뷰
- `/code-review high --comment` — 발견 사항을 PR 인라인 코멘트로 게시
- `/code-review high --fix` — 리뷰 후 발견 사항을 워킹트리에 직접 적용

## 🛠️ 제공 도구 / 주요 파라미터
- **effort 레벨**:
  - `low` / `medium` — 적은 수의 고확신 발견
  - `high` → `max` — 넓은 커버리지, 불확실한 발견 포함 가능
  - `ultra` — 클라우드에서 도는 **멀티에이전트 심층 리뷰**
- **플래그**:
  - `--comment` — 발견 사항을 PR **인라인 코멘트**로 게시
  - `--fix` — 리뷰 후 발견 사항을 **워킹트리에 적용**
- **리뷰 관점**: correctness 버그 + reuse / simplification / efficiency

## 📦 출처 / 설치 상태
- 분류: **빌트인(내장) 스킬**
- 경로: 없음 (내장)
- 참고: 이름이 비슷한 `pr-review-toolkit` 플러그인(ON)은 별개다 — 그쪽은 **에이전트 + `review-pr` 커맨드**를 제공하며, 이 `/code-review` 빌트인 스킬과 다르다.
