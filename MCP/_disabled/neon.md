# neon (MCP)

> Neon(서버리스 Postgres)의 프로젝트·데이터베이스를 관리하는 원격 HTTP MCP 서버. `https://mcp.neon.tech/mcp`에 연결.

## 📌 용도
Neon 서버리스 Postgres 플랫폼의 프로젝트와 데이터베이스를 관리한다. neon 플러그인 매니페스트 설명: "Manage your Neon projects and databases with the neon-postgres agent skill and the Neon MCP Server". 도구가 원격에서 노출되므로 Neon 계정/프로젝트 생성·DB 운영·쿼리 등 Neon 플랫폼 작업 전반을 커버한다.

## 🎯 사용 맥락
- Neon 프로젝트/데이터베이스 생성·관리, 브랜치, 연결 문자열 조회 등 Neon 플랫폼 작업
- 함께 제공되는 `neon-postgres`, `neon-postgres-egress-optimizer` 스킬과 연계하여 사용
- 로컬 CLI 상태가 아닌 **원격 서버(HTTP)**에 직접 연결 — 인증은 연결 시점에 처리

## 🛠️ 제공 도구 목록
| 도구명 | 기능 | 주요 파라미터 |
|---|---|---|
| (원격/런타임 노출) | mcp.json에 도구 목록 없음 | — |

mcp.json은 도구를 정적으로 나열하지 않는다. `type: http`, `url: https://mcp.neon.tech/mcp`로 **원격 MCP 서버에 연결**하며, 실제 도구 카탈로그는 Neon이 원격 엔드포인트에서 런타임에 노출한다.

**연결 정의 (실제 mcp.json 내용):**
```json
{
  "mcpServers": {
    "neon": {
      "type": "http",
      "url": "https://mcp.neon.tech/mcp"
    }
  }
}
```

## 💻 사용 예시
- 플러그인을 활성화(enabledPlugins=true)하면 `neon` MCP가 `https://mcp.neon.tech/mcp`에 연결되어 원격 노출 도구를 사용할 수 있다.
- Neon 프로젝트/DB 생성·조회, 브랜치 관리, 연결 정보 획득 등은 원격 서버가 제공하는 도구로 수행 (구체 도구명은 런타임 결정).

## 📦 출처 / 설치 상태
- 제공: neon@claude-plugins-official
- 활성화: ⛔ OFF (enabledPlugins = false)
- 연결 방식: 원격 HTTP MCP (`type: http`, `url: https://mcp.neon.tech/mcp`) — 도구는 원격/런타임 노출
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/neon/1.0.0/mcp.json`
- 저장소: https://github.com/neondatabase/agent-skills
