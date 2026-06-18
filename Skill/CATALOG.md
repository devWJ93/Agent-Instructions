# 스킬 카탈로그 — 한 줄 목차

> 각 스킬의 한 줄 요약 모음. 상세는 각 `<그룹>/<스킬>.md`, 분류·수치·출처는 [`README.md`](README.md).
> 형식: `- **스킬명** — 요약`

---

## 노출 스킬 (97)

### _builtin (13)
- **claude-api** — Claude API / Anthropic SDK 레퍼런스 빌트인 스킬. model id·가격·파라미터·스트리밍·tool use·MCP·캐싱·토큰 카운팅·모델 마이그레이션을 다룬다. **Claude/Anthropic 관련 작업 시 파일을 열기 전에 먼저 읽는 트리거 스킬**.
- **code-review** — 현재 diff를 **정확성(버그) + 재사용/단순화/효율성** 관점에서 리뷰하는 빌트인 스킬. effort 레벨로 깊이를 조절하고, PR 코멘트나 워킹트리 적용까지 가능.
- **fewer-permission-prompts** — 트랜스크립트에서 자주 쓴 **read-only Bash·MCP 호출**을 스캔해, 프로젝트 `settings.json`에 우선순위 allowlist를 추가함으로써 권한 프롬프트를 줄이는 빌트인 스킬.
- **init** — 코드베이스를 분석해 **CLAUDE.md를 생성/초기화**하는 빌트인 명령.
- **keybindings-help** — `~/.claude/keybindings.json`을 편집해 Claude Code의 키보드 단축키를 커스터마이즈하는 빌트인 스킬.
- **loop** — 프롬프트나 슬래시 명령을 **주기적으로 반복 실행**하는 빌트인 스킬. 인터벌을 주면 그 간격으로, 생략하면 모델이 스스로 페이스를 정해 반복한다.
- **review** — PR / 코드 리뷰를 수행하는 빌트인 명령.
- **run** — 프로젝트 앱을 **실제로 띄워서** 변경이 동작하는지 확인하는 빌트인 스킬. 프로젝트 타입(CLI/server/TUI/Electron/browser/library)별 구동 패턴을 안다.
- **schedule** — **cron 스케줄로 도는 클라우드 에이전트(routines)**를 생성·수정·목록·실행하는 빌트인 스킬. 1회성 예약 실행도 지원.
- **security-review** — **보안 취약점 관점**에서 코드를 리뷰하는 빌트인 명령.
- **simplify** — 변경된 코드를 **재사용·단순화·효율성·altitude(추상화 수준)** 관점에서 정리하고 그 수정을 직접 적용하는 빌트인 스킬. **품질 전용**이며 버그는 찾지 않는다.
- **update-config** — `settings.json` / `settings.local.json`을 직접 편집해 Claude Code 하네스(harness)를 설정하는 빌트인 스킬. 권한·환경변수·hook·자동 동작을 다룬다.
- **verify** — 코드 변경이 **실제로 의도대로 동작하는지** 앱을 직접 실행해 동작을 관찰하며 검증하는 빌트인 스킬.

### _personal (4)
- **claude-news** — Claude Code 릴리스 노트 + Anthropic 모델/제품 소식을 **한국어로 요약**하는 개인 글로벌 스킬. 새 기능과 주요 변경을 카테고리별로 정리해 보여준다.
- **deep-research** — 멀티소스 웹 검색 + 소스 fetch + **교차검증(adversarial verify)** + 인용 포함 리포트 합성을 수행하는 개인 글로벌 딥리서치 스킬.
- **delete-session** — 현재 프로젝트의 **세션 목록을 보여주고**, 선택한 세션의 `jsonl` 파일을 삭제해 resume 리스트에서 제거하는 개인 글로벌 스킬.
- **handover** — 현재 세션 전체 내용을 **다음 세션 인수인계용 md 파일**로 정리·저장하는 개인 글로벌 스킬.

