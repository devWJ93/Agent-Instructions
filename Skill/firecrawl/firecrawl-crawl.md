# firecrawl-crawl

> 웹사이트 전체 또는 섹션의 여러 페이지를 링크를 따라가며 대량 추출하는 스킬.

## 📌 용도
링크를 따라가며 깊이/개수 제한 안에서 사이트의 여러 페이지 내용을 한꺼번에 추출한다. 경로 필터링과 동시 추출을 지원한다.

- 사이트의 많은 페이지가 필요할 때(예: 모든 `/docs/`)
- 사이트 섹션 전체를 추출하고 싶을 때
- 워크플로우 에스컬레이션 패턴 4단계: search → scrape → map → **crawl** → interact

## 🎯 트리거 조건
- **언제 호출되는가**: "crawl", "모든 페이지 가져와", "/docs 아래 전부 추출", "대량 추출(bulk extract)", 같은 사이트의 여러 페이지 콘텐츠가 필요할 때.
- **언제 쓰지 않는가**: 단일 페이지(→ `firecrawl-scrape`), 로컬 파일로 저장하고 싶을 때(→ `firecrawl-download`), 크롤 전 URL 목록만 보고 싶을 때(→ `firecrawl-map`), AI 자율 구조화 추출(→ `firecrawl-agent`).

## 💻 사용 예시 / 명령어
```bash
# docs 섹션 크롤
firecrawl crawl "<url>" --include-paths /docs --limit 50 --wait -o .firecrawl/crawl.json

# 깊이 제한 전체 크롤 + 진행 표시
firecrawl crawl "<url>" --max-depth 3 --wait --progress -o .firecrawl/crawl.json

# 실행 중 크롤 상태 확인
firecrawl crawl <job-id>
```

## 🛠️ 제공 도구 / 주요 파라미터
- 사용 도구: `Bash(firecrawl *)`, `Bash(npx firecrawl *)`

| 옵션 | 설명 |
| --- | --- |
| `--wait` | 완료까지 대기 후 반환 |
| `--progress` | 대기 중 진행 표시 |
| `--limit <n>` | 최대 크롤 페이지 수 |
| `--max-depth <n>` | 따라갈 최대 링크 깊이 |
| `--include-paths <paths>` | 해당 경로 매칭 URL만 크롤 |
| `--exclude-paths <paths>` | 해당 경로 매칭 URL 제외 |
| `--delay <ms>` | 요청 간 지연 |
| `--max-concurrency <n>` | 최대 병렬 크롤 워커 |
| `--pretty` | JSON 보기 좋게 출력 |
| `-o, --output <path>` | 출력 파일 경로 |

**팁**: 결과가 즉시 필요하면 항상 `--wait`(없으면 비동기 폴링용 job ID). `--include-paths`로 범위를 좁혀 전체 사이트를 무분별하게 크롤하지 말 것. 크롤은 페이지당 크레딧을 소모하므로 대규모 크롤 전 `firecrawl credit-usage` 확인.

## 📦 출처 / 설치 상태
- 플러그인: firecrawl@claude-plugins-official
- 활성화: ON
- 관련 MCP: firecrawl은 CLI 기반(`firecrawl crawl`)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/firecrawl/1.0.9/skills/firecrawl-crawl/SKILL.md`
