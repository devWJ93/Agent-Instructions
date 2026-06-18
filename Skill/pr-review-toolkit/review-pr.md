# review-pr

> 변경된 코드(git diff)를 여러 전문 에이전트로 나눠 종합 PR 리뷰를 수행하고, 심각도별 액션 플랜으로 정리해주는 슬래시 명령어.

## 📌 용도

`/review-pr`은 풀 리퀘스트(또는 커밋 전 변경분)를 **각기 다른 품질 측면에 특화된 전문 에이전트**로 동시 또는 순차 분석하는 종합 코드 리뷰 명령어다. 변경 파일을 감지해 어떤 리뷰가 적용 가능한지 판단하고, 해당 에이전트를 띄운 뒤 결과를 모아 다음 형식으로 정리한다.

- Critical Issues (머지 전 반드시 수정)
- Important Issues (수정 권장)
- Suggestions (있으면 좋음)
- Strengths (잘된 점)
- Recommended Action (수정 우선순위 및 재리뷰 권고)

각 발견 사항은 `[agent-name]: 설명 [file:line]` 형태로 **구체적 파일·라인 참조**와 함께 제시되어 즉시 조치 가능하다.

## 🎯 트리거 조건

- **언제 호출되는가**
  - 사용자가 `/review-pr` 또는 `/pr-review-toolkit:review-pr`을 직접 입력할 때
  - 커밋 전 또는 PR 생성 전에 변경 코드 품질을 점검하고 싶을 때
  - 특정 측면(테스트, 에러 처리, 주석, 타입 등)만 집중 리뷰하고 싶을 때
  - PR 피드백을 받은 뒤 수정 사항을 다시 검증하고 싶을 때

- **언제 쓰지 않는가**
  - 변경된 파일이 전혀 없을 때 (git diff가 비어 있으면 분석 대상 없음)
  - 단순 단일 파일 한 줄 수정처럼 종합 리뷰가 과한 경우
  - 코드를 *작성/수정*하는 작업 자체 (이 명령은 리뷰 전용, `code-simplifier`만 polish 수행)

## 💻 사용 예시 / 명령어

전체 리뷰 (기본값 — 적용 가능한 모든 리뷰 실행):
```
/pr-review-toolkit:review-pr
```

특정 측면만:
```
/pr-review-toolkit:review-pr tests errors
# 테스트 커버리지 + 에러 처리만 리뷰

/pr-review-toolkit:review-pr comments
# 코드 주석만 리뷰

/pr-review-toolkit:review-pr simplify
# 리뷰 통과 후 코드 단순화
```

병렬 리뷰:
```
/pr-review-toolkit:review-pr all parallel
# 모든 에이전트를 동시에 실행 (빠르지만 결과가 한꺼번에 도착)
```

**워크플로 통합 예시:**
```
커밋 전:   /pr-review-toolkit:review-pr code errors → critical 수정 → commit
PR 생성 전: 변경 stage → /pr-review-toolkit:review-pr all → 이슈 해결 → 재리뷰 → PR 생성
피드백 후:  수정 → 관련 측면 타겟 리뷰 → 해결 확인 → push
```

**인자(`argument-hint: [review-aspects]`):** `comments`, `tests`, `errors`, `types`, `code`, `simplify`, `all`(기본), `parallel`.

## 🛠️ 제공 도구 / 주요 파라미터

**허용 도구(allowed-tools):** `Bash`, `Glob`, `Grep`, `Read`, `Task`

**리뷰 워크플로:**
1. 범위 결정 — `git status`로 변경 파일 식별, 인자 파싱
2. 변경 파일 식별 — `git diff --name-only`, `gh pr view`로 기존 PR 확인
3. 적용 리뷰 판단 — 파일 유형에 따라 어떤 에이전트가 필요한지 결정
4. 리뷰 에이전트 실행 (순차 또는 병렬)
5. 결과 집계 후 심각도별 요약 + 액션 플랜 제시

**조건별 적용 에이전트:**
- 항상 적용 → **code-reviewer** (일반 품질, CLAUDE.md 준수, 버그 탐지)
- 테스트 파일 변경 시 → **pr-test-analyzer** (행위 기반 테스트 커버리지, 중요 누락 식별)
- 주석/문서 추가 시 → **comment-analyzer** (주석 정확성, comment rot, 문서 완성도)
- 에러 처리 변경 시 → **silent-failure-hunter** (조용한 실패, catch 블록, 에러 로깅 점검)
- 타입 추가/수정 시 → **type-design-analyzer** (타입 캡슐화, 불변식 표현, 타입 설계 품질 평가)
- 리뷰 통과 후 → **code-simplifier** (복잡한 코드 단순화, 가독성 향상, 기능 보존)

**실행 방식 옵션:**
- 순차(Sequential): 한 번에 하나씩, 리포트가 완결되어 이해·조치 쉬움 — 대화형 리뷰에 적합
- 병렬(Parallel, 사용자 요청 시): 모든 에이전트 동시 실행, 빠르지만 결과가 한꺼번에 도착

**팁:** PR 생성 *전*에 일찍 실행 / 에이전트는 기본적으로 git diff를 분석 / critical부터 수정 / 수정 후 재실행으로 해결 검증 / 관심사가 명확하면 특정 측면만 타겟. 모든 에이전트는 `/agents` 목록에서 확인 가능.

## 📦 출처 / 설치 상태

- 플러그인: pr-review-toolkit@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/pr-review-toolkit/unknown/commands/review-pr.md`