### superpowers (14)
- **brainstorming** — 모든 창작 작업(기능/컴포넌트/동작 추가·변경) 전에 반드시 거쳐야 하는, 아이디어를 검증된 설계·스펙으로 발전시키는 대화형 디스커션 스킬.
- **dispatching-parallel-agents** — 서로 의존하지 않는 2개 이상의 독립 작업을, 격리된 컨텍스트를 가진 전용 에이전트에게 병렬로 위임하는 스킬.
- **executing-plans** — 작성된 구현 계획을, 별도 세션에서 리뷰 체크포인트를 두며 태스크 단위로 실행하는 스킬.
- **finishing-a-development-branch** — 구현이 끝나고 테스트가 통과한 뒤, 작업을 어떻게 통합할지(머지/PR/유지/폐기) 구조화된 선택지로 안내하고 워크스페이스를 정리하는 스킬.
- **receiving-code-review** — 코드 리뷰 피드백을 받을 때, 형식적 동의나 맹목적 구현 대신 기술적 검증과 엄밀함으로 평가·대응하는 스킬.
- **requesting-code-review** — 코드 리뷰어 서브에이전트를 디스패치해, 문제가 연쇄되기 전에 작업 산출물을 검토받는 스킬.
- **subagent-driven-development** — 구현 계획을 같은 세션에서 실행하되, 태스크마다 새 서브에이전트를 디스패치하고 2단계 리뷰(스펙 준수 → 코드 품질)를 거치는 스킬.
- **systematic-debugging** — 어떤 버그·테스트 실패·예상 밖 동작이든, 수정안을 내기 전에 반드시 근본 원인을 먼저 찾는 4단계 체계적 디버깅 스킬.
- **test-driven-development** — 어떤 기능·버그픽스든 구현 코드 작성 전에 실패하는 테스트를 먼저 쓰고, 실패를 직접 확인한 뒤 최소 코드로 통과시키는 TDD 스킬.
- **using-git-worktrees** — 작업을 격리된 워크스페이스에서 진행하도록, 기존 격리를 먼저 감지하고 네이티브 도구를 우선하며 git worktree는 폴백으로만 쓰는 스킬.
- **using-superpowers** — 모든 대화 시작 시, 어떤 응답(명확화 질문 포함)보다 먼저 관련 스킬을 찾아 Skill 도구로 호출하도록 규율을 세우는 메타 스킬.
- **verification-before-completion** — 작업이 완료·수정·통과됐다고 주장하기 전에, 반드시 검증 명령을 실행하고 그 출력을 확인하라는 스킬 — 주장보다 증거가 항상 먼저.
- **writing-plans** — 스펙·요구사항을 받아, 코드를 건드리기 전에 잘게 쪼갠 태스크 단위의 완전한 구현 계획을 작성하는 스킬.
- **writing-skills** — 새 스킬을 만들거나 기존 스킬을 수정·검증할 때, 프로세스 문서화에 TDD(RED-GREEN-REFACTOR)를 적용하는 스킬.

