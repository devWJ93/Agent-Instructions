# figma-use-figjam

> FigJam 컨텍스트에서 `use_figma` MCP 도구를 사용하기 위한 FigJam 특화 스킬. `figma-use`와 함께 사용.

## 📌 용도
`use_figma` MCP 도구를 FigJam(`figma.com/board/...`) 컨텍스트에서 쓰기 위한 FigJam 특화 컨텍스트를 제공한다. 기반 컨텍스트와 전체 Plugin API는 [figma-use](./figma-use.md)에 있고, 이 스킬은 FigJam만의 차이(인스펙션 도구, 이미지 업로드, 노드 타입, 색상 등)를 다룬다.

> `use_figma`를 FigJam 작업에 호출할 때 `skillNames: "figma-use-figjam"` 전달(로깅용).
> **FigJam에서 `figma.createPage()` 금지** — Design 전용 API. FigJam은 단일 암묵 페이지이며 섹션으로 콘텐츠를 정리.

## 🎯 트리거 조건
- **언제 호출되는가**: FigJam 보드(`figma.com/board/...`)에서 `use_figma`로 작업할 때 — 스티키/섹션/커넥터/도형/텍스트/표/코드블록/라벨 생성·수정, 보드 템플릿/레트로/브레인스토밍/회의 보드 구성, 마인드맵 등. `figma-use`와 함께 로드.
- **언제 쓰지 않는가**: Design 파일(→ `figma-use` 단독), Slides 파일(→ `figma-use-slides`).

## 💻 사용 예시 / 명령어
**인스펙션**: `get_figjam`이 FigJam 인스펙션 도구. 전체 노드 트리를 XML로 반환(페이지/섹션/스티키/커넥터 ID 포함). 후속 `use_figma`에서 ID 참조 전 먼저 호출. `console.log`는 반환되지 않으므로 인스펙션 스크립트로 ID 발견 금지 — `return` 또는 `get_figjam` 사용.
- `get_metadata`는 FigJam에서 **동작 안 함**(design 전용).
- `get_screenshot`은 유효한 `nodeId` 필요(`get_figjam`에서 먼저 획득).

**이미지 추가**: `upload_assets`가 FigJam에 이미지를 추가하는 **유일한** 지원 방법. `figma.createImage()`/`createImageAsync()`는 FigJam에서 미지원. FigJam `fileKey`로 호출 → 단일사용 업로드 URL 반환 → raw 바이트 POST. `nodeId`(+`count:1`) 전달 시 기존 노드에 fill로 부착, 생략 시 새 레이어로 배치.

```
ToolSearch query="select:use_figma,get_figjam,get_screenshot,get_metadata,create_new_file"
```

## 🛠️ 제공 도구 / 주요 파라미터
- **사용 MCP 도구**: `use_figma`, `get_figjam`(인스펙션), `get_screenshot`, `create_new_file`, `upload_assets`(이미지). (`get_metadata`는 FigJam 미지원)
- **텍스트 mutation**: Design과 동일한 canonical recipe(폰트 로드→await→mutate→ID 반환). FigJam 서브레이어 기본 폰트가 다름(sticky/shape → `Inter Medium`, connector → 설정 전 invalid)이므로 하드코딩 대신 `node.text.fontName`에서 로드.
- **참조 문서**: `plan-board-content`(보드 콘텐츠 요청 시 필수), `create-section`/`create-sticky`/`create-connector`/`create-text`/`create-shape-with-text`/`create-code-block`/`create-table`/`create-label`, `position-figjam-nodes`, `edit-text`, `batch-modify`, `figjam-colors`(노드 타입별 팔레트 + `hex/255` 규칙·`h()` 헬퍼). 여러 참조가 필요하면 단일 병렬 배치로 읽기.

## 📦 출처 / 설치 상태
- 플러그인: figma@claude-plugins-official
- 활성화: ON (`disable-model-invocation: false`)
- 관련 MCP: Figma MCP 연동(FigJam 컨텍스트). `figma-use` 기반 위에서 동작.
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/figma/2.2.12/skills/figma-use-figjam/SKILL.md`
