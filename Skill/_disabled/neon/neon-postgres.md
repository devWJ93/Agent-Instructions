# neon-postgres

> Neon 서버리스 Postgres 프로젝트와 데이터베이스를 관리하는 에이전트 스킬 (Neon MCP 서버와 연계).

> ⚠️ **원본 근거 한계**: 이 스킬의 SKILL.md 본문은 로컬 캐시에 실제로 vendoring 되어 있지 않다. 캐시의 `skills/neon-postgres`는 `../../../skills/neon-postgres`를 가리키는 **깨진 심볼릭 링크 스텁(29바이트)**이며, 대상 파일이 디스크에 존재하지 않는다. 아래 내용은 플러그인 매니페스트(`.claude-plugin`)의 description·keywords·스킬명·저장소 등 **확인 가능한 원본 메타데이터에 한정**해 작성했다. 트리거 조건/명령어 등 SKILL.md 본문에만 있는 세부는 로컬에서 확인 불가하므로 추측으로 채우지 않았다.

## 📌 용도
Neon(서버리스 Postgres) 프로젝트와 데이터베이스를 관리한다. 매니페스트 설명: "Manage your Neon projects and databases with the neon-postgres agent skill and the Neon MCP Server". 키워드: neon, postgres, serverless, database, mcp.

## 🎯 트리거 조건
- **언제 호출되는가**: (SKILL.md 본문 미vendoring — 정확한 트리거 문구는 로컬 확인 불가) 스킬명·매니페스트상 Neon 프로젝트/Postgres 데이터베이스 관리 작업 시 호출되는 것으로 보임.
- **언제 쓰지 않는가**: (로컬 확인 불가) Neon이 아닌 다른 Postgres/DB 작업으로 추정되나 원본 미확인.

## 💻 사용 예시 / 명령어
- (SKILL.md 본문 미vendoring으로 로컬 확인 불가) Neon 관리 작업은 함께 제공되는 `neon` MCP 서버(`https://mcp.neon.tech/mcp`)의 원격 노출 도구를 통해 수행되는 구조.
- 정확한 명령/예시는 원본 저장소(아래) 확인 필요.

## 🛠️ 제공 도구 / 주요 파라미터
- 이 스킬은 `neon` MCP 서버(원격 HTTP)와 연계되어 동작. MCP 도구는 원격/런타임 노출이라 mcp.json에 정적 목록 없음. (`MCP/_disabled/neon.md` 참조)
- 스킬 자체의 allowed-tools 등 프론트매터는 SKILL.md 본문 미vendoring으로 로컬 확인 불가.

## 📦 출처 / 설치 상태
- 플러그인: neon@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로(스텁): `~/.claude/plugins/cache/claude-plugins-official/neon/1.0.0/skills/neon-postgres`
  - 심볼릭 링크 대상: `../../../skills/neon-postgres` (로컬에 미존재 — 본문 vendoring 안 됨)
- 매니페스트: `...\neon\1.0.0\.claude-plugin\` (name: neon, version: 1.0.0, author: Neon)
- 원본 저장소: https://github.com/neondatabase/agent-skills (전체 SKILL.md 본문은 여기서 확인 가능)
