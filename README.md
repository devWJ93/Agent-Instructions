<!-- Encoding: UTF-8 (no BOM). 이 저장소의 모든 파일은 UTF-8이다. UTF-8로 읽어라(CP949/EUC-KR로 읽으면 한글이 깨진다). -->
<!--
  이 파일은 사람이 아니라 "처음 이 저장소에 들어온 AI 에이전트"를 위한 오리엔테이션 맵이다.
  사람용 설명서는 index.html, 정식 행동 지침은 AGENTS.md.
-->

# Agent-Instructions

AI 코딩 에이전트에게 일관된 작업 방식, 검증 규칙, 도구 선택 기준을 전달하기 위한 공개용 운영 지침 저장소다.

> **공개 주의**: 이 저장소는 특정 개인 환경에서 정리한 운영 지침·카탈로그 스냅샷이다. 실제 토큰·인증 헤더·개인 설정 파일은 포함하지 않는다.

## Quick Start

다른 AI에게 이 저장소를 적용시키려면 아래처럼 말한다.

```text
이 저장소를 읽고 내 작업 지침으로 적용해줘.
먼저 README.md를 읽고, 그 다음 AGENTS.md와 PORTABILITY.md를 읽어.
Claude 전용 기능은 네 환경의 동등 기능으로 번역하고, 없는 기능은 건너뛰어.
이 저장소는 토큰이나 개인 설정 파일을 포함하지 않는 공개용 운영 지침 카탈로그야.
https://github.com/devWJ93/Agent-Instructions
```

