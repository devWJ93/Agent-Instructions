# firecrawl-cli

> Firecrawl CLI로 웹을 검색·스크래핑·상호작용하는 최상위 워크플로우 스킬. LLM에 최적화된 클린 마크다운 반환.

> 참고: 이 스킬의 frontmatter `name`은 `firecrawl`이지만, 플러그인 디렉터리/슬래시 명칭은 `firecrawl-cli`다.

## 📌 용도
웹 검색, 스크래핑, 페이지 상호작용을 모두 아우르는 Firecrawl CLI의 진입점/허브 스킬이다. 결과는 LLM 컨텍스트에 최적화된 클린 마크다운으로 반환한다. 개별 작업은 하위 스킬(search/scrape/map/crawl/agent/interact/download/parse/monitor)로 라우팅한다.

**워크플로우 에스컬레이션 패턴** (필요에 따라 단계 상승):
1. **search** — 특정 URL이 아직 없음. 페이지 발견, 질문 답변, 소스 탐색.
2. **scrape** — URL 보유. 내용 직접 추출(정적/JS 렌더링 모두).
3. **map + scrape** — 대형 사이트에서 특정 서브페이지를 찾을 때. `map --search`로 URL 찾고 스크래프.
4. **crawl** — 사이트 섹션 전체 대량 추출(예: 모든 `/docs/`).
5. **monitor** — 반복 점검/알림. 일회성 반복 스크래프 대신 `--page` + `--goal` 모니터 설정.
6. **interact** — 스크래프 후 페이지 상호작용(페이지네이션, 모달, 폼 제출, 다단계 내비게이션).

## 🎯 트리거 조건
- **언제 호출되는가**: 웹 검색, 기사/주제 리서치, 온라인 조회, 웹페이지 스크래핑, URL에서 콘텐츠 가져오기, 문서 크롤링, 사이트 다운로드, 클릭/로그인이 필요한 상호작용. "fetch this page", "pull the content from", "get the page at https://", 외부 웹사이트 언급 등.
- **언제 쓰지 않는가**: 로컬 파일 작업, git 명령, 배포, 코드 편집 작업.

## 💻 사용 예시 / 명령어
```bash
# 상태/인증 확인
firecrawl --status

# 설치 검증
mkdir -p .firecrawl
firecrawl scrape "https://firecrawl.dev" -o .firecrawl/install-check.md

# 검색 + 스크래프
firecrawl search "query" --scrape --limit 3

# 모니터 생성(단일 페이지, 이메일 알림)
firecrawl monitor create --name "Blog" --schedule "every 5 minutes" \
  --goal "Alert when a new blog post is published." \
  --page https://example.com/blog --email alerts@example.com

# 병렬 스크래프(동시성 한도 내)
firecrawl scrape "<url-1>" -o .firecrawl/1.md &
firecrawl scrape "<url-2>" -o .firecrawl/2.md &
wait

# 크레딧 사용량
firecrawl credit-usage
```

## 🛠️ 제공 도구 / 주요 파라미터
- 사용 도구: `Bash(firecrawl *)`, `Bash(npx firecrawl *)`
- **명령 라우팅 표**: search / scrape / map / crawl / agent / interact / download / parse / monitor.
- **모니터 `--goal` 작성 규칙**: 사용자 의도를 `Alert when ...` 형식의 2~3문장으로 변환. 범위(top N, 가격, 역할, 회사, 지역 등) 명시. 의도별 예외만 `Ignore ...`로 추가(일반 노이즈는 judge가 자동 처리). 모호하면 범위를 넓게 유지.
- **JSON-mode change tracking**: 특정 구조화 필드(가격, 헤드라인, 재고)만 감시 시 `changeTracking` 포맷 + `modes: ["json"]` + JSON 스키마를 JSON 페이로드로 전달. 결과는 `plans[0].price` 같은 필드별 diff. `["json","git-diff"]`는 혼합 모드.
- **출력 규칙**: 결과는 `-o`로 `.firecrawl/`에 저장하고 `.gitignore`에 추가. URL은 항상 따옴표(`?`, `&` 보호). 전체 파일 통째로 읽지 말고 `grep`/`head`/증분 읽기. 단일 포맷은 raw, 복수 포맷(`--format markdown,links`)은 JSON.
- **search 후 피드백**: 검색은 2크레딧, `firecrawl search-feedback <id>`로 첫 피드백 시 1크레딧 환불. `FIRECRAWL_NO_SEARCH_FEEDBACK=1`이면 비활성.
- **연계 스킬**: 앱 통합은 `firecrawl-build`, 결과물 워크플로우(리서치/SEO/QA/리드/지식베이스/디자인시스템 추출)는 `firecrawl-workflows`.

## 📦 출처 / 설치 상태
- 플러그인: firecrawl@claude-plugins-official
- 활성화: ON
- 관련 MCP: firecrawl은 CLI 기반(설치·인증 필요, `firecrawl --status`로 확인). 동시성/크레딧 한도 존재.
- 부속 룰 파일: `rules/install.md`(설치·인증), `rules/security.md`(출력·안전 읽기)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/firecrawl/1.0.9/skills/firecrawl-cli/SKILL.md`
