# a11y-debugging

> Chrome DevTools MCP를 활용해 web.dev 가이드라인 기반으로 웹 페이지의 접근성(a11y)을 디버깅·감사하는 스킬

## 📌 용도
웹 페이지의 접근성 문제를 진단하고 개선 방향을 제시한다. 시맨틱 HTML, ARIA 라벨, 포커스 상태, 키보드 내비게이션, 탭 타겟 크기, 색상 대비 등을 점검한다.

핵심 개념은 **접근성 트리(Accessibility Tree) vs DOM**의 차이다. 시각적으로 요소를 숨기는 방식(`opacity: 0`)은 스크린 리더 입장에서 `display: none`이나 `aria-hidden="true"`와 다르게 동작한다. `take_snapshot` 도구가 반환하는 접근성 트리가 보조기술이 "보는" 것을 그대로 나타내므로 시맨틱 구조의 가장 신뢰할 수 있는 근거가 된다.

web.dev 문서를 읽을 때는 URL 끝에 `.md.txt`를 붙이면 깨끗한 raw 마크다운으로 받을 수 있다 (예: `https://web.dev/articles/accessible-tap-targets.md.txt`).

## 🎯 트리거 조건
- **언제 호출되는가**: 시맨틱 HTML 테스트, ARIA 라벨, 포커스 상태, 키보드 내비게이션, 탭 타겟, 색상 대비 검사 요청 시. "접근성 점검", "a11y 감사", "스크린 리더 호환성" 등의 키워드.
- **언제 쓰지 않는가**: 일반적인 디버깅·자동화(→ chrome-devtools), 성능/LCP(→ debug-optimize-lcp), 메모리 누수(→ memory-leak-debugging)는 별도 스킬 사용.

## 💻 사용 예시 / 명령어
- "이 페이지 접근성 감사해줘"
- "버튼에 ARIA 라벨이 제대로 붙어 있는지 확인해줘"
- "키보드 탭 이동이 모달에 갇히는지 테스트해줘"
- "색상 대비가 WCAG 기준을 만족하는지 봐줘"

## 🛠️ 제공 도구 / 주요 파라미터
워크플로 패턴 8가지를 순차적으로 적용한다.

1. **자동 감사 (Lighthouse)**: `lighthouse_audit`를 `mode: "navigation"`, `outputDirPath` 지정으로 실행. `scores`(0~1) 확인, `jq`/Node.js 한 줄 스크립트로 실패 항목만 필터링(전체 리포트를 통째로 읽지 말 것).
2. **브라우저 이슈/감사**: `list_console_messages`를 `types: ["issue"]`, `includePreservedMessages: true`로 호출해 Chrome 네이티브 a11y 이슈(라벨 누락, 잘못된 ARIA 등) 확인.
3. **시맨틱 & 구조**: `take_snapshot`으로 헤딩 계층(h1~h3 건너뜀 여부) 점검, `take_screenshot`과 비교해 DOM 순서와 시각적 읽기 순서 일치 검증.
4. **라벨/폼/대체 텍스트**: 인터랙티브 요소의 접근 가능한 이름 확인, `evaluate_script` + "Find Orphaned Form Inputs" 스니펫으로 라벨 없는 입력 탐지, 이미지 `alt` 확인.
5. **포커스 & 키보드**: `press_key`로 `Tab`/`Shift+Tab` 이동 후 `take_snapshot`으로 포커스 위치 검증. 모달 포커스 트랩 확인.
6. **탭 타겟**: web.dev 기준 최소 48x48px. `evaluate_script` + "Measure Tap Target Size" 스니펫 사용(스냅샷의 `uid`를 인자로 전달).
7. **색상 대비**: `list_console_messages`로 "Low Contrast" 이슈 확인. 네이티브 감사가 못 잡으면 "Check Color Contrast" 스니펫 사용.
8. **전역 페이지 검사**: "Global Page Checks" 스니펫으로 문서 레벨 설정 점검.

**참조 파일**: `references/a11y-snippets.md` (evaluate_script용 스니펫 모음)
**주요 도구**: `take_snapshot`, `take_screenshot`, `lighthouse_audit`, `list_console_messages`, `press_key`, `evaluate_script`

## 📦 출처 / 설치 상태
- 플러그인: chrome-devtools-mcp@claude-plugins-official
- 활성화: ON (Chrome DevTools MCP 서버와 연동)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/chrome-devtools-mcp/1.2.0/skills/a11y-debugging/SKILL.md`
