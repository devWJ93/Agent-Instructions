# figma-use

> Figma Plugin API로 JavaScript를 실행해 노드를 생성/편집/조회하는 기반 스킬. 모든 `use_figma` 호출의 필수 선행 스킬.

## 📌 용도
`use_figma` 도구로 Figma 파일 컨텍스트에서 JavaScript를 실행(Plugin API)한다. 노드 생성/편집/삭제, 변수·토큰 설정, 컴포넌트·배리언트 빌드, 오토레이아웃·fill 수정, 변수 바인딩, 파일 구조 프로그래밍적 인스펙트 등. 상세 레퍼런스는 모두 `references/`에 있다.

> **필수**: 모든 `use_figma` 호출 전에 이 스킬을 로드해야 한다. 스킵 시 디버깅 어려운 흔한 실패 발생. `use_figma` 호출 시 `skillNames: "figma-use"` 전달(로깅용).

- 전체 페이지/화면/다중 섹션 레이아웃을 코드에서 빌드/업데이트 → `figma-generate-design`도 로드.
- 컴포넌트(단일 포함) 생성/빌드 → `figma-generate-library`도 로드.

## 🎯 트리거 조건
- **언제 호출되는가**: 쓰기 액션 또는 JS 실행이 필요한 고유 읽기 액션 — 노드 생성/편집/삭제, 변수/토큰 설정, 컴포넌트·배리언트 빌드, 오토레이아웃/fill 수정, 변수 바인딩, 파일 구조 인스펙트.
- **언제 쓰지 않는가**: FigJam 전용 작업(→ `figma-use-figjam` 추가), Slides 전용 작업(→ `figma-use-slides` 추가). 단, 둘 다 이 스킬을 기반으로 함.

## 💻 사용 예시 / 명령어
```js
// 페이지 전환(동기 setter는 throw — 반드시 async)
const targetPage = figma.root.children.find((p) => p.name === "My Page");
await figma.setCurrentPageAsync(targetPage);

// 오토레이아웃 컨테이너(권장 기본 컨테이너)
const frame = figma.createAutoLayout('VERTICAL', { name: 'Column', itemSpacing: 8 });

// CSS류 셀렉터 + 배치 속성
figma.currentPage.query('FRAME[name^=Card] TEXT').set({
  fills: [{ type: 'SOLID', color: { r: 0.2, g: 0.2, b: 0.8 } }]
});

// 모든 created/mutated 노드 ID 반환(필수)
return { createdNodeIds: [...], mutatedNodeIds: [...] };
```

## 🛠️ 제공 도구 / 주요 파라미터
- **사용 MCP 도구**: `use_figma`, `get_screenshot`, `get_metadata`, `create_new_file` 등(deferred일 경우 단일 `ToolSearch query="select:..."`로 일괄 로드 권장).
- **Critical Rules(요약)**: `return`으로 출력(IIFE/`closePlugin` 금지), top-level `await`+`return`, `figma.notify()`/`console.log()` 출력 금지, `getPluginData`/`setPluginData` 미지원(→ shared 버전), 색상 0~1, fills/strokes 읽기전용(복제 후 재할당), 텍스트는 폰트 로드 후 mutate, 페이지는 `setCurrentPageAsync`(동기 setter throw), `setBoundVariableForPaint`는 새 paint 반환, `layoutSizingHorizontal/Vertical`의 HUG/FILL은 구조 제약(appendChild 후 설정), 새 top-level 노드는 (0,0) 회피, 모든 created/mutated ID 반환, 모든 Promise `await`.
- **에디터 모드**: design(`figma.com/design/`) 기본. FigJam(`/board/`)·Slides(`/slides/`)는 노드 타입이 다름. `figma.createPage()`는 Design 전용.
- **효율 API**: `node.query(selector)`, `node.set(props)`, `figma.createAutoLayout()`, `node.placeholder`(진행 중 셰이머), `await node.screenshot()`.
- **워크플로우**: 인스펙트 우선 → 스켈레톤(placeholder) → 섹션 증분 채우기 → 매 호출 ID 반환 → 단계별 검증. 호출당 최대 10 논리 연산. 에러 시 STOP(원자적 — 부분 변경 없음), 메시지 읽고 수정 후 재시도.
- 참조: gotchas/common-patterns/plugin-api-patterns/api-reference/validation-and-recovery/component·variable·text-style·effect-style-patterns, plugin-api-standalone(.index.md/.d.ts — grep으로 부분 로드), working-with-design-systems/wwds.md.

## 📦 출처 / 설치 상태
- 플러그인: figma@claude-plugins-official
- 활성화: ON (`disable-model-invocation: false`)
- 관련 MCP: Figma MCP 연동(`use_figma` 등). 모든 Figma 쓰기/JS 실행 작업의 기반 스킬.
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/figma/2.2.12/skills/figma-use/SKILL.md`
