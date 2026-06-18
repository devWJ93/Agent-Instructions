# 스킬 인덱스

현재 세션에서 호출 가능한(노출된) 최상위 스킬 **97개**를 출처별로 정리했습니다.
※ 플러그인 내부 reference 하위 스킬(chrome-devtools 65개, huggingface 68개 등 SKILL.md)은 세션에서 직접 참고하는 단위가 아니므로 제외했습니다.

> 📋 **각 스킬의 한 줄 설명 목차**는 [`CATALOG.md`](CATALOG.md) 참조(노출 97 + OFF 22, 스킬별 요약). 이 README는 분류·수치·출처를 다룬다.

## 분류

### 🔧 빌트인 스킬 (`_builtin/`, 13개)
Claude Code 내장 스킬. 별도 파일 없이 하네스에 내장.

| 스킬 | 한 줄 | 스킬 | 한 줄 |
|---|---|---|---|
| [code-review](_builtin/code-review.md) | diff 버그/품질 리뷰 | [simplify](_builtin/simplify.md) | 변경 코드 정리 |
| [verify](_builtin/verify.md) | 앱 실행해 동작 검증 | [run](_builtin/run.md) | 프로젝트 앱 실행 |
| [loop](_builtin/loop.md) | 주기적 반복 실행 | [schedule](_builtin/schedule.md) | cron 클라우드 에이전트 |
| [update-config](_builtin/update-config.md) | settings.json 설정 | [keybindings-help](_builtin/keybindings-help.md) | 단축키 커스터마이즈 |
| [fewer-permission-prompts](_builtin/fewer-permission-prompts.md) | allowlist 자동 추가 | [claude-api](_builtin/claude-api.md) | Claude API 레퍼런스 |
| [init](_builtin/init.md) | CLAUDE.md 초기화 | [review](_builtin/review.md) | PR 리뷰 |
| [security-review](_builtin/security-review.md) | 보안 취약점 리뷰 | | |

### 👤 개인 글로벌 스킬 (`_personal/`, 4개)
`~/.claude/skills/` 또는 하네스 등록형 개인 스킬.

| 스킬 | 한 줄 |
|---|---|
| [claude-news](_personal/claude-news.md) | Claude Code/Anthropic 소식 한국어 요약 |
| [delete-session](_personal/delete-session.md) | 세션 jsonl 삭제 |
| [handover](_personal/handover.md) | 세션 인수인계 md 작성 |
| [deep-research](_personal/deep-research.md) | 멀티소스 교차검증 리서치 리포트 |

### 🧩 플러그인 스킬

| 플러그인 | 스킬 수 | 폴더 | 주제 |
|---|---|---|---|
| [superpowers](superpowers/) | 14 | `superpowers/` | 개발 워크플로우(브레인스토밍·TDD·디버깅·플랜) |
| [huggingface-skills](huggingface-skills/) | 18 | `huggingface-skills/` | HF Hub·모델학습·Spaces·데이터셋 |
| [firecrawl](firecrawl/) | 11 | `firecrawl/` | 웹 스크래핑·검색·크롤·모니터 |
| [figma](figma/) | 9 | `figma/` | Figma 디자인·다이어그램·코드커넥트·프로젝트플랜 |
| [plugin-dev](plugin-dev/) | 8 | `plugin-dev/` | Claude Code 플러그인 개발 |
| [chrome-devtools-mcp](chrome-devtools-mcp/) | 6 | `chrome-devtools-mcp/` | 브라우저 디버깅·성능·a11y |
| [ralph-loop](ralph-loop/) | 3 | `ralph-loop/` | Ralph Loop 자동 반복 |
| [claude-md-management](claude-md-management/) | 2 | `claude-md-management/` | CLAUDE.md 관리 |
| [qodo-skills](qodo-skills/) | 2 | `qodo-skills/` | Qodo 코딩 룰·PR 리졸버 |
| [agent-sdk-dev](agent-sdk-dev/) | 1 | `agent-sdk-dev/` | Agent SDK 앱 생성 |
| [pr-review-toolkit](pr-review-toolkit/) | 1 | `pr-review-toolkit/` | PR 종합 리뷰 |
| [feature-dev](feature-dev/) | 1 | `feature-dev/` | 기능 개발 가이드 |
| [claude-code-setup](claude-code-setup/) | 1 | `claude-code-setup/` | 자동화 추천 |
| [skill-creator](skill-creator/) | 1 | `skill-creator/` | 스킬 생성·최적화 |
| [frontend-design](frontend-design/) | 1 | `frontend-design/` | 프런트엔드 디자인 |
| [playground](playground/) | 1 | `playground/` | 인터랙티브 HTML 플레이그라운드 |

### ⛔ 비활성(OFF) 플러그인 스킬 (`_disabled/`, 22개)
현재 `settings.json`에서 `enabledPlugins = false`라 세션에 노출되지 않음. 켜면 사용 가능 — "켜면 무엇이 추가되는가" 카탈로그.

