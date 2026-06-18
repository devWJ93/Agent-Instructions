# figma-use-slides

> Slides 컨텍스트에서 `use_figma` MCP 도구를 사용하기 위한 Slides 특화 스킬. `figma-use`와 함께 사용.

## 📌 용도
`use_figma` MCP 도구를 Figma Slides(`figma.com/slides/...`) 컨텍스트에서 쓰기 위한 Slides 특화 컨텍스트를 제공한다. 기반 컨텍스트와 전체 Plugin API는 [figma-use](./figma-use.md)에 있고, 이 스킬은 Slides만의 차이(슬라이드 그리드, 좌표 오프셋, 스피커 노트, 디자인 씽킹 등)를 다룬다.

> `use_figma`를 Slides 작업에 호출할 때 `skillNames: "figma-use-slides"` 전달(로깅용).

## 🎯 트리거 조건
- **언제 호출되는가**: Slides 파일(`figma.com/slides/...`)에서 `use_figma`로 작업할 때 — 슬라이드/섹션 생성·복제·삭제·재정렬, 슬라이드 내 콘텐츠(텍스트/도형/오토레이아웃) 빌드, 테마/팔레트 설정, 스피커 노트 작성, 덱 디자인. `figma-use`와 함께 로드.
- **언제 쓰지 않는가**: Design 파일(→ `figma-use` 단독), FigJam 파일(→ `figma-use-figjam`).

## 💻 사용 예시 / 명령어
**슬라이드 목록 인스펙션**:
```js
const grid = figma.getSlideGrid();
return grid.map((row, rowIdx) =>
  row.map((slide, colIdx) => ({
    id: slide.id, name: slide.name, row: rowIdx, col: colIdx,
    isSkipped: slide.isSkippedSlide, speakerNotes: slide.speakerNotes,
  }))
);
```
**섹션 이름 변경**(SLIDE_ROW에 직접 — 그리드 내부 JS 배열에 `.name` 설정은 no-op):
```js
const slideGrid = figma.currentPage.children.find(c => c.type === "SLIDE_GRID");
slideGrid.children[0].name = "Intro";
```

## 🛠️ 제공 도구 / 주요 파라미터
- **사용 MCP 도구**: `use_figma`(읽기전용 인스펙션 포함), `get_screenshot` / `await node.screenshot()`(시각 컨텍스트). (`get_metadata`는 Slides 미지원 — 읽기전용 스크립트로 검증)
- **Slides 특화 Critical Rules**: ①새 Slides 파일은 기본 light 테마(스캐폴딩 — 자체 디자인으로 덮어쓰기). ②`x`/`y` 설정 전 슬라이드에 `appendChild` 먼저(좌표 오프셋 방지). ③SLIDE_GRID/SLIDE_ROW는 불투명 노드(`.fills`/`.effects`/레이아웃 접근 금지; `SLIDE_ROW.name`만 설정 가능). ④`get_metadata` 미동작. ⑤`figma.createPage()` 금지(Design 전용) — 슬라이드 그리드로 구조 구성.
- **디자인 씽킹 기어**: 콘텐츠/속성 편집(씽킹 스킵, 기존 매칭), 구조적 추가(inherit 모드 — 기존 덱 언어 매칭), 새 덱 생성(풀 디자인 프로세스 — 브리프 읽기, 디자인 언어 확인, 포지션 취하기, 시그니처 부여).
- **스피커 노트**: 요청 시/프레젠터 준비 덱에 작성. 슬라이드 반복 금지, 간결·스캔 가능, 전환·맥락 포함. `slide.speakerNotes`는 마크다운(리스트/볼드/이탤릭/취소선 지원; 헤딩/코드블록/링크 미지원). 무프롬프트 추가 금지.
- **레퍼런스 파일 읽기**: Slides 참조는 `get_screenshot`+읽기전용 스크립트, Design 참조는 `get_design_context`.
- 참조: `slide-gotchas`, `slide-lifecycle`, `slide-grid`, `slide-content`, `slide-properties`, `slide-design`.

## 📦 출처 / 설치 상태
- 플러그인: figma@claude-plugins-official
- 활성화: ON (`disable-model-invocation: false`)
- 관련 MCP: Figma MCP 연동(Slides 컨텍스트). `figma-use` 기반 위에서 동작.
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/figma/2.2.12/skills/figma-use-slides/SKILL.md`