### huggingface-skills (18)
- **hf-cli** — Hugging Face Hub의 공식 CLI 도구 `hf`로 모델·데이터셋·스페이스·버킷·repo·논문·잡(Jobs) 등을 다운로드/업로드/관리하는 스킬.
- **huggingface-best** — 특정 작업에 가장 좋은/추천 AI 모델을 HF 공식 벤치마크 리더보드로 찾아 비교표로 제시하는 스킬.
- **huggingface-community-evals** — 로컬 하드웨어에서 inspect-ai와 lighteval로 HF Hub 모델 평가(eval)를 실행하는 스킬.
- **huggingface-datasets** — Hugging Face Dataset Viewer API로 데이터셋 메타데이터·행 페이지네이션·검색·필터·parquet URL을 읽는 스킬.
- **huggingface-gradio** — Python으로 Gradio 웹 UI와 ML 데모를 만드는 스킬 (Interface/Blocks/ChatInterface, 컴포넌트, 이벤트 리스너).
- **huggingface-llm-trainer** — TRL/Unsloth와 Hugging Face Jobs 클라우드 GPU로 언어·비전 모델을 학습/파인튜닝하는 스킬 (SFT/DPO/GRPO/Reward + GGUF 변환).
- **huggingface-local-models** — llama.cpp + GGUF로 CPU/Mac Metal/CUDA/ROCm에서 모델을 로컬 실행하기 위한 모델 선택·실행 스킬.
- **huggingface-lora-space-builder** — 사용자가 제공한 LoRA를 위한 Gradio 데모를 Hugging Face Spaces(ZeroGPU, 기본 private)에 빌드·발행하는 스킬.
- **huggingface-paper-publisher** — Hugging Face Hub에 연구 논문을 발행·관리하는 스킬 (페이지 생성, 모델/데이터셋 연결, 저자권 주장, 연구 아티클 생성).
- **huggingface-papers** — HF paper page를 마크다운으로 읽고, papers API로 저자·연결된 모델/데이터셋/스페이스 등 구조화 메타데이터를 조회하는 스킬.
- **huggingface-spaces** — Hugging Face Spaces 앱(Gradio/Docker/Static, ZeroGPU·전용 하드웨어)을 빌드·배포·디버깅·유지보수하는 스킬.
- **huggingface-tool-builder** — Hugging Face API 데이터를 fetch·가공·결합하는 재사용 가능한 CLI 스크립트/유틸리티를 만드는 스킬.
- **huggingface-trackio** — Trackio로 ML 학습 실험의 메트릭을 로깅·시각화하고, 알림 발화·메트릭 조회까지 다루는 스킬.
- **huggingface-vision-trainer** — HF Transformers + HF Jobs 클라우드 GPU로 객체탐지·이미지분류·SAM/SAM2 분할 비전 모델을 학습/파인튜닝하는 스킬.
- **huggingface-zerogpu** — Gradio Spaces의 ZeroGPU 하드웨어 코드 제약(`@spaces.GPU`, 프로세스 격리, duration/quota, CUDA 빌드)을 다루는 스킬.
- **train-sentence-transformers** — sentence-transformers 모델(SentenceTransformer/CrossEncoder/SparseEncoder)을 학습/파인튜닝하는 라우터형 스킬.
- **transformers-js** — Transformers.js로 최신 ML 모델을 JavaScript/TypeScript에서 직접 실행하는 스킬 (브라우저·Node.js·Bun·Deno, WebGPU/WASM).
- **trl-training** — TRL(Transformers Reinforcement Learning) CLI 명령으로 LM을 학습/파인튜닝하는 스킬 (SFT/DPO/GRPO/KTO/RLOO/Reward).

### firecrawl (11)
- **firecrawl-agent** — AI가 복잡한 사이트를 자율 탐색해 구조화된 JSON 데이터를 추출하는 스킬.
- **firecrawl-cli** — Firecrawl CLI로 웹을 검색·스크래핑·상호작용하는 최상위 워크플로우 스킬. LLM에 최적화된 클린 마크다운 반환.
- **firecrawl-crawl** — 웹사이트 전체 또는 섹션의 여러 페이지를 링크를 따라가며 대량 추출하는 스킬.
- **firecrawl-download** — 웹사이트 전체를 로컬 파일(마크다운/스크린샷/다중 포맷)로 다운로드하는 스킬. (실험적)
- **firecrawl-interact** — 스크래프한 페이지의 라이브 브라우저 세션을 자연어/코드로 제어하는 스킬 — 클릭, 폼 입력, 내비게이션, 로그인.
- **firecrawl-map** — 웹사이트의 모든 URL을 발견·나열하는 스킬. 검색 필터로 특정 페이지 위치 파악.
- **firecrawl-monitor** — 웹사이트 콘텐츠 변경을 감지해 웹훅/이메일로 알려주는 스킬 — cron·스크래퍼·diff 스크립트 불필요.
- **firecrawl-parse** — 로컬 파일(PDF/DOCX/XLSX/HTML 등)을 클린 마크다운으로 변환해 디스크에 저장하는 스킬.
- **firecrawl-scrape** — 임의의 URL(JS 렌더링 SPA 포함)에서 클린 마크다운을 추출하는 스킬. WebFetch 대체.
- **firecrawl-search** — 전체 페이지 콘텐츠 추출까지 지원하는 웹 검색 스킬. 스니펫이 아닌 실제 결과 + 풀페이지 마크다운.
- **skill-gen** — Firecrawl로 문서 URL을 스크래핑해 완전한 Agent Skill을 자동 생성하는 슬래시 명령어.

