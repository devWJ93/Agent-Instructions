# qodo-get-rules

> 현재 코딩 작업에 가장 관련 있는 Qodo 코딩 규칙을, 시맨틱 검색 쿼리를 생성해 `POST /rules/search`로 가져와 심각도별로 적용하는 스킬.

## 📌 용도

`qodo-get-rules`는 코딩 작업을 시작할 때 그 작업과 **가장 관련 있는 Qodo 코딩 규칙만** 골라 불러오는 "shift-left" 스킬이다. 코딩 과제로부터 집중된 시맨틱 검색 쿼리를 생성하고, Qodo 플랫폼의 `POST /rules/search` 엔드포인트를 호출해 관련도 순으로 규칙을 받아온다. 받은 규칙은 심각도(ERROR/WARNING/RECOMMENDATION)에 따라 코드 생성에 강제 적용된다.

전체 규칙을 무차별로 끌어오지 않고, **토픽 쿼리 + 교차 관심사 쿼리**의 듀얼 쿼리 전략으로 핵심 규칙과 공통 표준 규칙을 모두 포착하는 것이 특징이다. 이미 규칙이 로드되어 있으면(컨텍스트에 "Qodo Rules Loaded"가 있으면) **스킵**한다.

## 🎯 트리거 조건

- **언제 호출되는가**
  - Qodo가 설정된 상태에서 사용자가 코드를 작성·편집·리팩터·리뷰하려 할 때
  - 구현 계획을 세우기 시작할 때 (코드 생성 직전)
  - 트리거 정규식 매칭: `get/load/fetch (qodo) rules`, `search rules`, `coding/code rules`, `before cod`, `start coding`, `write code`, `implement`, `create.*code`, `build/add.*feature`, `fix.*bug`, `refactor`, `modify/update.*code` 등

- **언제 쓰지 않는가**
  - 이미 규칙이 로드됨("Qodo Rules Loaded"가 컨텍스트에 존재)
  - git 저장소 밖에서 작업 중일 때 (git 저장소 필수 — 없으면 안내 후 우아하게 종료)
  - Qodo API 키 미설정 (안내 후 종료)
  - 코딩과 무관한 작업

## 💻 사용 예시 / 명령어

Claude Code에서:
```
/qodo-get-rules
```

또는 트리거 문구로 자연스럽게 호출 ("get qodo rules", "load rules", "이 작업 전에 규칙 가져와" 등). 코드 생성·편집·리팩터를 요청하면 자동으로 선행 실행되도록 설계됨.

**동작 흐름 요약:** git 저장소·스코프 감지 → Qodo 설정 확인 → 구조화된 두 쿼리 생성 → `POST /rules/search` 두 번 호출(병렬 가능) → 결과 병합·중복 제거 → "📋 Qodo Rules Loaded" 출력 → 심각도별 적용 → 보고.

## 🛠️ 제공 도구 / 주요 파라미터

**허용 도구(allowed-tools):** `Bash` (git 명령, JSON 파싱, curl API 호출, UUID 생성)

**워크플로 단계:**
1. **규칙 로드 여부 확인** — "Qodo Rules Loaded"가 있으면 Step 6으로 건너뜀.
2. **git 저장소 확인 + 스코프 감지** — `git rev-parse --is-inside-work-tree`, `git remote get-url origin`으로 원격 URL을 파싱. SSH(`git@host:org/repo`)·HTTPS(`https://host/org/repo`) 형식 처리, `.git` 접미사 제거. cwd가 `modules/<name>/` 안이면 모듈 스코프(`/org/repo/modules/name/`), 아니면 저장소 스코프(`/org/repo/`). 스코프를 못 구하면 생략하고 조직 전체 검색으로 우아하게 강등.
3. **Qodo 설정 확인** — 기본 위치 `~/.qodo/config.json`. `API_KEY`(환경변수 `QODO_API_KEY` 우선), `ENVIRONMENT_NAME`(환경변수 `QODO_ENVIRONMENT_NAME` 우선, 없으면 production), 선택적 `QODO_API_URL`(있으면 `{URL}/rules/v1`을 base로 사용). 호출용 `request-id` UUID 생성.
   - URL 우선순위: `QODO_API_URL` → `ENVIRONMENT_NAME` → production 기본값(`https://qodo-platform.qodo.ai/rules/v1`).
4. **구조화된 검색 쿼리 2개 생성** — 규칙 임베딩 포맷을 모방한 3줄 구조:
   ```
   Name: {5~10단어 규칙 제목}
   Category: {Security|Correctness|Quality|Reliability|Performance|Testability|Compliance|Accessibility|Observability|Architecture 중 하나}
   Content: {검사·강제할 내용 1~2문장}
   ```
   - **쿼리 1(토픽)**: 과제의 주된 관심사에 초점. 기술 스택을 알면 Content에 명시.
   - **쿼리 2(교차 관심사)**: 대부분의 변경에 적용되는 반복적 품질·표준 패턴(모듈 구조, 타입 어노테이션, 구조화 로깅, 리포지토리 패턴 등). 기본 Category는 Security/Compliance/Observability/Architecture.
   - 키워드 나열·평문 문장 금지(임베딩 성능 저하).
5. **`POST /rules/search` 호출** — 쿼리당 1회씩, 기본 `TOP_K=20`. 병렬 가능. 결과를 규칙 ID로 중복 제거하고 토픽 쿼리 결과 우선. 스코프 감지 시에만 `scopes` 포함(빈 값이면 필드 자체 생략, `null`/`[]` 보내지 않음).
6. **규칙 포맷·출력** — "📋 Qodo Rules Loaded" 헤더 + 관련도 순 목록(규칙별 심각도 라벨).
7. **심각도별 적용**:

   | 심각도 | 강제 수준 | 건너뛸 때 |
   |---|---|---|
   | **ERROR** | 반드시 준수(협상 불가). 준수 주석 추가(예: `# Following Qodo rule: No Hardcoded Credentials`) | 사용자에게 설명하고 지침 요청 |
   | **WARNING** | 기본적으로 준수 | 응답에서 이유 간단 설명 |
   | **RECOMMENDATION** | 적절할 때 고려 | 조치 불필요 |
8. **보고** — 적용된 규칙·심각도, 스킵한 WARNING 이유, 적용 규칙 없음("No Qodo rules were applicable...") 안내, 설계에 영향을 준 RECOMMENDATION만 언급.

**참조 문서:** `references/repository-scope.md`(URL 포맷·강등), `references/query-generation.md`(쿼리 전략·카테고리 선택), `references/search-endpoint.md`(요청/응답 계약·TOP_K 튜닝·에러 처리), `references/output-format.md`(출력 포맷).

**흔한 실수:** 이미 로드됐는데 재실행 / 잘못된 쿼리 포맷(키워드 나열) / 토픽 쿼리만 사용(교차 관심사 누락) / 모호한 쿼리 / 빈 결과에 크래시 / git 저장소 아님 / API 키 없음 / ERROR 규칙 준수 주석 누락.

**전제 도구:** Git, curl, Python 3.6+ (JSON 파싱·경로 처리 용도, API 호출은 curl).

## 📦 출처 / 설치 상태

- 플러그인: qodo-skills@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/qodo-skills/0.6.1/skills/qodo-get-rules/SKILL.md`