| 플러그인 | 구성 | 폴더 | 주제 |
|---|---|---|---|
| [vercel](_disabled/vercel/) | 스킬 7 + 커맨드 6 | `_disabled/vercel/` | 배포 커맨드(deploy·env·status…)는 사용자용 / benchmark·release·audit은 플러그인 내부 개발용 |
| [hookify](_disabled/hookify/) | 스킬 1 + 커맨드 4 | `_disabled/hookify/` | 자연어 규칙→훅 자동 생성 |
| [neon](_disabled/neon/) | 스킬 2 | `_disabled/neon/` | Neon Postgres·이그레스 최적화 ※원문 미vendoring → 메타데이터 기반 작성(문서는 완결) |
| [railway](_disabled/railway/) | 스킬 1 | `_disabled/railway/` | Railway 배포·DB 운영 |
| [semgrep](_disabled/semgrep/) | 커맨드 1 | `_disabled/semgrep/` | Semgrep 보안 스캔 설치(스킬 없음, MCP 제공) |

> ⚠️ **neon** 두 스킬은 캐시의 SKILL.md가 깨진 심볼릭 링크라 본문이 없어 매니페스트 메타데이터로만 작성됨. 본문 필요 시 `github.com/neondatabase/agent-skills` fetch 또는 플러그인 재설치 필요.
> ⚠️ **vercel** `_conventions.md`는 실제 슬래시 커맨드가 아닌 메타 문서(언더스코어 접두).

## ℹ️ 비노출 ON 플러그인 (의도된 제외)

다음은 `enabledPlugins`에서 **ON이지만** 세션에 스킬/MCP로 노출되지 않아 이 카탈로그에서 제외됩니다. **누락이 아니라 의도된 제외**입니다.
- **LSP 4종** (`rust-analyzer`·`typescript`·`pyright`·`csharp`-lsp): 에디터 LSP 기능만 제공, 스킬/MCP 비노출.
- **atomic-agents**: 세션에 노출되는 스킬/MCP 컴포넌트 없음.
- **code-simplifier**·**pr-review-toolkit**: 에이전트/커맨드를 제공(스킬 비노출). 동명 빌트인 `simplify`·`code-review`와는 별개 → 각 `_builtin/` 문서 참고.

## 📦 출처·설치 매핑 (다른 AI로 이식 시 — 경로 A)

이 저장소는 스킬 **실물을 담지 않는다**(재배포 회피). 다른 AI에서 쓰려면 아래 **공개 원본에서 각자 설치**한다. 자세한 절차는 [`../PORTABILITY.md`](../PORTABILITY.md) §3 참조.

### 외부 서드파티 repo (라이선스 명시 — 이식 가장 안전)
| 플러그인 | 공개 원본 | License |
|---|---|---|
| `superpowers` | `github.com/obra/superpowers` | MIT |
| `huggingface-skills` | `github.com/huggingface/skills` | Apache-2.0 |
| `qodo-skills` | `github.com/qodo-ai/qodo-skills` | MIT |
| `vercel` (OFF) | `github.com/vercel/vercel-plugin` | Apache-2.0 |
| `railway` (OFF) | `github.com/railwayapp/railway-skills` | MIT |
| `neon` (OFF) | `github.com/neondatabase/agent-skills` | Apache-2.0 |
| `atomic-agents`* | `github.com/BrainBlend-AI/atomic-agents` | MIT |

\* atomic-agents는 세션 비노출(카탈로그 외)이나 출처 참고용 기재.

### 마켓플레이스 번들 (plugin.json에 repo·license 미기재 ⚠️)
아래는 공개 repo URL이 매니페스트에 없어, 마켓플레이스 **`github.com/anthropics/claude-plugins-official`** 에서 함께 배포되는 구조다.
- **Anthropic 제작(12)**: `plugin-dev`·`ralph-loop`·`claude-md-management`·`agent-sdk-dev`·`pr-review-toolkit`·`feature-dev`·`claude-code-setup`·`skill-creator`·`frontend-design`·`playground`·`code-simplifier`·`hookify`
- **외부사 번들(5)**: `firecrawl`(Firecrawl)·`figma`(Figma)·`context7`(Upstash)·`playwright`(Microsoft)·`semgrep`(Semgrep)
- `chrome-devtools-mcp`: npm 패키지 `chrome-devtools-mcp`(Google Chrome팀)를 npx로 실행하는 래퍼

> ⚠️ **License 미기재 주의**: 위 번들 항목은 plugin.json에 license 필드가 없다. 외부사 제작분(Figma·Microsoft·Upstash·Semgrep·Firecrawl)을 다른 곳에 재배포·이식할 때는 **각 제작사 약관을 별도 확인**할 것. 안전한 길은 "재배포하지 말고 마켓플레이스에서 각자 설치"다.

### 설치 방법
- **받는 AI가 Claude Code**: `/plugin marketplace add anthropics/claude-plugins-official` → 플러그인 설치(이 저장소 불필요, 원본에서 직접).
- **받는 AI가 Agent Skills 지원**: 위 원본 repo에서 해당 스킬의 **`SKILL.md` 디렉토리만** 추출해 배치(플러그인의 커맨드·훅 등 Claude 전용 부분 제외).
- **미지원 AI**: [`../PORTABILITY.md`](../PORTABILITY.md) 경로 B(참조 활용)·C(MCP 래핑).

## 각 문서 구성

모든 스킬 md는 다음 5개 항목을 포함합니다:
1. **📌 용도** — 무엇을 하는 스킬인지
2. **🎯 트리거 조건** — 언제 호출되는가 / 언제 안 쓰는가 / (해당 시) Rigid·Flexible
3. **💻 사용 예시 / 명령어** — 실제 호출·슬래시 명령·인자
4. **🛠️ 제공 도구 / 주요 파라미터** — 도구·절차·참조파일
5. **📦 출처 / 설치 상태** — 플러그인·활성화·캐시 경로