### figma (9)
- **figma-code-connect** — Figma 컴포넌트를 코드 스니펫에 매핑하는 Code Connect 템플릿 파일(`.figma.ts`)을 생성·유지하는 스킬.
- **figma-create-new-file** — 새 빈 Figma 파일(design/figjam/slides)을 생성하는 스킬. `create_new_file` 호출의 필수 선행 스킬.
- **figma-generate-design** — 코드/설명으로부터 디자인 시스템을 재사용해 Figma에 화면·뷰·다중 섹션 레이아웃을 구축/업데이트하는 스킬.
- **figma-generate-diagram** — Mermaid.js 구문으로 편집 가능한 FigJam 다이어그램을 생성하는 스킬. `generate_diagram` 호출의 필수 선행 스킬.
- **figma-generate-library** — 코드베이스로부터 Figma에 프로페셔널급 디자인 시스템(변수/토큰, 컴포넌트 라이브러리, 테마)을 구축/업데이트하는 스킬.
- **figma-generate-project-plan** — PRD(+선택적 코드베이스 컨텍스트)를 **FigJam 프로젝트 플랜 보드**로 변환하는 인터랙티브 워크플로 스킬. 섹션 후보 제안 → 사용자 선택 → 섹션별 콘텐츠·블록 확정 → FigJam 생성까지 단계마다 확인을 거친다.
- **figma-use-figjam** — FigJam 컨텍스트에서 `use_figma` MCP 도구를 사용하기 위한 FigJam 특화 스킬. `figma-use`와 함께 사용.
- **figma-use-slides** — Slides 컨텍스트에서 `use_figma` MCP 도구를 사용하기 위한 Slides 특화 스킬. `figma-use`와 함께 사용.
- **figma-use** — Figma Plugin API로 JavaScript를 실행해 노드를 생성/편집/조회하는 기반 스킬. 모든 `use_figma` 호출의 필수 선행 스킬.

### plugin-dev (8)
- **agent-development** — Claude Code 플러그인용 서브에이전트의 구조·시스템 프롬프트·트리거 조건 설계를 안내하는 스킬
- **command-development** — Claude Code 슬래시 명령의 구조·YAML frontmatter·동적 인자·사용자 인터랙션 패턴을 안내하는 스킬
- **create-plugin** — 컴포넌트 설계·구현·검증을 아우르는, 가이드형 엔드투엔드 Claude Code 플러그인 생성 워크플로 (슬래시 명령)
- **hook-development** — Claude Code 플러그인의 이벤트 기반 훅을 생성·구현하는 스킬 (프롬프트 기반 훅 API 중심)
- **mcp-integration** — Claude Code 플러그인에 Model Context Protocol(MCP) 서버를 통합해 외부 도구·서비스를 연동하는 스킬
- **plugin-settings** — `.claude/plugin-name.local.md` 패턴으로 플러그인별 설정·상태를 YAML frontmatter와 마크다운으로 저장하는 스킬
- **plugin-structure** — Claude Code 플러그인의 디렉터리 레이아웃·매니페스트·컴포넌트 구성·자동 발견을 안내하는 스킬
- **skill-development** — Claude Code 플러그인용 스킬의 구조·점진적 공개·작성 스타일 베스트 프랙티스를 안내하는 스킬

