# figma-code-connect

> Figma 컴포넌트를 코드 스니펫에 매핑하는 Code Connect 템플릿 파일(`.figma.ts`)을 생성·유지하는 스킬.

## 📌 용도
Figma 컴포넌트를 코드 스니펫에 매핑하는 Code Connect **템플릿 파일**(`.figma.ts`)을 만든다. Figma URL을 받아 정해진 단계를 따라 템플릿을 생성한다. 이 스킬은 MCP 도구로 컴포넌트 컨텍스트를 가져오는 **템플릿 파일(`.figma.ts`)** 만 다룬다(파서 기반 `.figma.tsx` — `figma.connect()` + CLI 게시 — 는 범위 밖).

**6단계 워크플로우**
1. **Figma URL 파싱** — `fileKey`, `nodeId` 추출. nodeId 하이픈→콜론 변환(`1234-5678`→`1234:5678`).
2. **미매핑 컴포넌트 발견** — `get_code_connect_suggestions`(`excludeMappingPrompt: true`)로 미매핑 게시 컴포넌트 식별, `mainComponentNodeId` 추출.
3. **컴포넌트 속성 페치** — `get_context_for_code_connect`로 속성 정의(TEXT/BOOLEAN/VARIANT/INSTANCE_SWAP/SLOT) 확인.
4. **코드 컴포넌트 식별** — `figma.config.json`의 paths/importPaths로 코드 컴포넌트를 찾아 props 인터페이스 비교, 사용자 확인.
5. **템플릿 파일 생성**(`.figma.ts`) — 속성 매핑 메서드로 작성.
6. **검증** — 속성 커버리지, 유효·정확한 타입, 하드코딩 자식 없음, 보간 래핑 확인.

## 🎯 트리거 조건
- **언제 호출되는가**: Code Connect 언급, Figma 컴포넌트 매핑, 디자인→코드 변환, `.figma.ts`/`.figma.js` 파일 생성/업데이트 요청.
- **언제 쓰지 않는가**: 파서 기반 `.figma.tsx`(CLI 게시) 작업, 컴포넌트 미게시 상태, Organization/Enterprise 플랜이 아닌 경우(Code Connect 미지원), Figma MCP 미연결 상태.

## 💻 사용 예시 / 명령어
입력 예: `https://www.figma.com/design/QiEF6w564ggoW8ftcLvdcu/MyDesignSystem?node-id=4185-3778`
→ `fileKey = QiEF6w564ggoW8ftcLvdcu`, `nodeId = 4185:3778`

생성되는 템플릿 구조(`ComponentName.figma.ts`):
```ts
// url=https://www.figma.com/file/{fileKey}/{fileName}?node-id={nodeId}
// source={코드 컴포넌트 경로}
// component={코드 컴포넌트명}
import figma from 'figma'
const instance = figma.selectedInstance

const label = instance.getString('Label')
const variant = instance.getEnum('Variant', { 'Primary': 'primary', 'Secondary': 'secondary' })
const disabled = instance.getBoolean('Disabled')

export default {
  example: figma.code`<Button variant="${variant}" ${disabled ? 'disabled' : ''}>${label}</Button>`,
  imports: ['import { Button } from "primitives"'],
  id: 'button',
  metadata: { nestable: true }
}
```

## 🛠️ 제공 도구 / 주요 파라미터
- **사용 MCP 도구**: `get_code_connect_suggestions`, `get_context_for_code_connect` (Figma MCP).
- **속성 매핑 메서드**: `getString`(TEXT), `getBoolean`(BOOLEAN), `getEnum`(VARIANT, 모든 값 빠짐없이 매핑), `getInstanceSwap`(INSTANCE_SWAP), `getSlot`(SLOT 전용), `findInstance`/`findText`/`findConnectedInstance`(명명 자식 레이어).
- **보간 래핑**: 문자열→따옴표, 인스턴스/섹션(`executeTemplate().example`)→중괄호, 슬롯 섹션→`figma.code\`...\`` 내부 직접, boolean→조건식.
- **주요 규칙(Pitfalls)**: 템플릿 결과 문자열 연결 금지(`[object Object]` 발생), `hasCodeConnect()` 가드 사용 금지, `executeTemplate()` 전 `type === 'INSTANCE'` 체크, INSTANCE_SWAP엔 `getInstanceSwap()` 선호, 슬롯/자식 콘텐츠 하드코딩 금지, 속성명 대소문자 정확히, 코드 prop에 없는 속성 발명 금지.
- 부속 참조: `references/api.md`(전체 API), `references/advanced-patterns.md`(중첩·메타데이터 prop 전달).

## 📦 출처 / 설치 상태
- 플러그인: figma@claude-plugins-official
- 활성화: ON (`disable-model-invocation: false`)
- 관련 MCP: Figma MCP 연동(`get_code_connect_suggestions`, `get_context_for_code_connect` 등). MCP 미연결 시 활성화 후 재시작 안내.
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/figma/2.2.12/skills/figma-code-connect/SKILL.md`
