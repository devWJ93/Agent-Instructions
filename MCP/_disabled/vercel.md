# vercel (MCP)

> Vercel 공식 원격 MCP 서버 — OAuth 인증으로 문서 검색, 프로젝트/배포 조회, 로그 검사를 제공(초기 릴리스는 읽기 전용).

## 📌 용도
**사용자용 원격 MCP 서버**다. vercel 플러그인이 제공하는 공식 Vercel MCP 서버(`https://mcp.vercel.com`)에 연결해, 에이전트가 Vercel 플랫폼의 데이터를 직접 조회할 수 있게 한다. 초기 릴리스는 **읽기 전용(read-only)**으로, 문서 검색, 프로젝트/배포 목록 조회, 로그 검사를 지원한다.

플러그인의 commands(/deploy, /status, /env 등)가 "MCP-first, CLI-fallback" 규약을 따르므로, 이 MCP가 켜져 있으면 읽기 작업(배포 목록, 런타임 로그, drains 조회 등)이 CLI 대신 MCP를 통해 수행된다.

## 🎯 사용 맥락
- **언제 쓰이는가**: Vercel 프로젝트/배포 상태를 조회하거나, Vercel 공식 문서를 검색하거나, 배포 로그를 검사할 때. `/status`·`/deploy`의 검증 단계에서 `list_drains`·`get_runtime_logs` 같은 MCP 읽기 도구를 우선 사용.
- **언제 쓰지 않는가**: 쓰기 작업(배포 트리거, env 추가/삭제). 초기 릴리스가 읽기 전용이므로 이런 변경은 Vercel CLI로 폴백한다.

## 🛠️ 제공 도구 목록
이 MCP는 **원격 HTTP 서버**(`type: http`)이며, `.mcp.json`에 정적 도구 목록이 정의되어 있지 않다. 실제 도구는 **OAuth 인증 후 런타임에 원격 서버가 노출**한다. 첫 연결 시 에이전트는 Vercel 인증(authorize) 프롬프트를 받는다.

| 항목 | 값 |
|------|-----|
| 서버 타입 | `http` (원격) |
| URL | `https://mcp.vercel.com` |
| 인증 | OAuth (첫 연결 시 Vercel 인증 프롬프트) |
| 권한 | 읽기 전용 (초기 릴리스) |
| 노출 도구 | 런타임에 동적 노출 — `.mcp.json`에 목록 없음 |

플러그인 커맨드 문서에서 참조되는 MCP 읽기 도구 예시: `list_drains`(drain 목록 조회), `get_runtime_logs`(런타임 로그, 레벨 필터). 그 외 문서 검색·프로젝트/배포 조회 도구가 인증 후 노출된다.

## 💻 사용 예시
```text
# 첫 연결: 에이전트가 Vercel OAuth authorize 프롬프트를 표시 → 사용자 승인

# 이후 자연어 요청으로 읽기 도구 사용 (런타임 노출 도구명에 의존):
"이 프로젝트의 최근 배포 목록을 보여줘"        → 배포 조회 도구
"프로덕션 런타임 에러 로그를 가져와"            → get_runtime_logs (level=error)
"설정된 drains를 확인해"                       → list_drains
"Vercel의 cron jobs 설정 문서를 검색해"        → 문서 검색 도구
```

## 📦 출처 / 설치 상태
- 제공: vercel 플러그인
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 연결 방식: 원격 HTTP MCP (`type: http`, `https://mcp.vercel.com`), OAuth 인증, 읽기 전용
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/.mcp.json`
