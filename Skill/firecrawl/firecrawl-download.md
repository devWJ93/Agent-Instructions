# firecrawl-download

> 웹사이트 전체를 로컬 파일(마크다운/스크린샷/다중 포맷)로 다운로드하는 스킬. (실험적)

## 📌 용도
`map` + `scrape`를 결합한 편의 명령으로, 사이트 전체를 로컬 파일로 저장한다. 먼저 사이트를 매핑해 페이지를 발견한 뒤 각 페이지를 `.firecrawl/` 아래 중첩 디렉터리로 스크래프한다. 모든 scrape 옵션이 download에서도 동작한다. 자동화 흐름에서는 항상 `-y`로 확인 프롬프트를 건너뛴다.

- 사이트(또는 섹션) 전체를 로컬 파일로 저장하고 싶을 때
- 문서를 오프라인으로 접근해야 할 때
- 정리된 파일 구조로 대량 콘텐츠 추출

> **실험적(Experimental)** 명령이다.

## 🎯 트리거 조건
- **언제 호출되는가**: "사이트 다운로드", "로컬 파일로 저장", "오프라인 사본", "모든 docs 다운로드", "참고용으로 저장(save for reference)" 등.
- **언제 쓰지 않는가**: URL만 발견하면 되는 경우(→ `firecrawl-map`), 개별 페이지(→ `firecrawl-scrape`), 로컬 파일이 아닌 JSON 대량 추출(→ `firecrawl-crawl`).

## 💻 사용 예시 / 명령어
```bash
# 대화형 마법사(포맷/스크린샷/경로 자동 선택)
firecrawl download https://docs.example.com

# 스크린샷 포함
firecrawl download https://docs.example.com --screenshot --limit 20 -y

# 다중 포맷(페이지마다 별도 파일)
firecrawl download https://docs.example.com --format markdown,links --screenshot --limit 20 -y
# 페이지별 생성: index.md + links.txt + screenshot.png

# 특정 섹션 필터
firecrawl download https://docs.example.com --include-paths "/features,/sdks"

# 번역 페이지 제외
firecrawl download https://docs.example.com --exclude-paths "/zh,/ja,/fr,/es,/pt-BR"
```

## 🛠️ 제공 도구 / 주요 파라미터
- 사용 도구: `Bash(firecrawl *)`, `Bash(npx firecrawl *)`

**Download 옵션**

| 옵션 | 설명 |
| --- | --- |
| `--limit <n>` | 최대 다운로드 페이지 수 |
| `--search <query>` | 검색어로 URL 필터 |
| `--include-paths <paths>` | 매칭 경로만 다운로드 |
| `--exclude-paths <paths>` | 매칭 경로 제외 |
| `--allow-subdomains` | 서브도메인 페이지 포함 |
| `-y` | 확인 프롬프트 건너뛰기(자동화 시 항상 사용) |

**Scrape 옵션(모두 download와 호환)**: `-f <formats>`, `-H`, `-S`, `--screenshot`, `--full-page-screenshot`, `--only-main-content`, `--include-tags`, `--exclude-tags`, `--wait-for`, `--max-age`, `--country`, `--languages`

## 📦 출처 / 설치 상태
- 플러그인: firecrawl@claude-plugins-official
- 활성화: ON
- 관련 MCP: firecrawl은 CLI 기반(`firecrawl download`)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/firecrawl/1.0.9/skills/firecrawl-download/SKILL.md`
