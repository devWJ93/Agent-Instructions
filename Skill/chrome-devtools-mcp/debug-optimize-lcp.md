# debug-optimize-lcp

> Chrome DevTools MCP로 Largest Contentful Paint(LCP)를 디버깅하고 최적화하는 스킬

## 📌 용도
LCP(최대 콘텐츠 렌더링 시간) 성능을 진단하고 최적화한다. LCP는 페이지의 주요 콘텐츠(가장 큰 이미지/텍스트 블록)가 뷰포트에 렌더링되기까지의 시간으로, Core Web Vital 지표이며 사용자 경험과 검색 순위에 직접 영향을 준다.

- **양호**: 2.5초 이하
- **개선 필요**: 2.5~4.0초
- **불량**: 4.0초 초과

모바일 페이지 73%에서 LCP 요소는 이미지다.

**LCP 4개 하위 구간**(빈틈/중복 없이 순차적):
| 구간 | 이상적 비율 | 측정 내용 |
| --- | --- | --- |
| TTFB | ~40% | 내비게이션 시작 → HTML 첫 바이트 수신 |
| Resource load delay | <10% | TTFB → LCP 리소스 로딩 시작 |
| Resource load duration | ~40% | LCP 리소스 다운로드 시간 |
| Element render delay | <10% | 리소스 다운로드 완료 → 요소 렌더링 |

병목 구간을 파악하는 것이 효과적 최적화의 핵심이다. **흔한 함정**: 한 구간만 최적화(이미지 압축)하면 절약 시간이 다른 구간(render delay)으로 이동해 효과가 없을 수 있다.

## 🎯 트리거 조건
- **언제 호출되는가**: LCP 성능, 느린 페이지 로딩, Core Web Vitals 최적화 질문 시. "largest contentful paint", "page load speed", "CWV", 히어로 이미지/주요 콘텐츠 렌더링 속도 개선 요청.
- **언제 쓰지 않는가**: 접근성(→ a11y-debugging), 메모리(→ memory-leak-debugging), 일반 디버깅(→ chrome-devtools).

## 💻 사용 예시 / 명령어
- "우리 페이지 LCP가 왜 느린지 분석해줘"
- "히어로 이미지가 너무 늦게 뜨는데 개선해줘"
- "Core Web Vitals 점수를 올리고 싶어"

## 🛠️ 제공 도구 / 주요 파라미터
**디버깅 워크플로(순서대로)**:
1. **트레이스 기록**: `navigate_page` 후 `performance_start_trace`(`reload: true`, `autoStop: true`)로 전체 로딩 캡처. 출력의 insight set ID를 기록.
2. **LCP 인사이트 분석**: `performance_analyze_insight`로 드릴다운. 인사이트 이름: `LCPBreakdown`(4개 구간 타이밍), `DocumentLatency`(TTFB), `RenderBlocking`(렌더 차단 리소스), `LCPDiscovery`(조기 발견 여부).
3. **LCP 요소 식별**: `evaluate_script` + "Identify LCP Element" 스니펫으로 태그·리소스 URL·타이밍 확인. `url`이 비면 텍스트 기반 요소.
4. **네트워크 워터폴 확인**: `list_network_requests`(`resourceTypes: ["Image", "Font"]`), `get_network_request`로 시작 시간·duration 점검.
5. **HTML 공통 이슈 점검**: `evaluate_script` + "Audit Common Issues" 스니펫으로 lazy-load 이미지, fetchpriority 누락, 렌더 차단 스크립트 확인.

**최적화 전략(우선순위)**:
1. **Resource load delay 제거**: 표준 `<img src>` 사용, LCP 이미지는 절대 lazy-load 금지, `fetchpriority="high"` 또는 `<link rel="preload">` 추가.
2. **Element render delay 제거**: 크리티컬 CSS 인라인, 비크리티컬 CSS/JS defer, 긴 작업 분할, SSR.
3. **Resource load duration 단축**: WebP/AVIF, `srcset`, CDN, `Cache-Control`, `font-display: swap`.
4. **TTFB 단축**: 리다이렉트 최소화, 엣지 캐싱(CDN), bfcache 적격화.

**검증/에뮬레이션**: 수정 후 `performance_start_trace`(`reload: true`) 재실행하여 병목 축소 확인. `emulate`(`networkConditions: "Fast 3G"`, `cpuThrottlingRate: 4`)로 제약 환경 테스트.

**참조 파일**: `references/lcp-snippets.md`

## 📦 출처 / 설치 상태
- 플러그인: chrome-devtools-mcp@claude-plugins-official
- 활성화: ON (Chrome DevTools MCP 서버와 연동)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/chrome-devtools-mcp/1.2.0/skills/debug-optimize-lcp/SKILL.md`
