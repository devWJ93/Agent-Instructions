# 02. 도구 경계 (Tool Boundaries)

에이전트가 어떤 도구를 **언제·어디까지** 쓸 수 있는지 사전에 정한다. 경계가 분명해야 자율 실행이 안전해진다.

## 1. 정보 조회: RAG 우선

- 코드·프로젝트 관련 질문은 `rag-search` MCP의 `rag_agent` 도구를 **최우선**으로 쓴다(서버 등록 전제). 질문 유형을 자동 분류해 최적 전략을 고른다.
  - 관계/의존성 → 그래프 우선, 과거 이력 → 세션 우선, 코드 상세 → 코드 우선, 문서/규칙 → 문서 우선.
- 내장 도구(Grep, Read)는 RAG 결과가 부족하거나 **코드를 직접 수정할 때**만 쓴다.
- 단, 코드를 고치기 직전에는 대상 파일을 Read로 **완전히** 읽는다(부분 읽기 금지).

## 2. 탐색·조사: 서브에이전트 위임

- 파일 탐색·조사 작업은 Task(Explore)에 위임해 메인 컨텍스트를 아낀다.
- 서로 의존 없는 독립 작업은 병렬 서브에이전트로 동시에 돌린다.
- 서브에이전트 결과는 **요약만** 메인으로 가져온다(원문 덤프 금지).

## 3. 팀 작업

- TeamCreate 후 반드시 모니터 창을 띄운다.
  1. `team-monitor.js` 위치를 찾는다(`which team-monitor.js` 또는 `claude-team-monitor` 디렉토리 검색).
  2. 없으면 현재 작업 디렉토리에 클론한다: `git clone https://github.com/glowElephant/claude-team-monitor.git`
  3. 실행한다: `start bash -c "node <찾은경로>/team-monitor.js {team-name}"`
- TeamDelete 후 모니터는 자동 종료된다(별도 조치 불필요).

## 4. 스킬 선택 (도구 *선택* — 경계와 별개, 포인터)

어떤 스킬을 **언제** 쓸지는 도구 경계(권한)가 아니라 도구 *선택*이다. 상황→스킬 매핑과 전체 인덱스는 한 곳에서 관리한다:
- **상황→스킬 매핑**: `README §6`(의사결정)·`README §7`(스킬 인덱스).
- **각 스킬의 트리거·예시·파라미터**: [`Skill/README.md`](../Skill/README.md) → `Skill/<그룹>/<스킬>.md`.
- **규칙**: 스킬이 적용되면 **먼저 호출**하고 "Using [skill]"를 알린다. (비노출 ON 플러그인·OFF 플러그인 구분은 `Skill/README.md` 참조)

## 5. MCP 선택 (도구 *선택*, 포인터)

어떤 MCP를 **언제** 쓸지 → `README §8`(MCP 인덱스) → `MCP/<server>.md`, 전체 도구 표는 [`MCP/README.md`](../MCP/README.md).
- 주의: `claude.ai PlayMCP`는 계정 종속(타 AI 사용 불가). **등록 권한·금지사항은 아래 §6**.

## 6. MCP 서버 등록 권한

- MCP 등록은 `claude mcp add` 명령어만 사용한다. settings.json·.mcp.json에 직접 쓰면 인식되지 않는다.
- 항상 `--scope user`로 등록한다. project scope로 하면 다른 프로젝트에서 동작하지 않는다.
- 경로는 실제 저장소 위치로 지정한다(하드코딩한 임의 경로 금지).
- 등록 전 `claude mcp list`로 중복을 확인하고, 이미 있으면 스킵한다.
- ⚠️ 이 §6 전체는 **Claude Code 한정**이다. 비-Claude AI는 반대로 `mcp.json`/SDK **직접 편집이 정상**이다(→ `PORTABILITY.md` §2.2, README §5.5).

## 7. 외부로 나가는 작업의 경계

- 외부 서비스로 콘텐츠를 보내는 행위(게시·전송)는 캐시·색인될 수 있으니, 되돌리기 어려운 작업은 먼저 확인받는다.
- Notion 페이지 수정은 **부분 블록 수정 금지**. 전체 블록을 삭제하고 처음부터 재생성한다(부분 업데이트는 API 에러 반복).

## 8. Windows 환경 도구 주의

- Bash(mintty)에서 `$VAR` 변수 확장이 안 된다 → 경로를 직접 지정한다.
- 따옴표 이스케이프 문제가 잦다 → Glob·Grep·Read 도구를 우선 사용한다.
- PowerShell 도구는 `-NonInteractive`라 `$PROFILE`을 읽지 않는다. 환경 의존 명령은 경로/환경변수를 명시한다.

## 9. Unity 개발 (해당 프로젝트 한정)

- `/compile-check`는 **사용자가 요청할 때만** 실행한다 — 자동 실행 금지.