처음 쓰는 사람은 [`GETTING_STARTED.md`](GETTING_STARTED.md)를 먼저 읽는다. Codex 전역 적용 방식은 [`GETTING_STARTED.md`의 Codex 섹션](GETTING_STARTED.md#4-codex에서-헷갈리기-쉬운-부분)을 확인한다. 공개·보안 기준은 [`SECURITY.md`](SECURITY.md)를 따른다. 변경 이력은 [`CHANGELOG.md`](CHANGELOG.md)에 남긴다.

---

## 0. TL;DR (먼저 이것부터)

- **정체**: AI 에이전트 운영 패키지 = **행동 지침**(`AGENTS.md` + `rules/`) + **스킬·MCP 레퍼런스 카탈로그**(`Skill/` + `MCP/`).
- **"적용"의 정의 (중요)**: 이 저장소를 "적용"한다 = **행동 규칙을 로드해 그대로 따른다**. 이 환경을 *설치*하는 게 아니다(설치 절차는 Claude Code 전용 → `references/`·`PORTABILITY.md`). 비-Claude AI라면 **§5.5(범용 코어 vs Claude 전용)**를 먼저 보고, 🟢는 그대로 적용·🟡는 번역·🔴는 건너뛴다.
- **AI가 할 일**: ① `AGENTS.md`로 행동 규칙 로드 → ② 작업 중 스킬/MCP가 필요하면 본 README의 §6 의사결정 가이드와 §7~8 인덱스로 찾기 → ③ 해당 개별 md를 펴서 상세 확인.
- **핵심 원칙**: `AGENTS.md`는 **60줄 이하**(진입점), 세부는 분리해 **필요할 때만** 참조. 운영은 **4대 축**(강제·도구경계·피드백·GC).
- **규모**: 노출 스킬 **97** (+OFF 22), 활성 MCP 서버 **10**(문서 11) (+OFF 4).
- **중요**: 이 카탈로그는 **특정 환경의 스냅샷**이다. 네 환경의 플러그인/MCP 구성이 다르면 목록이 안 맞을 수 있다(§9 참조).

---

## 1. 이 저장소는 무엇인가

AI 에이전트가 일관되게 일하도록 **규칙과 경계를 코드화**하고, **사용 가능한 도구(스킬/MCP)를 사전(辭典)화**한 패키지다. 두 레이어로 나뉜다:

- **지침 레이어** (에이전트가 *따른다*): `AGENTS.md`, `rules/`
- **레퍼런스 레이어** (에이전트가 *필요할 때 펴본다*): `Skill/`, `MCP/`, `references/`

---

## 2. 읽기 순서 (AI 워크플로우)

```
1. README.md (이 파일)      → 전체 그림 파악            [지금]
2. AGENTS.md                → 행동 규칙 로드 (필수)      [매 작업]
3. rules/0X_*.md            → 특정 상황 상세가 필요할 때
4. Skill/<g>/<name>.md      → 특정 스킬 동작이 궁금할 때
5. MCP/<server>.md          → 특정 MCP 도구·파라미터
```

작업 자체의 흐름은 `AGENTS.md`가 강제한다: **① 디스커션(의도 파악, 모호하면 질문) → ② 요구사항 정리(범위·영향파일·승인) → ③ 실행(예상과 다르면 멈추고 1단계로, 땜빵 금지)**.

---

## 3. 디렉토리 맵

```
Agent-Instructions/
├─ README.md              ← (이 파일) AI 오리엔테이션 맵
├─ AGENTS.md              ← 정식 행동 지침 진입점 (60줄, 매 작업 로드)
├─ GETTING_STARTED.md     ← 사람/AI용 빠른 적용 절차
├─ SECURITY.md            ← 공개·보안 기준
├─ CHANGELOG.md           ← 공개 저장소 변경 이력
├─ codex-global-AGENTS.md ← Codex 전역 ~/.codex/AGENTS.md용 지침
├─ LICENSE                ← 저장소 문서 라이선스
├─ index.html             ← 사람용 설명서 (AI는 무시해도 됨)
├─ PORTABILITY.md         ← 다른 AI(비-Claude)에서 스킬/MCP 쓰는 이식 가이드
├─ rules/                 ← 운영 체계 4대 축
│  ├─ 01_enforcement-system.md   검증·테스트·커밋 강제 / "성공은 조용히, 실패만 시끄럽게"
│  ├─ 02_tool-boundaries.md      RAG·서브에이전트·MCP 등록·팀·외부작업·Windows·Unity 도구 경계
│  ├─ 03_feedback-loop.md        리뷰→규칙 보강 반복, 규칙 추가·배치 기준
│  └─ 04_garbage-collection.md   문서-코드 정합·데드코드·MEMORY·Compact 청소
├─ Skill/                 ← 스킬 카탈로그 (스킬 1개당 md 1개)
│  ├─ README.md           스킬 분류 인덱스
│  ├─ _builtin/ (13)      Claude Code 내장 스킬
│  ├─ _personal/ (4)      개인 글로벌 스킬
│  ├─ _disabled/ (22)     OFF 플러그인 카탈로그(켜야 사용)
│  └─ <플러그인>/          활성 플러그인별 폴더
├─ MCP/                   ← MCP 카탈로그
│  ├─ README.md           MCP 인덱스
│  ├─ _disabled/ (4)      OFF MCP
│  ├─ claude-ai-PlayMCP/  PlayMCP 하위 5개 서버
│  └─ <server>.md         서버별 도구 표·연결방식
└─ references/            ← 보조 참조 문서(CLI 셋업 등)
```

---

## 4. 핵심 개념

### 4.1 두 원칙
1. **60줄 진입점 + 세부 분리** — `AGENTS.md`는 짧게. 1,000페이지 설명서는 읽히지 않는다. 세부는 별도 파일로 "필요할 때만".
2. **실수마다 한 줄 누적** — 에이전트가 실패하면 `AGENTS.md` 하단 실패 로그에 재발 방지 한 줄을 추가(피드백 루프).

### 4.2 4대 운영 축 (`rules/`)
| 축 | 파일 | 한 줄 |
|---|---|---|
| 강제 시스템 | `rules/01` | 검증·테스트·커밋 게이트. 통과는 조용히, 실패만 보고 |
| 도구 경계 | `rules/02` | 어떤 도구를 언제·어디까지(권한·경계). 스킬/MCP *선택*은 §6~8 |
| 피드백 루프 | `rules/03` | 코딩→리뷰→규칙 보강→반복 |
| 가비지 컬렉션 | `rules/04` | 문서-코드 정합·데드코드·중복 청소 |

---

## 5. 행동 규칙 요약 (정식 출처: `AGENTS.md`)

> 아래는 빠른 파악용 미러. 충돌 시 `AGENTS.md`·`rules/`·사용자 지시가 우선.

**DO**
- 코드 수정 전 대상 파일을 **Read로 완전히** 읽는다(부분 읽기 금지).
- 탐색·조사는 **서브에이전트(Explore/general-purpose)** 에 위임, **요약만** 회수.
- 정보 조회는 `rag_agent`(rag-search MCP) 우선, 라이브러리 문서는 `context7` MCP 우선.
- 최대 3개 연속 액션 후 결과 확인. 가정은 명시, 불확실하면 질문.
- 코드/설정 수정 후 **실행·빌드로 검증**한 뒤 커밋.

**DON'T (→ 대안)**
- 추측 진행 → **질문**한다.
- 절대 경로 하드코딩(`C:\Users\…`) → `__dirname`·상대경로·환경변수·설정파일.
- 검증 없이 완료/커밋 → 실행·테스트 후.
- 땜빵(딜레이·우회) → 근본 원인 해결, 불가능하면 솔직히 보고.
- `rm -rf` / force push / 시크릿(.env·.pem·.key) 커밋 → 금지. *(이 환경의 Claude Code는 PreToolUse 훅으로 자동 차단하지만, **외부 AI엔 이 안전망이 없으니 수동으로 준수**한다 — 있다고 신뢰하지 말 것.)*

---

## 5.5 적용 범위 — 범용 코어 vs Claude 전용 ⭐ (비-Claude AI 필독)

이 저장소를 적용할 때 **어디서나 되는 것**과 **Claude Code 환경에서만 되는 것**을 구분하라. 외부 AI는 🟢는 그대로, 🟡는 자기 도구로 번역, 🔴는 건너뛰거나 대체한다(용어 번역표·대체 방법 → [`PORTABILITY.md`](PORTABILITY.md)).

| 분류 | 내용 | 외부 AI 처리 |
|---|---|---|
| 🟢 **범용 코어** | 작업 3단계(디스커션·정리·실행), DO/DON'T, 검증 게이트, 디버깅 규율(에러·스택 끝까지 읽기), GC 점검, 출력 규칙. | **그대로 적용** |
| 🟡 **번역 필요** | 서브에이전트 위임(`Task/Explore`→그 AI의 위임), 상황→스킬/MCP 매핑, MCP 등록(`claude mcp add`→자기 `mcp.json`/SDK), 내장 도구명(`Grep/Read`) | 동등물로 치환 |
| 🔴 **Claude 전용 (건너뜀·대체)** | `rag_agent`(rag-search MCP), `Skill` 도구 호출·"Using [skill]", `PreToolUse 훅` 자동차단, `TeamCreate`·`team-monitor` clone(`rules/02 §3`), `MEMORY.md`·`Compact`, `~/.claude/` 설치체계·`claude mcp` CLI·`dotfiles`/`sync.py` 프로토콜(`rules/03 §6`) | 외부 AI엔 **없음** → PORTABILITY로 대체하거나 제외 |

> ⚠️ **규칙 충돌 주의 (외부 AI 필독)**: `rules/02 §6`은 "MCP 등록은 `claude mcp add`만 쓰고 `settings.json`·`.mcp.json` 직접 편집 금지"라 하지만 이는 **Claude Code 한정** 규칙이다. **외부 AI는 정반대로 `mcp.json`/SDK 직접 편집이 정상**이다(→ `PORTABILITY.md` §2.2). 마찬가지로 `rules/02 §3`의 `team-monitor` clone 강제도 외부 AI는 건너뛴다.
>
> **요지**: 행동 규율(🟢)은 어떤 AI든 100% 따를 수 있다. 막히는 건 도구·하네스 종속 레이어(🟡🔴)뿐이며, 그것들은 위 표대로 번역/제외하면 된다. "100% 그대로 복붙 적용"은 안 되지만, **"🟢 적용 + 🟡 번역 + 🔴 제외"로 운용하면 실질 100%**가 된다.

### 용어 빠른 정의 (Claude 전용 — 외부 AI가 모를 수 있음)
- **`rag_agent`** — 이 사용자 환경의 rag-search MCP 도구(코드 RAG 검색). 외부 AI엔 없음 → 자체 검색/임베딩으로 대체.
- **`Task`/`Explore`** — Claude Code의 서브에이전트(탐색 위임) 메커니즘 → 그 AI의 서브에이전트로.
- **`PreToolUse 훅`** — 도구 실행 전 가로채 차단하는 Claude Code settings.json 기능(rm -rf·시크릿 차단). 외부 AI엔 없음 → 수동 준수.
- **`Skill` 도구** — SKILL.md를 트리거·로드하는 Claude Code 기능 → §PORTABILITY 경로 A/B/C.
- **`MEMORY.md`/`Compact`** — 자동 메모리 파일 / 컨텍스트 압축. Claude Code 하네스 기능 → 동등물 있으면 매핑, 없으면 제외.

---

## 6. 의사결정 가이드 (WHEN → WHERE)

| 하려는 것 | 가서 볼 곳 |
|---|---|
| 행동 규칙 확인 | `AGENTS.md` |
| 검증·테스트·커밋 규칙 | `rules/01` |
| "이 도구 써도 되나 / 어디까지" | `rules/02` |
| **어떤 스킬을 쓸지** (상황→스킬) | 본 README **§7** |
| 특정 스킬의 트리거·예시·파라미터 | `Skill/<그룹>/<스킬>.md` |
| **어떤 MCP를 쓸지** / 도구 목록 | 본 README **§8** → `MCP/<server>.md` |
| 규칙을 추가·수정하려면 | `rules/03` (배치 기준 포함) |
| 정리·청소 시점 | `rules/04` |
| 프로젝트 도메인 지식 | `references/` |
| **다른 AI(비-Claude)에서 스킬/MCP를 쓰려면** | `PORTABILITY.md` |

---

## 7. 스킬 카탈로그 전체 인덱스 (노출 97)

> 📋 각 스킬의 **한 줄 설명 목차**는 [`Skill/CATALOG.md`](Skill/CATALOG.md). 아래는 빠른 이름 인덱스다.
>
> 상황별 빠른 매핑: **만들기 전**=`brainstorming`→`writing-plans` · **구현**=`test-driven-development`·`systematic-debugging` · **마무리**=`verify`·`code-review`·`simplify` · **웹**=`firecrawl-*` · **리서치**=`deep-research` · **라이브러리 문서**=`context7`(MCP).

### _builtin (13) — Claude Code 내장
`code-review` · `simplify` · `verify` · `run` · `loop` · `schedule` · `update-config` · `keybindings-help` · `fewer-permission-prompts` · `claude-api` · `init` · `review` · `security-review`

### _personal (4) — 개인 글로벌
`claude-news` · `delete-session` · `handover` · `deep-research`

### superpowers (14) — 개발 워크플로우 방법론
`brainstorming` · `writing-plans` · `test-driven-development` · `systematic-debugging` · `executing-plans` · `subagent-driven-development` · `dispatching-parallel-agents` · `requesting-code-review` · `receiving-code-review` · `finishing-a-development-branch` · `using-git-worktrees` · `verification-before-completion` · `writing-skills` · `using-superpowers`

### huggingface-skills (18) — HF Hub·모델·Spaces
`hf-cli` · `huggingface-best` · `huggingface-community-evals` · `huggingface-datasets` · `huggingface-gradio` · `huggingface-llm-trainer` · `huggingface-local-models` · `huggingface-lora-space-builder` · `huggingface-paper-publisher` · `huggingface-papers` · `huggingface-spaces` · `huggingface-tool-builder` · `huggingface-trackio` · `huggingface-vision-trainer` · `huggingface-zerogpu` · `train-sentence-transformers` · `transformers-js` · `trl-training`

### firecrawl (11) — 웹 스크래핑·검색·크롤
`firecrawl-scrape` · `firecrawl-search` · `firecrawl-crawl` · `firecrawl-map` · `firecrawl-download` · `firecrawl-interact` · `firecrawl-monitor` · `firecrawl-parse` · `firecrawl-agent` · `firecrawl-cli` · `skill-gen`

### figma (9) — 디자인·다이어그램
`figma-use` · `figma-use-figjam` · `figma-use-slides` · `figma-generate-design` · `figma-generate-diagram` · `figma-generate-library` · `figma-create-new-file` · `figma-code-connect` · `figma-generate-project-plan`

### plugin-dev (8) — Claude Code 플러그인 개발
`create-plugin` · `plugin-structure` · `agent-development` · `command-development` · `hook-development` · `mcp-integration` · `plugin-settings` · `skill-development`

### chrome-devtools-mcp (6) — 브라우저 디버깅·성능
`chrome-devtools` · `chrome-devtools-cli` · `a11y-debugging` · `debug-optimize-lcp` · `memory-leak-debugging` · `troubleshooting`

### ralph-loop (3) · claude-md-management (2) · qodo-skills (2)
`ralph-loop` · `cancel-ralph` · `help` ‖ `revise-claude-md` · `claude-md-improver` ‖ `qodo-get-rules` · `qodo-pr-resolver`

### 단일 스킬 플러그인 (7)
`agent-sdk-dev/new-sdk-app` · `pr-review-toolkit/review-pr` · `feature-dev/feature-dev` · `claude-code-setup/claude-automation-recommender` · `skill-creator/skill-creator` · `frontend-design/frontend-design` · `playground/playground`

### ⛔ OFF (`_disabled/`, 22) — 켜야 사용
`vercel`(스킬7+커맨드6) · `hookify`(5) · `neon`(2, ⚠️원문 미vendoring·메타데이터 기반) · `railway`(1) · `semgrep`(1)

> 각 스킬 md는 동일한 5항목: **용도 / 트리거 / 사용예시·명령어 / 제공도구·파라미터 / 출처·설치상태**. 전체 인덱스는 [`Skill/README.md`](Skill/README.md).

---

## 8. MCP 카탈로그 인덱스

### 활성 (ON)
| 서버 | 언제 쓰나 | 비고 |
|---|---|---|
| `notion` | Notion 페이지/DB 읽기·검색 | 수정은 **전체 삭제→재생성**(부분수정 금지) |
| `context7` | 라이브러리/프레임워크 공식 문서 조회 | 라이브러리 질문 시 우선 |
| `playwright` | 브라우저 자동화·E2E·스크린샷 | 23개 `browser_*` 도구 |
| `figma` | Figma 디자인/다이어그램 연동 | figma 스킬과 함께 |
| `huggingface-skills` | HF Hub 인증·작업 | hf 스킬과 함께 |
| `claude.ai PlayMCP` | 네이버검색·사람인·미국주식·DART·카카오 | 하위 5서버: `NaverSearch`·`KakaotalkChat`·`SaraminMcp`·`UsStockInfo`·`opendart` |

### ⛔ OFF (`MCP/_disabled/`, 4)
`vercel` · `railway` · `neon` · `semgrep` (해당 플러그인 OFF)

> 서버별 도구 표·연결방식·파라미터는 [`MCP/README.md`](MCP/README.md) 및 `MCP/<server>.md`.

---

## 9. 메타 · 정합성 · 알려진 한계

- **숫자**: 노출 스킬 97 + OFF 22 = 카탈로그 119 md. MCP ON 11 md(서버 10) + OFF 4.
- **비노출 ON 플러그인(의도된 제외, 누락 아님)**: `atomic-agents`, LSP 4종(`rust-analyzer`·`typescript`·`pyright`·`csharp`). ON이지만 스킬/MCP로 노출 안 됨 → 카탈로그에 없는 게 정상.
- **동명 주의**: 빌트인 `simplify`·`code-review`는 플러그인 `code-simplifier`·`pr-review-toolkit`(agent/command 제공)과 **별개**.
- **카탈로그 = 스냅샷**: 스킬/MCP 목록은 작성 환경 기준. 다른 환경에선 다를 수 있다 → 변경 시 `rules/04`(GC)에 따라 `Skill/`·`MCP/`와 README 숫자를 함께 갱신.
- **figma 9 vs 노출 8**: 카탈로그는 figma 9개(`generate-project-plan` 포함)다. 단 `generate-project-plan`은 캐시 `workflow-skills/` 소속이라 환경에 따라 **세션 노출이 8개일 수 있다**(카탈로그 자체는 자기일관적).
- **neon 문서 성격**: `_disabled/neon/` 2개는 원본 `SKILL.md`가 깨진 심볼릭 링크라 **원문 대신 매니페스트 메타데이터로** 작성됨(문서 자체는 완결, "파일 없음"이 아님). 원문은 `github.com/neondatabase/agent-skills`에서 받는다.
- **설치 가이드의 외부 전제**: `references/claude-code-cli-setup.md`의 설치 절차는 형제 저장소(`claude-code-cli-settings`)의 `dotfiles/`·`mcp-servers/`를 전제하며 **이 저장소엔 그 실물이 없다**. 또 Claude Code 전용이라 비-Claude AI엔 적용 불가(§5.5의 🔴).
- **출처 근거**: 각 스킬/MCP 문서는 플러그인 캐시의 실제 `SKILL.md`·`.mcp.json`을 근거로 작성됨(추측 아님). 캐시 절대경로 표기는 작성 환경 기준.

---

*기준일 2026-06-09 · 진입점=`AGENTS.md` · 세부=`rules/` · 카탈로그=`Skill/`·`MCP/` · 사람용=`index.html`*
