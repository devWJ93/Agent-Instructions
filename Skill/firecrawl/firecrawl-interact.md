# firecrawl-interact

> 스크래프한 페이지의 라이브 브라우저 세션을 자연어/코드로 제어하는 스킬 — 클릭, 폼 입력, 내비게이션, 로그인.

## 📌 용도
스크래프한 페이지를 라이브 브라우저 세션에서 조작한다. 먼저 페이지를 스크래프한 뒤, 자연어 프롬프트 또는 코드로 클릭·폼 입력·내비게이션·데이터 추출을 수행한다.

- 클릭, 폼 입력, 페이지네이션, 로그인 등 상호작용이 필요한 콘텐츠
- JavaScript 상호작용 뒤에 콘텐츠가 있어 `scrape`가 실패한 경우
- 다단계 플로우(체크아웃, 위저드 등) 내비게이션
- 워크플로우 에스컬레이션 패턴의 최후 수단: search → scrape → map → crawl → **interact**
- 웹 검색에는 절대 interact 사용 금지 — `search` 사용

## 🎯 트리거 조건
- **언제 호출되는가**: "interact", "click", "fill out the form", "log in to", "sign in", "submit", "paginated", "next page", "infinite scroll", "navigate to", "open a session", "scrape failed". 인증 스크래핑(프로필) 포함.
- **언제 쓰지 않는가**: 단순 정적/SPA 페이지(먼저 `scrape` 시도), 웹 검색(→ `firecrawl-search`), AI 자율 구조화 추출(→ `firecrawl-agent`).

## 💻 사용 예시 / 명령어
```bash
# 1) 페이지 스크래프(scrape ID 자동 저장)
firecrawl scrape "<url>"

# 2) 자연어로 상호작용
firecrawl interact --prompt "Click the login button"
firecrawl interact --prompt "Fill in the email field with test@example.com"
firecrawl interact --prompt "Extract the pricing table"

# 3) 코드로 정밀 제어
firecrawl interact --code "agent-browser click @e5" --language bash
firecrawl interact --code "agent-browser snapshot -i" --language bash

# 4) 작업 종료 시 세션 정리
firecrawl interact stop
```

**프로필(인증 상태 유지)**:
```bash
# 세션1: 로그인 후 상태 저장
firecrawl scrape "https://app.example.com/login" --profile my-app
firecrawl interact --prompt "Fill in email with user@example.com and click login"

# 세션2: 인증된 상태로 재접속
firecrawl scrape "https://app.example.com/dashboard" --profile my-app
firecrawl interact --prompt "Extract the dashboard data"

# 읽기 전용 재접속(프로필 상태 미변경)
firecrawl scrape "https://app.example.com" --profile my-app --no-save-changes
```

## 🛠️ 제공 도구 / 주요 파라미터
- 사용 도구: `Bash(firecrawl *)`, `Bash(npx firecrawl *)`

| 옵션 | 설명 |
| --- | --- |
| `--prompt <text>` | 자연어 지시(이것 OR `--code`) |
| `--code <code>` | 브라우저 세션에서 실행할 코드 |
| `--language <lang>` | 코드 언어: bash, python, node |
| `--timeout <seconds>` | 실행 타임아웃(기본 30, 최대 300) |
| `--scrape-id <id>` | 특정 스크래프 대상 지정(기본: 마지막 스크래프) |
| `-o, --output <path>` | 출력 파일 경로 |

**팁**: 반드시 먼저 `scrape` — interact는 이전 스크래프의 scrape ID가 필요. scrape ID는 자동 저장되어 이후 interact에는 `--scrape-id` 불필요. 작업 끝나면 `firecrawl interact stop`으로 리소스 해제. 병렬 작업 시 여러 페이지를 스크래프 후 각 `--scrape-id`로 상호작용.

## 📦 출처 / 설치 상태
- 플러그인: firecrawl@claude-plugins-official
- 활성화: ON
- 관련 MCP: firecrawl은 CLI 기반(`firecrawl interact`, 라이브 브라우저 세션)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/firecrawl/1.0.9/skills/firecrawl-interact/SKILL.md`
