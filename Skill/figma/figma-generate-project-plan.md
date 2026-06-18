# figma-generate-project-plan

> PRD(+선택적 코드베이스 컨텍스트)를 **FigJam 프로젝트 플랜 보드**로 변환하는 인터랙티브 워크플로 스킬. 섹션 후보 제안 → 사용자 선택 → 섹션별 콘텐츠·블록 확정 → FigJam 생성까지 단계마다 확인을 거친다.

## 📌 용도
PRD를 받아 FigJam에 프로젝트 플랜 보드를 그린다. 섹션 집합은 고정이 아니라 리서치에서 **후보를 제안하고 사용자가 포함할 섹션을 고른다.** 선택된 각 섹션마다 콘텐츠 + 렌더링 블록(표/멀티컬럼/스티키 컬럼/다이어그램 등)을 제안하고 사용자가 확인한다. 전체 **9단계**로 진행한다:
1. 컨텍스트 수집(PRD·코드베이스 grounding) → 2. 섹션 후보 제안 → 3. 섹션별 심층 리서치(갭 도출) → 4. 콘텐츠 + 블록 제안 → 5. FigJam 파일 생성 → 6. 스켈레톤 → 7. 섹션별 채우기 → 8. 다이어그램 → 9. 최종 검토·리포트

각 단계는 Research(셀프체크) / Confirm(AskUserQuestion) / Write(스크린샷 + AskUserQuestion) 세 유형 중 하나이며, **확인 안 된 체크포인트를 지나쳐 쓰지 않는다.**

## 🎯 트리거 조건
- **언제 호출되는가**: "/generate-project-plan", "project plan in FigJam", "interactive project plan", "PRD to FigJam", PRD를 주며 섹션별 확인을 원할 때. `disable-model-invocation: false`라 모델이 자동 호출도 가능.
- **언제 쓰지 않는가**: 단순 다이어그램만(→ `figma-generate-diagram`), 디자인 시안 생성(→ `figma-generate-design`), 일반 FigJam 편집(→ `figma-use-figjam`).

## 💻 사용 예시 / 명령어
- `/generate-project-plan` + PRD 경로 또는 붙여넣기 → (선택) 코드베이스 진입점 지정
- 흐름: 컨텍스트 수집 → 섹션 후보 멀티셀렉트(≤4개씩 배치 질문) → 섹션별 갭 질문 → 콘텐츠·블록 확인 → 파일 생성(probe) → 스켈레톤(스크린샷 확인) → 섹션별 채우기(섹션마다 확인) → 다이어그램(생성·reparent) → 전체 스크린샷 최종 검토
- 0개 섹션 선택 시: 깔끔히 중단, 파일 생성 안 함.

## 🛠️ 제공 도구 / 주요 파라미터
- **MCP 도구**: `create_new_file`, `use_figma`, `generate_diagram`, `whoami`(planKey 확인)
- **선행 필수 스킬**: `figma-use`(세션당 1회 로드), `figma-use-figjam`(매 `use_figma` 호출 전 재로드), `figma-generate-diagram`(매 `generate_diagram` 전 재로드)
- **블록 서브스킬**(콘텐츠 타입별 reference): section / nested-section / intro-callout / text-primitives / table / multi-column-text / sticky-column / diagram-section / metadata-strip
- **STRICT 규칙**: `section.name = ""`(제목은 내부 H2 텍스트), 섹션 배경은 `ARCH_PALE` 팔레트(FigJam 표준 SECTION 팔레트 금지 — 다이어그램과 색 충돌), 표 셀은 `Inter Bold` 16px, 자식 위치는 `prevChild.y + height + 24`(고정 오프셋 금지), `use_figma` 1회당 ≤10 논리 연산, 모든 write 스크립트는 `createdNodeIds`/`mutatedNodeIds` 반환
- **다이어그램 주의**: reparent 후 커넥터는 **삭제·재생성**(stale elbow 방지), 커넥터 라벨은 `fontName`+`fontSize`+`characters`+`fills` 4개 모두 설정(기본 `fills=[]`라 투명)

## 📦 출처 / 설치 상태
- 플러그인: figma@claude-plugins-official
- 활성화: ON
- 관련 MCP: figma MCP (`create_new_file`·`use_figma`·`generate_diagram`·`whoami`)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/figma/2.2.12/workflow-skills/generate-project-plan/SKILL.md` (※ 다른 figma 스킬과 달리 `skills/`가 아닌 `workflow-skills/` 하위)
