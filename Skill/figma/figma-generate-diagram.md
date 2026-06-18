# figma-generate-diagram

> Mermaid.js 구문으로 편집 가능한 FigJam 다이어그램을 생성하는 스킬. `generate_diagram` 호출의 필수 선행 스킬.

## 📌 용도
`generate_diagram` 도구는 Mermaid.js 구문을 받아 편집 가능한 FigJam 다이어그램을 만든다. 이 스킬은 타입별 가이드로 라우팅하고 보편 제약을 설정하며, 다른 다이어그램 타입을 써야 하거나 도구를 아예 건너뛰어야 할 때를 알려준다.

> **필수**: 모든 `generate_diagram` 호출 전에 이 스킬을 로드해야 한다. 스킵 시 렌더링 실패·저품질 발생.

**7단계 흐름**: (1) 도구가 적합한지 판단 → (2) 다이어그램 타입 선택 → (3) 보편 제약 적용 → (4) GIGO(맥락 충분히 수집) → (5) Mermaid 한계 초과 여부(하이브리드 워크플로우 판단) → (6) 도구 호출 → (7) 생성 후 처리.

## 🎯 트리거 조건
- **언제 호출되는가**: 다이어그램 생성/그리기 요청 — flowchart, architecture, sequence diagram, ERD, state diagram, gantt/timeline. Mermaid 구문 언급, 시스템 아키텍처·결정 트리·의존성 그래프·API 호출 흐름·인증 핸드셰이크·스키마·파이프라인을 FigJam에 시각화.
- **언제 쓰지 않는가(도구 호출 금지)**: pie chart, mindmap, venn, class diagram, journey, timeline, quadrant, C4, git graph, requirement diagram — 사용자에게 미지원임을 직접 알릴 것. 기존 다이어그램의 폰트 변경/개별 도형 이동/노드별 편집은 Figma에서 직접 편집 권장.

## 💻 사용 예시 / 명령어
**지원 타입**: `flowchart`, `sequenceDiagram`, `stateDiagram`/`stateDiagram-v2`, `gantt`, `erDiagram`.

**타입 라우팅**(첫 매치 사용):
| 사용자 의도 | 타입 | 참조 |
| --- | --- | --- |
| 서비스+데이터스토어+큐+통합 | Architecture flowchart | `references/architecture.md` |
| 결정 트리/프로세스/파이프라인/의존성 | Flowchart | `references/flowchart.md` |
| 시간순 상호작용(API/인증/메시징) | Sequence | `references/sequence.md` |
| 데이터 모델/테이블/키/카디널리티 | ER | `references/erd.md` |
| 명명된 상태+전이 | State | `references/state.md` |
| 일정/마일스톤 | Gantt | `references/gantt.md` |

도구 호출(필수: `name`, `mermaidSyntax`; 선택: `userIntent`, `useArchitectureLayoutCode`(아키텍처 전용), `fileKey`).

## 🛠️ 제공 도구 / 주요 파라미터
- **사용 MCP 도구**: `generate_diagram`, 하이브리드 확장 시 `use_figma`(FigJam 전용 API), `get_figjam`/`get_design_context`(맥락 수집).
- **보편 제약**: 이모지 금지, 라벨 내 `\n` 금지, HTML 태그 금지, 예약어(`end`/`subgraph`/`graph`)를 노드 ID로 금지, 노드 ID camelCase·공백 금지, 특수문자 라벨은 따옴표, sequence의 `Note over/left/right`는 제거됨(하이브리드로), gantt 스타일링은 전처리에서 제거됨.
- **FigJam 전용 API 제약**: 출력이 FigJam(`figma.com/board/...`)이므로 `figma.createPage()` 호출 금지(Design 전용) — FigJam 섹션으로 정리.
- **하이브리드 워크플로우**: 스티키 주석, ERD 노드별 색상, 데이터 첨부 콜아웃 등 Mermaid가 못 하는 것은 `generate_diagram` + `use_figma`(`figma-use-figjam`) 조합. 사전 `references/workflow.md` 로드.
- **파일 재사용**: 반복/관련 다이어그램은 `fileKey` 전달로 기존 파일 재사용(매 호출이 새 드래프트 생성).

## 📦 출처 / 설치 상태
- 플러그인: figma@claude-plugins-official
- 활성화: ON
- 관련 MCP: Figma MCP 연동(`generate_diagram` — 자체 FigJam 파일 생성, 따라서 `create_new_file` 선행 불필요).
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/figma/2.2.12/skills/figma-generate-diagram/SKILL.md`
