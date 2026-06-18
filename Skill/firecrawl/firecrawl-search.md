# firecrawl-search

> 전체 페이지 콘텐츠 추출까지 지원하는 웹 검색 스킬. 스니펫이 아닌 실제 결과 + 풀페이지 마크다운.

## 📌 용도
선택적 콘텐츠 스크래핑이 포함된 웹 검색이다. 검색 결과를 JSON으로 반환하며, 옵션으로 풀 페이지 콘텐츠를 함께 가져온다. Claude 내장 WebSearch를 넘어서는 실제 검색 결과(스니펫이 아닌 전체 마크다운)를 제공한다.

- 특정 URL이 아직 없을 때
- 페이지 발견, 질문 답변, 소스 탐색이 필요할 때
- 워크플로우 에스컬레이션 패턴 첫 단계: **search** → scrape → map → crawl → interact

## 🎯 트리거 조건
- **언제 호출되는가**: 웹 검색, 기사/주제 리서치, 조회, 최신 뉴스, 소스 발견. "search for", "find me", "look up", "what are people saying about", "find articles about".
- **언제 쓰지 않는가**: 이미 URL이 있을 때(→ `firecrawl-scrape`), 사이트 내 URL 발견(→ `firecrawl-map`), 사이트 대량 추출(→ `firecrawl-crawl`).

## 💻 사용 예시 / 명령어
```bash
# 기본 검색
firecrawl search "your query" -o .firecrawl/result.json --json

# 검색 + 결과 풀페이지 스크래프
firecrawl search "your query" --scrape -o .firecrawl/scraped.json --json

# 지난 하루 뉴스
firecrawl search "your query" --sources news --tbs qdr:d -o .firecrawl/news.json --json
```

## 🛠️ 제공 도구 / 주요 파라미터
- 사용 도구: `Bash(firecrawl *)`, `Bash(npx firecrawl *)`

| 옵션 | 설명 |
| --- | --- |
| `--limit <n>` | 최대 결과 수 |
| `--sources <web,images,news>` | 검색 소스 타입 |
| `--categories <github,research,pdf>` | 카테고리 필터 |
| `--tbs <qdr:h\|d\|w\|m\|y>` | 시간 기반 필터 |
| `--location` / `--country <code>` | 위치/국가 코드 |
| `--scrape` | 각 결과의 풀 페이지 콘텐츠도 스크래프 |
| `--scrape-formats` | 스크래프 시 포맷(기본 markdown) |
| `-o, --output <path>` / `--json` | 출력 경로 / JSON 출력 |

**팁**: `--scrape`는 풀 콘텐츠를 가져오므로 결과 URL을 재스크래프하지 말 것(크레딧 절약). 결과는 `.firecrawl/`에 저장. `jq -r '.data.web[].url'`로 URL 추출.

## 검색 후 피드백 (1크레딧 환불)
검색은 2크레딧. 결과를 실제로 사용/판단한 뒤 `firecrawl search-feedback <id>`로 구조화된 피드백을 보내면 검색당 첫 피드백 시 1크레딧 환불 + 검색 품질 개선에 기여. 백그라운드(`--silent &`)로 검색당 1회.

- **시간 창**: 검색 후 ~2분 이내(늦으면 거부).
- **`--missing-content`가 가장 중요**: 기대했지만 못 찾은 콘텐츠를 한 항목당 한 주제로. JSON 배열 `{topic, description?}` 선호.
- **실질 내용 필수**: `good`→`--valuable-sources` 1개 이상, `partial`→둘 중 하나, `bad`→`--missing-content` 또는 `--query-suggestions`(미충족 시 HTTP 400).
- **일일 환불 상한**: 팀·UTC일 기준 기본 100크레딧. `dailyCapReached: true`면 그날 호출 중단.
- **옵트아웃**: `FIRECRAWL_NO_SEARCH_FEEDBACK=1`(또는 `FIRECRAWL_DISABLE_SEARCH_FEEDBACK=1`)이면 조용히 스킵 — 우회 금지.

```bash
SEARCH_ID=$(jq -r '.id' .firecrawl/search-react-hooks.json)
firecrawl search-feedback "$SEARCH_ID" \
  --rating good \
  --valuable-sources '[{"url":"https://react.dev/reference/react/hooks","reason":"Most authoritative"}]' \
  --missing-content '[{"topic":"useDeferredValue","description":"No example with Suspense"}]' \
  --silent &
```

## 📦 출처 / 설치 상태
- 플러그인: firecrawl@claude-plugins-official
- 활성화: ON
- 관련 MCP: firecrawl은 CLI 기반(`firecrawl search`)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/firecrawl/1.0.9/skills/firecrawl-search/SKILL.md`
