# figma-generate-library

> 코드베이스로부터 Figma에 프로페셔널급 디자인 시스템(변수/토큰, 컴포넌트 라이브러리, 테마)을 구축/업데이트하는 스킬.

## 📌 용도
Figma에 코드와 일치하는 프로페셔널급 디자인 시스템을 구축한다. 20~100+회의 `use_figma` 호출에 걸친 다단계 워크플로우를 오케스트레이션하며, 실제 디자인 시스템(Material 3, Polaris, Figma UI3 등)의 품질 패턴을 강제한다. **무엇을 어떤 순서로** 만들지 가르치며, **어떻게** Plugin API를 호출하는지는 `figma-use`가 보완한다(둘 다 로드 필요).

> `use_figma` 호출 시 `skillNames: "figma-generate-library"` 전달(로깅용).

**가장 중요한 한 규칙**: 절대 원샷 작업이 아니다. 모든 작업을 최소 단위로 쪼개고, 검증하고, 피드백 받고, 진행한다.

**필수 페이즈 순서**
- **Phase 0 DISCOVERY** — 코드베이스 분석, Figma 인스펙트, 구독 라이브러리 검색, v1 스코프 락, 코드↔Figma 매핑. ✋체크포인트: 전체 계획 승인.
- **Phase 1 FOUNDATIONS** — 변수 컬렉션·모드, primitive/semantic 변수, scopes, code syntax, effect/text 스타일. ✋체크포인트.
- **Phase 2 FILE STRUCTURE** — 페이지 스켈레톤(Cover→Getting Started→Foundations→Components→Utilities), foundations 문서. ✋체크포인트.
- **Phase 3 COMPONENTS** — 컴포넌트를 하나씩(원자→분자 순서) 생성·배리언트·속성·문서·검증. ✋컴포넌트별 체크포인트.
- **Phase 4 INTEGRATION + QA** — Code Connect 마무리, 접근성·네이밍·바인딩 감사, 최종 스크린샷. ✋사인오프.

## 🎯 트리거 조건
- **언제 호출되는가**: 변수/토큰 생성, 컴포넌트 라이브러리 구축, 적절한 배리언트 세트·변수 바인딩의 개별 컴포넌트 생성, 테마(light/dark) 설정, 파운데이션 문서화, 코드↔Figma 갭 조정. "Figma에 컴포넌트 만들어/생성해" — 단일 컴포넌트라도 변수 파운데이션·배리언트 상태·토큰 바인딩이 필요하므로 이 스킬.
- **언제 쓰지 않는가**: 합성 화면/뷰 빌드(→ `figma-generate-design`), 단순 Plugin API 작업(→ `figma-use`).

## 💻 사용 예시 / 명령어
표준 토큰 아키텍처:
```
Collection "Primitives"  modes: ["Value"]   blue/500 = #3B82F6, gray/900 = #111827
Collection "Color"       modes: ["Light","Dark"]
  color/bg/primary → Light: alias Primitives/white, Dark: alias Primitives/gray-900
Collection "Spacing"     modes: ["Value"]   spacing/xs = 4, spacing/md = 16
```
상태 영속(컨텍스트 truncation 대비): `/tmp/dsb-state-{RUN_ID}.json`에 ledger 기록, 매 턴 재독.

## 🛠️ 제공 도구 / 주요 파라미터
- **사용 MCP 도구**: `use_figma`(직렬만 — 절대 병렬 금지), `get_libraries`, `search_design_system`(`includeComponents`/`includeVariables`/`includeStyles`/`includeLibraryKeys`), `get_metadata`, `get_screenshot`.
- **핵심 규칙**: 변수 BEFORE 컴포넌트, 생성 전 인스펙트, 컴포넌트당 1페이지(기본), 시각 속성은 변수 바인딩, 모든 변수에 scopes(ALL_SCOPES 금지)·code syntax(WEB은 `var()` 래퍼), semantic은 primitive에 alias, combineAsVariants 후 배리언트 그리드 배치, 아이콘은 INSTANCE_SWAP(배리언트 폭증 방지), 결정적 네이밍, 비파괴 클린업, 검증 후 진행, 노드 ID 환각 금지.
- **상태 관리**: `getPluginData`/`setPluginData` 미지원 → `getSharedPluginData`/`setSharedPluginData` 또는 이름·ledger 사용. 재개 프로토콜·continuation 프롬프트 제공.
- 참조 문서: `discovery-phase.md`, `token-creation.md`, `component-creation.md`, `code-connect-setup.md`, `naming-conventions.md`, `error-recovery.md`. 헬퍼 스크립트: `scripts/`(inspectFileStructure, createVariableCollection, createComponentWithVariants 등).

## 📦 출처 / 설치 상태
- 플러그인: figma@claude-plugins-official
- 활성화: ON (`disable-model-invocation: false`)
- 관련 MCP: Figma MCP 연동 필수. `figma-use`와 반드시 함께 로드.
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/figma/2.2.12/skills/figma-generate-library/SKILL.md`
