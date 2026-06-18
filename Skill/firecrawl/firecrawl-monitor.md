# firecrawl-monitor

> 웹사이트 콘텐츠 변경을 감지해 웹훅/이메일로 알려주는 스킬 — cron·스크래퍼·diff 스크립트 불필요.

## 📌 용도
웹사이트 콘텐츠가 변경되면 감지해 웹훅 또는 이메일로 통지한다. 각 체크의 페이지마다 `same`/`new`/`changed`/`removed`/`error` 라벨이 붙고, 스냅샷 이력과 구조화된 필드별 diff를 제공해 다운스트림 도구에 바로 연동할 수 있다. AI judge가 포맷·타임스탬프·트래킹 파라미터 노이즈를 걸러 실제 콘텐츠 변경에만 알림을 발생시킨다.

**모니터를 쓰는 이유**
- **변경 감지 서비스화** — 페치·diff·판정·통지를 모두 서버 측에서 처리. cron/diff 라이브러리/SMTP/스냅샷 DB 불필요.
- **알림 우선** — 웹훅(`monitor.page`, `monitor.check.completed`)과 이메일 요약. 외부 수신자는 옵트인 확인 필요.
- **AI 노이즈 필터(`--goal`)** — 평문 목표를 주면 judge가 포맷/공백/케이스/세션ID/캐시버스터/트래킹 파라미터 등을 무시.
- **구조화 필드별 diff** — JSON 모드 시 `plans[0].price: "$19/mo" → "$24/mo"` 형태.
- **단순 페이지 상태 모델**, **인프라 없는 스냅샷 이력(`--retention-days`)**, **다중 페이지 감시**, **스케줄 글루 불필요**(자연어 스케줄 지원).

## 🎯 트리거 조건
- **언제 호출되는가**: "monitor", "watch", "track", "alert me when", "notify when X changes", "ping me if", "email me when", "send a webhook when". 경쟁사 가격 감시, 신규 채용/블로그 알림, docs/changelog/status 페이지 모니터링. 같은 URL을 두 번 이상 점검해야 하면 일회성 반복 스크래프 대신 권장.
- **언제 쓰지 않는가**: 페이지를 한 번만 읽으면 되는 경우(→ `firecrawl-scrape`). zero-data-retention 팀은 모니터링 불가.

## 💻 사용 예시 / 명령어
```bash
# 단일 페이지, 자연어 스케줄, 이메일 알림
firecrawl monitor create --name "Blog" --schedule "every 30 minutes" \
  --goal "Alert when a new blog post is published." \
  --page https://example.com/blog --email alerts@example.com

# 여러 페이지 한 모니터로
firecrawl monitor create --name "Product pages" --schedule "every 30 minutes" \
  --goal "Alert when pricing, docs, or changelog content changes." \
  --scrape-urls https://example.com/pricing,https://example.com/docs

# 사이트 크롤 기반(발견된 모든 페이지 diff)
firecrawl monitor create --name "Docs site" --schedule "hourly" \
  --goal "Alert when any docs page is added, removed, or substantively changed." \
  --crawl-url https://docs.example.com

# 관리/검사
firecrawl monitor list --limit 20
firecrawl monitor run <monitorId>          # 즉시 체크 실행
firecrawl monitor checks <monitorId>
firecrawl monitor check <monitorId> <checkId> --page-status changed
firecrawl monitor update <monitorId> --state paused
firecrawl monitor delete <monitorId>
```
서브커맨드: `create | list | get | update | delete | run | checks | check`

## 🛠️ 제공 도구 / 주요 파라미터
- 사용 도구: `Bash(firecrawl *)`, `Bash(npx firecrawl *)`

| 옵션 | 설명 |
| --- | --- |
| `--name <name>` | 모니터 이름(create 시 필수) |
| `--goal <text>` | 평문 변경 목표(AI judge 자동 활성) |
| `--schedule <text>` / `--cron <expr>` | 자연어/cron 스케줄(최소 15분) |
| `--timezone <tz>` | 스케줄 타임존(기본 UTC) |
| `--page <url>` / `--scrape-urls <list>` / `--crawl-url <url>` | 대상 페이지/URL 목록/크롤 루트 |
| `--webhook-url <url>` / `--webhook-events <list>` | 웹훅 대상/이벤트(`monitor.page`, `monitor.check.completed`) |
| `--email <list>` | 이메일 수신자(콤마 구분) |
| `--retention-days <n>` | 스냅샷 보존 기간 |
| `--state <state>` | `active`/`paused` (update 전용, `--status` 아님) |
| `--page-status <state>` | check 결과 필터(`same`/`new`/`changed`/`removed`/`error`) |

**핵심 주의**: 일시 중지는 `delete`가 아니라 `--state paused`(update). check 필터는 `--page-status`(전역 `--status`와 충돌 회피). 모니터 트리거 스크래프는 `maxAge`가 기본 0(매 체크 신선 스크래프). JSON-mode change tracking은 CLI 플래그로 불가 — JSON 페이로드(파일/stdin)로 `changeTracking` + `modes:["json"]` + 스키마 전달, `["json","git-diff"]`는 혼합 모드.

## 📦 출처 / 설치 상태
- 플러그인: firecrawl@claude-plugins-official
- 활성화: ON
- 관련 MCP: firecrawl은 CLI 기반(`firecrawl monitor`)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/firecrawl/1.0.9/skills/firecrawl-monitor/SKILL.md`