### chrome-devtools-mcp (6)
- **a11y-debugging** — Chrome DevTools MCP를 활용해 web.dev 가이드라인 기반으로 웹 페이지의 접근성(a11y)을 디버깅·감사하는 스킬
- **chrome-devtools-cli** — 터미널에서 셸 스크립트/명령으로 Chrome DevTools를 제어해 브라우저 작업을 자동화하는 스킬
- **chrome-devtools** — Chrome DevTools를 MCP로 활용해 효율적으로 디버깅, 트러블슈팅, 브라우저 자동화를 수행하는 핵심 스킬
- **debug-optimize-lcp** — Chrome DevTools MCP로 Largest Contentful Paint(LCP)를 디버깅하고 최적화하는 스킬
- **memory-leak-debugging** — JavaScript/Node.js 애플리케이션의 메모리 누수를 진단·해결하는 스킬
- **troubleshooting** — Chrome DevTools MCP의 연결·타겟 문제를 단계별로 진단·해결하는 트러블슈팅 마법사 스킬

### ralph-loop (3)
- **cancel-ralph** — 활성화된 Ralph 루프의 상태 파일을 제거해 루프를 취소하는 명령어.
- **help** — Ralph Loop 플러그인의 개념·명령어·핵심 메커니즘을 사용자에게 설명하는 도움말 명령어.
- **ralph-loop** — 같은 프롬프트를 Stop 훅으로 반복 주입해 현재 세션 안에서 자기참조 반복 개발 루프(Ralph Wiggum 기법)를 시작하는 명령어.

### claude-md-management (2)
- **claude-md-improver** — 저장소의 CLAUDE.md 파일들을 감사·평가하고 타겟 개선을 수행하는 스킬
- **revise-claude-md** — 현재 세션에서 얻은 학습 내용을 CLAUDE.md에 반영해 업데이트하는 슬래시 명령

### qodo-skills (2)
- **qodo-get-rules** — 현재 코딩 작업에 가장 관련 있는 Qodo 코딩 규칙을, 시맨틱 검색 쿼리를 생성해 `POST /rules/search`로 가져와 심각도별로 적용하는 스킬.
- **qodo-pr-resolver** — 현재 브랜치 PR/MR의 Qodo 리뷰 이슈를 가져와 심각도별로 보여주고, 대화형/일괄로 수정한 뒤 인라인 코멘트에 답글·요약을 다는 스킬. GitHub·GitLab·Bitbucket·Azure DevOps·Gerrit 지원.

### agent-sdk-dev (1)
- **new-sdk-app** — Claude Agent SDK 애플리케이션(Python / TypeScript)을 대화형으로 새로 만들고, 최신 SDK 설치·검증까지 끝내는 슬래시 명령어.

### pr-review-toolkit (1)
- **review-pr** — 변경된 코드(git diff)를 여러 전문 에이전트로 나눠 종합 PR 리뷰를 수행하고, 심각도별 액션 플랜으로 정리해주는 슬래시 명령어.

### feature-dev (1)
- **feature-dev** — 코드베이스를 깊이 이해하고, 모호한 부분을 먼저 질문하고, 여러 아키텍처를 설계한 뒤 구현하는 7단계 가이드형 기능 개발 명령어.

### claude-code-setup (1)
- **claude-automation-recommender** — 코드베이스를 분석해 맞춤형 Claude Code 자동화(훅·서브에이전트·스킬·플러그인·MCP 서버)를 추천하는 스킬

### skill-creator (1)
- **skill-creator** — 새 스킬을 만들고, 기존 스킬을 수정·개선하며, eval로 성능을 측정·최적화하는 메타 스킬

### frontend-design (1)
- **frontend-design** — 일반적인 "AI 슬롭" 미감을 피하고, 독창적이고 프로덕션급의 프론트엔드 인터페이스를 만드는 스킬

### playground (1)
- **playground** — 컨트롤·라이브 프리뷰·프롬프트 출력을 갖춘 자기완결형 단일 HTML 인터랙티브 탐색기를 만드는 스킬

## ⛔ OFF 플러그인 (`_disabled/`, 22) — 켜야 사용

