# figma

> Figma 공식 원격 MCP 서버. OAuth 인증을 거쳐 Figma 디자인/디자인 시스템과 코드 생성을 연동한다.

## 📌 용도
Figma의 공식 HTTP MCP 서버(`https://mcp.figma.com/mcp`)에 연결한다. MCP 서버 자체가 직접 노출하는 도구는 **인증(OAuth) 처리용 2개**이며, 실제 디자인 생성/조회/코드 커넥트 등 핵심 작업은 `figma` **스킬군**(figma-generate-design, figma-create-new-file, figma-code-connect, figma-use 등)과 함께 동작한다. 즉 이 MCP는 인증 게이트웨이 + 스킬의 백엔드 역할을 한다.

## 🎯 트리거 조건 / 사용 맥락
- Figma에 디자인/다이어그램/슬라이드/FigJam을 생성하거나 불러올 때
- Figma 디자인을 코드로 변환(Code Connect)하거나 디자인 시스템/라이브러리를 다룰 때
- 위 작업 전, Figma 계정 연동이 필요할 때 (authenticate → complete_authentication)

## 🛠️ 제공 도구 목록

| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| authenticate | Figma OAuth 인증 시작. 사용자가 로그인/승인할 인증 URL을 발급 | (없음/내부 처리) |
| complete_authentication | 사용자가 로그인 완료 후 인증을 마무리하고 토큰을 확정 | 인증 콜백/코드 |

> 디자인 생성·조회·코드 연동 같은 실제 기능 도구는 MCP의 deferred tool로 직접 노출되지 않고, `figma:figma-*` 스킬을 통해 호출된다. 관련 스킬: `figma-generate-design`, `figma-create-new-file`, `figma-generate-diagram`, `figma-generate-library`, `figma-code-connect`, `figma-use`, `figma-use-figjam`, `figma-use-slides`.

## 💻 사용 예시

**예시 1 — 최초 연동**
1. Figma 관련 작업 요청 발생
2. `authenticate` 호출 → 발급된 URL에서 사용자가 Figma 로그인/권한 승인
3. `complete_authentication`으로 연동 완료 → 이후 figma 스킬 사용 가능

**예시 2 — 디자인 생성**
- 인증 완료 상태에서 `figma:figma-generate-design` 스킬로 시안 생성 (백엔드로 본 MCP 사용)

## 📦 출처 / 설치 상태
- 제공: **플러그인** `figma@claude-plugins-official` (버전 2.2.12)
- 활성화: ON (`settings.json` → `enabledPlugins["figma@claude-plugins-official"]: true`)
- 연결 방식: **http** — `https://mcp.figma.com/mcp` (원격 서버, OAuth 인증 필요)
- 설정/캐시 경로:
  - `~/.claude/plugins/cache/claude-plugins-official/figma/2.2.12/.mcp.json`
  - `~/.claude/plugins/cache/claude-plugins-official/figma/2.2.12/figma-power/mcp.json`
- 세션 내 도구 식별자 접두사: `mcp__plugin_figma_figma__`
