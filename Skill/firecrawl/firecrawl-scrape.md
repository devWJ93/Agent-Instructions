# firecrawl-scrape

> 임의의 URL(JS 렌더링 SPA 포함)에서 클린 마크다운을 추출하는 스킬. WebFetch 대체.

## 📌 용도
하나 이상의 URL을 스크래프한다. 클린한 LLM 최적화 마크다운을 반환하며, 여러 URL은 동시에 스크래프한다. 정적 페이지와 JS 렌더링 SPA를 모두 처리한다. 웹페이지 콘텐츠 추출에는 WebFetch 대신 이 스킬을 사용한다.

- 특정 URL이 있고 그 내용을 원할 때
- 페이지가 정적 또는 JS 렌더링(SPA)일 때
- 워크플로우 에스컬레이션 패턴 2단계: search → **scrape** → map → crawl → interact

## 🎯 트리거 조건
- **언제 호출되는가**: 사용자가 URL을 주며 내용을 원할 때, "scrape", "grab", "fetch", "pull", "get the page", "extract from this URL", "read this webpage".
- **언제 쓰지 않는가**: URL이 없어 먼저 찾아야 할 때(→ `firecrawl-search`), 클릭/폼 등 상호작용이 필요할 때(→ `firecrawl-interact`), 전체 사이트 로컬 다운로드(→ `firecrawl-download`), 로컬 파일(→ `firecrawl-parse`).

## 💻 사용 예시 / 명령어
```bash
# 기본 마크다운 추출
firecrawl scrape "<url>" -o .firecrawl/page.md

# 본문만(nav/footer 제외)
firecrawl scrape "<url>" --only-main-content -o .firecrawl/page.md

# JS 렌더링 대기 후 스크래프
firecrawl scrape "<url>" --wait-for 3000 -o .firecrawl/page.md

# 여러 URL(각각 .firecrawl/에 저장)
firecrawl scrape https://example.com https://example.com/blog https://example.com/docs

# 마크다운 + 링크 함께
firecrawl scrape "<url>" --format markdown,links -o .firecrawl/page.json

# 페이지에 대한 질문
firecrawl scrape "https://example.com/pricing" --query "What is the enterprise plan price?"
```

## 🛠️ 제공 도구 / 주요 파라미터
- 사용 도구: `Bash(firecrawl *)`, `Bash(npx firecrawl *)`

| 옵션 | 설명 |
| --- | --- |
| `-f, --format <formats>` | 출력 포맷: markdown, html, rawHtml, links, screenshot, json |
| `-Q, --query <prompt>` | 페이지 내용에 대한 질문(5 크레딧) |
| `-H` | 출력에 HTTP 헤더 포함 |
| `--only-main-content` | nav/footer/사이드바 제거, 본문만 |
| `--wait-for <ms>` | 스크래프 전 JS 렌더링 대기 |
| `--include-tags <tags>` / `--exclude-tags <tags>` | 특정 HTML 태그만/제외 |
| `--redact-pii` | 출력에서 개인식별정보 마스킹 |
| `-o, --output <path>` | 출력 파일 경로 |

**팁**: `--query`보다 일반 scrape 선호(파일로 저장 후 `grep`/`head`/직접 읽기로 전체 내용 추론 가능; `--query`는 5크레딧 추가). interact 전에 먼저 scrape 시도. 여러 URL은 동시 스크래프(`firecrawl --status`로 동시성 한도 확인). 단일 포맷은 raw, 복수 포맷은 JSON. URL은 항상 따옴표. 명명 규칙: `.firecrawl/{site}-{path}.md`.

## 📦 출처 / 설치 상태
- 플러그인: firecrawl@claude-plugins-official
- 활성화: ON
- 관련 MCP: firecrawl은 CLI 기반(`firecrawl scrape`)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/firecrawl/1.0.9/skills/firecrawl-scrape/SKILL.md`
