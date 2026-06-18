# figma-generate-design

> 코드/설명으로부터 디자인 시스템을 재사용해 Figma에 화면·뷰·다중 섹션 레이아웃을 구축/업데이트하는 스킬.

## 📌 용도
게시된 디자인 시스템(컴포넌트·변수·스타일)을 **재사용**해 Figma에 화면·뷰·다중 섹션 UI 컨테이너를 만들거나 업데이트한다. 전체 페이지, 모달, 다이얼로그, 드로어, 사이드바, 패널 등 다중 섹션 합성 뷰가 대상이다. 핵심 인사이트: Figma 파일에는 코드의 UI 컴포넌트/토큰에 대응하는 게시 디자인 시스템이 있으므로, 박스에 hex 색을 칠하는 대신 그것을 찾아 사용한다.

> **필수**: 모든 `use_figma` 호출 전에 [figma-use](./figma-use.md)도 로드(색상 범위·폰트 로딩 등 핵심 규칙). `use_figma` 호출 시 `skillNames: "figma-generate-design"` 전달(로깅용).

**핵심 워크플로우**
- **Step 1**: 산출물 이해 — 소스 읽고 섹션/컴포넌트 식별, 이미지 포함 여부 확인.
- **Step 2**: 컴포넌트 키·변수·스타일 수집 — (2a-i) Code Connect 우선 확인 → (2a-ii) 기존 화면 인스펙트 → (2a-iii) 최후로 `search_design_system`. (2b) 변수, (2c) 스타일 발견.
- **Step 3**: 래퍼 프레임 먼저 생성(섹션을 페이지 자식으로 만들고 reparent 금지).
- **Step 4**: 래퍼 안에 섹션을 한 번에 하나씩 빌드(자체 `use_figma` 호출). 인스턴스 텍스트는 `setProperties()`로 오버라이드.
- **Step 5**: 전체 뷰 검증 + 이미지 전송(웹앱+이미지 시 `generate_figma_design` 병렬 캡처에서 `imageHash` 복사).
- **Step 6**: 기존 뷰 업데이트(`get_metadata`로 구조 확인, 변경 섹션만 수정).

## 🎯 트리거 조건
- **언제 호출되는가**: "write to Figma", "create in Figma from code", "push page to Figma", "create a screen", "build a landing page in Figma", "update the Figma screen to match code", "convert this modal/dialog/drawer/panel to Figma". 코드/설명에서 전체 페이지·모달·드로어·사이드바·패널 등 다중 섹션 뷰를 만들거나 업데이트.
- **언제 쓰지 않는가**: 새 재사용 컴포넌트/배리언트 생성(→ `figma-use` 직접), Code Connect 매핑 작성(→ `figma-code-connect`).

## 💻 사용 예시 / 명령어
```js
// 래퍼 먼저 생성(Step 3) — 빈 공간 찾아 배치, ID 반환
const wrapper = figma.createAutoLayout("VERTICAL");
wrapper.name = "Homepage"; wrapper.resize(1440, 100);
wrapper.layoutSizingHorizontal = "FIXED";
return { success: true, wrapperId: wrapper.id };
```
```js
// 섹션 빌드(Step 4) — 디자인 시스템 의존성 병렬 임포트
const [wrapper, buttonSet, bgColorVar] = await Promise.all([
  figma.getNodeByIdAsync("WRAPPER_ID"),
  figma.importComponentSetByKeyAsync("BUTTON_SET_KEY"),
  figma.variables.importVariableByKeyAsync("BG_COLOR_VAR_KEY"),
]);
```

## 🛠️ 제공 도구 / 주요 파라미터
- **사용 MCP 도구**: `use_figma`, `search_design_system`(+ `includeComponents`/`includeVariables`/`includeStyles`/`includeLibraryKeys`), `get_libraries`, `get_screenshot`, `get_metadata`, `generate_figma_design`(웹앱 픽셀퍼펙트 캡처, 이미지 필수 시 병렬).
- **하드 게이트**: 2a-i 완료 + 2a-ii 시도/로그 전 `search_design_system` 금지. Step 2 모든 행 완료 전 캔버스 변형 `use_figma` 금지.
- **변수 발견 주의**: `getLocalVariableCollectionsAsync()`는 로컬 변수만 — 비어 있어도 "변수 없음" 결론 금지. 라이브러리 변수는 `search_design_system`(`includeVariables: true`)로 확인.
- **빌드 vs 임포트**: 래퍼·섹션 컨테이너·레이아웃 그리드는 수동, 컴포넌트·변수(색/간격/반경)·텍스트/이펙트 스타일은 디자인 시스템에서 임포트. hex/픽셀 하드코딩 금지(`setBoundVariable`/`setBoundVariableForPaint`).
- 참조: `figma-use`의 component-patterns/variable-patterns/text-style-patterns/effect-style-patterns/gotchas.

## 📦 출처 / 설치 상태
- 플러그인: figma@claude-plugins-official
- 활성화: ON (`disable-model-invocation: false`)
- 관련 MCP: Figma MCP 연동 필수(게시 디자인 시스템 있는 대상 파일 필요). `figma-use`와 함께 로드.
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/figma/2.2.12/skills/figma-generate-design/SKILL.md`
