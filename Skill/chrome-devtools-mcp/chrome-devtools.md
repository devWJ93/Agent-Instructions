# chrome-devtools

> Chrome DevTools를 MCP로 활용해 효율적으로 디버깅, 트러블슈팅, 브라우저 자동화를 수행하는 핵심 스킬

## 📌 용도
웹 페이지 디버깅, 브라우저 인터랙션 자동화, 성능 분석, 네트워크 요청 검사를 수행한다. chrome-devtools-mcp 플러그인의 중심 스킬로, 브라우저 제어 전반의 워크플로를 정의한다.

**브라우저 라이프사이클**: 첫 도구 호출 시 영속 Chrome 프로필로 브라우저가 자동 시작된다. MCP 서버 설정의 CLI 인자로 구성한다 (`npx chrome-devtools-mcp@latest --help`). 추가 기능은 플래그로 활성화: 확장 프로그램 도구는 `--categoryExtensions`, 메모리 도구는 `--memoryDebugging`.

**페이지 선택**: 도구는 현재 선택된 페이지에서 동작한다. `list_pages`로 페이지 목록 확인 후 `select_page`로 컨텍스트 전환.

**요소 인터랙션**: `take_snapshot`으로 각 요소의 고유 `uid`를 얻어 인터랙션한다. 요소를 못 찾으면 새 스냅샷을 찍는다(페이지 변경 가능성).

## 🎯 트리거 조건
- **언제 호출되는가**: 웹 페이지 디버깅, 브라우저 인터랙션 자동화, 성능 분석, 네트워크 요청 검사 시.
- **언제 쓰지 않는가**: `--slim` 모드(MCP 설정)에는 적용 안 됨. 접근성(→ a11y-debugging), LCP 최적화(→ debug-optimize-lcp), 메모리 누수(→ memory-leak-debugging), 셸 스크립트 자동화(→ chrome-devtools-cli), 연결 문제(→ troubleshooting)는 전용 스킬.

## 💻 사용 예시 / 명령어
- "이 페이지가 왜 안 뜨는지 디버깅해줘"
- "로그인 폼을 자동으로 채우고 제출해줘"
- "네트워크 요청 중 실패한 게 있는지 확인해줘"
- "Chrome 확장 프로그램을 테스트해줘"

## 🛠️ 제공 도구 / 주요 파라미터
**페이지 인터랙션 전 워크플로**:
1. `navigate_page` 또는 `new_page`로 이동
2. `wait_for`로 콘텐츠 로딩 대기(찾을 대상을 알 때)
3. `take_snapshot`으로 페이지 구조 파악
4. 스냅샷의 `uid`로 `click`, `fill` 등 인터랙션

**효율적 데이터 검색**:
- 큰 출력(스크린샷, 스냅샷, 트레이스)은 `filePath` 파라미터로 저장
- 페이지네이션(`pageIdx`, `pageSize`)·필터링(`types`)으로 데이터 최소화
- 입력 액션에서 업데이트된 상태가 필요 없으면 `includeSnapshot: false`

**도구 선택**:
- 자동화/인터랙션 → `take_snapshot`(텍스트 기반, 빠름)
- 시각적 검사 → `take_screenshot`
- 접근성 트리에 없는 데이터 → `evaluate_script`

**병렬 실행**: 여러 도구 호출을 병렬로 보낼 수 있으나 순서 유지(navigate → wait → snapshot → interact).

**확장 프로그램 테스트**: `--categoryExtensions` 플래그 필요. `install_extension` → `list_extensions`로 ID 확인 → `trigger_extension_action` → `evaluate_script`(serviceWorkerId)로 상태 검증.

**트러블슈팅 참조**: Chrome DevTools UI 문서, [공식 troubleshooting.md](https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/docs/troubleshooting.md)

## 📦 출처 / 설치 상태
- 플러그인: chrome-devtools-mcp@claude-plugins-official
- 활성화: ON (Chrome DevTools MCP 서버와 연동)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/chrome-devtools-mcp/1.2.0/skills/chrome-devtools/SKILL.md`