### vercel (13)
- **_conventions** — 이 플러그인의 모든 슬래시 커맨드가 따르는 일관된 구조를 정의하는 메타 문서. 사용자가 직접 호출하는 커맨드가 아님.
- **benchmark-agents** — vercel-plugin의 스킬 주입을 고난도 AI 시스템 시나리오로 스트레스 테스트하는 내부 벤치마크 스킬.
- **benchmark-e2e** — vercel-plugin을 현실적 프로젝트로 돌려 스킬 주입·dev 서버 기동·검증·개선 리포트를 한 번에 생성하는 내부 E2E 벤치마크 스킬.
- **benchmark-sandbox** — 로컬 WezTerm 대신 Vercel Sandbox(원격 microVM)에서 eval 시나리오를 돌리는 내부 벤치마크 스킬.
- **benchmark-testing** — 현실적·기술명 비공개 프롬프트로 vercel-plugin 스킬 주입을 시험할 격리 테스트 프로젝트를 만드는 내부 벤치마크 스킬.
- **bootstrap** — Vercel 연동 리소스로 저장소를 부트스트랩 — 사전 점검, 통합 프로비저닝, env 키 검증 후 db/dev 시작 명령을 안전하게 실행.
- **deploy** — 현재 프로젝트를 Vercel에 배포 — 사전 안전 점검, 프로덕션 명시 확인, 배포 후 검증 포함. 인자로 "prod"/"production" 전달 시 프로덕션 배포.
- **env** — Vercel 환경 변수 관리 — list, pull, add, remove, diff. Vercel과 로컬 개발 환경 간 env 변수 동기화. 시크릿 값은 절대 출력하지 않음.
- **marketplace** — Vercel Marketplace 통합 발견·설치 — 데이터베이스, CMS, 인증, 결제, 옵저버빌리티 등 서비스를 가이드 셋업과 로컬 검증으로 추가.
- **plugin-audit** — 실제 Claude Code 세션 로그를 분석해 vercel-plugin 스킬 주입 성능과 패턴 커버리지 갭을 진단하는 내부 감사 스킬.
- **release** — vercel-plugin을 릴리스 — 게이트 실행, 버전 범프, 아티팩트 생성, 커밋, 푸시까지 처리하는 내부 릴리스 스킬.
- **status** — 현재 Vercel 프로젝트의 상태 진단(Doctor) — 최근 배포, 링크 정보, env 개요, 도메인, 빌드 상태, 옵저버빌리티 점검. 읽기 전용.
- **vercel-plugin-eval** — 실제 Claude Code 세션을 띄워 vercel-plugin의 훅 발화·스킬 주입·dedup 정확성·커버리지를 검증하는 내부 eval 스킬.

### hookify (5)
- **configure** — 기존 hookify 규칙을 인터랙티브하게 활성/비활성 토글하는 커맨드.
- **help** — hookify 플러그인의 동작 원리와 사용법을 설명하는 도움말 커맨드.
- **hookify** — 대화 분석이나 명시적 지시로부터 원치 않는 행동을 막는 hookify 규칙을 생성하는 커맨드.
- **list** — 프로젝트에 설정된 모든 hookify 규칙을 표와 미리보기로 나열하는 커맨드.
- **writing-rules** — hookify 규칙 파일(`.claude/hookify.*.local.md`)의 작성 문법과 패턴을 안내하는 스킬.

### neon (2)
- **neon-postgres-egress-optimizer** — Neon Postgres의 데이터 송신(egress) 비용/트래픽을 최적화하는 에이전트 스킬 (neon 플러그인 동봉).
- **neon-postgres** — Neon 서버리스 Postgres 프로젝트와 데이터베이스를 관리하는 에이전트 스킬 (Neon MCP 서버와 연계).

### railway (1)
- **use-railway** — Railway 인프라(계정 가입/로그인, 프로젝트·서비스·DB·버킷 생성, 배포, 환경/변수, 도메인, 장애 진단, 메트릭, 에이전트 도구 설정)를 CLI/MCP로 운영하는 스킬.

### semgrep (1)
- **setup-semgrep-plugin** — Semgrep 설치·인증·버전 호환성 확인을 통해 Semgrep 플러그인을 셋업하는 커맨드.
