# use-railway

> Railway 인프라(계정 가입/로그인, 프로젝트·서비스·DB·버킷 생성, 배포, 환경/변수, 도메인, 장애 진단, 메트릭, 에이전트 도구 설정)를 CLI/MCP로 운영하는 스킬.

## 📌 용도
Railway 플랫폼의 모든 인프라 작업을 한곳에서 수행한다.
- 계정 가입(signup) / 로그인 — 통합 OAuth 플로우(`railway up`, `railway login`)
- 프로젝트 / 서비스 / 데이터베이스 / 버킷(S3 호환 오브젝트 스토리지) 프로비저닝
- 코드 배포, 재배포, 재시작, 빌드 설정(모노레포·Dockerfile 포함)
- 환경(production/staging 등) · 변수 · 도메인 · 네트워킹 설정
- 빌드/런타임 장애 진단, 상태·로그·메트릭 조회, 복구
- Railway 에이전트 도구(MCP·skills) 설치 및 인증 설정
- Railway 공식 문서/GraphQL API 조회

## 🎯 트리거 조건
- **언제 호출되는가**
  - 사용자가 Railway를 언급할 때 (배포, 서비스, 환경, 버킷, 오브젝트 스토리지, 빌드 실패, 에이전트 설정, MCP, 인프라 작업)
  - "Railway"라고 명시하지 않아도 가입/등록/온보딩/로그인/배포 의도가 있을 때
  - "sign me up", "create my Railway account", "deploy this app", "ship", "push to Railway" 등
  - Railway 대시보드 URL(`https://railway.com/project/...`)을 붙여넣을 때
- **언제 쓰지 않는가**
  - Railway가 아닌 다른 PaaS/클라우드(Vercel, AWS 등) 작업
  - 단순 로컬 코드 작업, Railway 인프라와 무관한 일반 개발 작업

## 💻 사용 예시 / 명령어
```bash
# 컨텍스트/인증
railway status --json                         # 현재 링크된 project/environment/service
railway whoami --json                         # 인증·워크스페이스 정보
railway project list --json                   # 프로젝트 목록

# 가입 / 로그인 (통합 OAuth)
railway up                                     # cwd 배포 + 미인증 시 가입/로그인까지 한 번에
railway up -y                                  # 프롬프트 스킵, 비대화형 강제 생성
railway login                                  # 로그인 겸 신규 가입(별도 signup 명령 없음)
railway login --browserless                    # 헤드리스: 디바이스 코드(URL+코드) 플로우

# 리소스 생성
railway add --database <type> --json           # DB 추가 (항상 --json)
railway add --service <name> --json            # 빈 서비스 추가

# 변수 / 배포 / 로그 / 메트릭
railway variable list --service <svc> --json
railway variable set KEY=value --service <svc>
railway up --detach -m "<summary>"             # cwd 배포 (QUEUED 반환 — 성공 확정 아님)
railway deployment list --json                 # 배포 상태 폴링 (SUCCESS 확인 필수)
railway logs --service <svc> --lines 200 --json
railway metrics --service <svc> --since 1h --json

# 버킷 (S3 호환 오브젝트 스토리지)
railway bucket list --json
railway bucket info --bucket <name> --json
railway bucket credentials --bucket <name> --json

# 에이전트 도구 / MCP / skills 설치
railway setup agent              # skills + MCP + 인증 설정
railway mcp install              # 로컬 stdio MCP 설치
railway mcp install --remote     # 원격 MCP(https://mcp.railway.com) 설정
railway skills update --agent codex
railway agent -p "why is my service crashing?"  # 자연어 조사 (OAuth 로그인 필요)
```

## 🛠️ 제공 도구 / 주요 파라미터
**세 가지 에이전트용 작업 경로(tool routing)** — 작업에 맞게 선택:
| 경로 | 설명 | 적합한 작업 |
|---|---|---|
| Remote MCP (`https://mcp.railway.com`) | Railway OAuth 기반, 로컬 CLI 상태 불필요 | 계정/프로젝트/서비스 탐색, 배포 상태, 제한된 로그, 단순 재배포·프로젝트 생성, 복잡 워크플로우를 `railway-agent`에 위임 |
| Local CLI MCP (`railway mcp`) | CLI 백엔드 플랫폼 작업 | 변수, 도메인, 서비스 설정, 템플릿, 메트릭, HTTP 요약, 버킷, 볼륨, 문서, 디렉토리 배포 |
| Railway CLI (`railway`) | 로컬 머신 상태 의존 | cwd 배포, `railway up`/`run`, SSH, DB 분석 스크립트, 로컬 링크, 대화형 설정 |

**리소스 모델 계층**: Workspace(과금/팀) → Project → Environment → Service / Bucket → Deployment

**주요 환경 변수 / 플래그**:
- `RAILWAY_CALLER=skill:use-railway@1.2.4`, `RAILWAY_AGENT_SESSION=...` — CLI 호출 시 텔레메트리 프리픽스
- `RAILWAY_API_TOKEN`(계정 범위) / `RAILWAY_TOKEN`(프로젝트 범위) — 완전 무인 실행용
- `--project` / `--environment` / `--service` — 컨텍스트 명시 (링크보다 권장)

**사용자만 실행해야 하는 명령(에이전트 직접 실행 금지)**: `enable-pg-stats.py`, `pg-extensions.py`, `ALTER SYSTEM SET`, `CREATE/DROP EXTENSION` — DB 상태 변경/재시작 유발.

**핵심 실행 규칙**: 배포는 터미널 SUCCESS를 관측하기 전까지 성공 보고 금지(`railway deployment list --json` 폴링). 파괴적 작업은 사전 확인.

## 📦 출처 / 설치 상태
- 플러그인: railway@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/railway/1.2.4/skills/use-railway/SKILL.md`
  - (참고: 캐시에는 railway `1.2.1`~`1.2.4` 버전 디렉토리가 공존하며, 현재 설치된 최신본은 `1.2.4`. 최상위 스킬은 `use-railway` 단일 스킬이고, 부가 참조 문서는 `references/`(analyze-db, setup, deploy, configure, operate, request)에 분리되어 있음)
