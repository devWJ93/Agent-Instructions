# mcp-integration

> Claude Code 플러그인에 Model Context Protocol(MCP) 서버를 통합해 외부 도구·서비스를 연동하는 스킬

## 📌 용도
MCP를 통해 플러그인이 외부 서비스·API와 통합되어 구조화된 도구 접근을 제공하도록 한다. 외부 서비스(DB, API, 파일시스템) 연결, 단일 서비스에서 10개 이상의 관련 도구 제공, OAuth·복잡한 인증 처리, MCP 서버를 플러그인에 번들링해 자동 설정한다.

## 🎯 트리거 조건
- **언제 호출되는가**: "MCP 서버 추가", "MCP 통합", "플러그인에 MCP 구성", ".mcp.json 사용", "Model Context Protocol 설정", "외부 서비스 연결", "${CLAUDE_PLUGIN_ROOT} with MCP", 또는 MCP 서버 유형(SSE, stdio, HTTP, WebSocket) 논의 시.
- **언제 쓰지 않는가**: 내부 컴포넌트(스킬·에이전트·훅·명령) 작성은 해당 전용 스킬.

## 💻 사용 예시 / 명령어
- "플러그인에 데이터베이스 MCP 서버를 추가해줘"
- "Asana SSE MCP 서버를 연동하고 싶어"
- "토큰 인증 HTTP MCP를 설정해줘"

## 🛠️ 제공 도구 / 주요 파라미터
**구성 방법**: ① 전용 `.mcp.json`(플러그인 루트, 권장), ② `plugin.json`의 `mcpServers` 필드 인라인(단순 단일 서버).

**서버 유형**:
| 유형 | 전송 | 적합 | 인증 |
| --- | --- | --- | --- |
| stdio | 프로세스 | 로컬 도구·커스텀 서버 | env 변수 |
| SSE | HTTP | 호스팅 서비스·클라우드 API | OAuth |
| HTTP | REST | API 백엔드·토큰 인증 | 토큰 |
| ws | WebSocket | 실시간·스트리밍 | 토큰 |

- **stdio**: `{"command":"npx","args":[...],"env":{...}}` — Claude Code가 프로세스 spawn/관리, stdin/stdout 통신.
- **SSE**: `{"type":"sse","url":"https://mcp.asana.com/sse"}` — OAuth 자동 처리, 첫 사용 시 브라우저 인증.
- **HTTP**: `{"type":"http","url":"...","headers":{"Authorization":"Bearer ${API_TOKEN}"}}`.
- **WebSocket**: `{"type":"ws","url":"wss://...","headers":{...}}`.

**환경변수 확장**: `${CLAUDE_PLUGIN_ROOT}`(포터빌리티 필수), 사용자 env(`${MY_API_KEY}`).

**도구 네이밍**: `mcp__plugin_<plugin-name>_<server-name>__<tool-name>`. 명령 frontmatter에서 특정 도구만 pre-allow(와일드카드 지양).

**라이프사이클**: 플러그인 로드 → MCP 구성 파싱 → 서버 시작/연결 → 도구 발견·등록 → `mcp__plugin_...` 사용 가능. `/mcp`로 서버 확인.

**보안**: HTTPS/WSS 사용, env 변수로 토큰(하드코딩·git 커밋 금지), 특정 도구만 pre-allow.

**통합 패턴**: ① 단순 도구 래퍼(명령 + 검증), ② 자율 에이전트, ③ 멀티 서버 플러그인.

**테스트**: `.mcp.json` 구성 → 로컬 설치 → `/mcp` 확인 → `claude --debug` 로그.

**참조 파일**: `references/server-types.md`, `references/authentication.md`, `references/tool-usage.md`; `examples/stdio-server.json`, `sse-server.json`, `http-server.json`

## 📦 출처 / 설치 상태
- 플러그인: plugin-dev@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/plugin-dev/unknown/skills/mcp-integration/SKILL.md`
