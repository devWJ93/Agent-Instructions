# firecrawl-map

> 웹사이트의 모든 URL을 발견·나열하는 스킬. 검색 필터로 특정 페이지 위치 파악.

## 📌 용도
사이트의 URL을 발견한다. `--search`로 대형 사이트 안에서 특정 페이지를 찾을 수 있다. 사이트는 알지만 정확한 페이지를 모를 때 필수적이다.

- 대형 사이트에서 특정 서브페이지를 찾아야 할 때
- 스크래프/크롤 전에 전체 URL 목록이 필요할 때
- 워크플로우 에스컬레이션 패턴 3단계: search → scrape → **map** → crawl → interact

## 🎯 트리거 조건
- **언제 호출되는가**: "map the site", "find the URL for", "what pages are on", "list all pages", 사이트 구조 파악, 도메인 내 위치 찾기.
- **언제 쓰지 않는가**: 이미 URL을 알고 내용만 필요할 때(→ `firecrawl-scrape`), 발견 후 바로 대량 추출(→ `firecrawl-crawl`), 로컬 다운로드(→ `firecrawl-download`).

## 💻 사용 예시 / 명령어
```bash
# 대형 사이트에서 특정 페이지 찾기
firecrawl map "<url>" --search "authentication" -o .firecrawl/filtered.txt

# 모든 URL 가져오기
firecrawl map "<url>" --limit 500 --json -o .firecrawl/urls.json
```

**Map + scrape 패턴(흔한 조합)**: `map --search`로 올바른 URL을 찾고 그 URL을 `scrape`.
예: `map https://docs.example.com --search "auth"` → `/docs/api/authentication` 발견 → 해당 URL `scrape`.

## 🛠️ 제공 도구 / 주요 파라미터
- 사용 도구: `Bash(firecrawl *)`, `Bash(npx firecrawl *)`

| 옵션 | 설명 |
| --- | --- |
| `--limit <n>` | 반환할 최대 URL 수 |
| `--search <query>` | 검색어로 URL 필터 |
| `--sitemap <include\|skip\|only>` | 사이트맵 처리 전략 |
| `--include-subdomains` | 서브도메인 URL 포함 |
| `--json` | JSON 출력 |
| `-o, --output <path>` | 출력 파일 경로 |

## 📦 출처 / 설치 상태
- 플러그인: firecrawl@claude-plugins-official
- 활성화: ON
- 관련 MCP: firecrawl은 CLI 기반(`firecrawl map`)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/firecrawl/1.0.9/skills/firecrawl-map/SKILL.md`
