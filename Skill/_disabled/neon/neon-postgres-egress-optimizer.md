# neon-postgres-egress-optimizer

> Neon Postgres의 데이터 송신(egress) 비용/트래픽을 최적화하는 에이전트 스킬 (neon 플러그인 동봉).

> ⚠️ **원본 근거 한계**: 이 스킬의 SKILL.md 본문은 로컬 캐시에 vendoring 되어 있지 않다. 캐시의 `skills/neon-postgres-egress-optimizer`는 `../../../skills/neon-postgres-egress-optimizer`를 가리키는 **깨진 심볼릭 링크 스텁(46바이트)**이며 대상 파일이 디스크에 없다. 아래 내용은 **스킬명과 플러그인 매니페스트** 등 확인 가능한 원본 메타데이터에 한정한다. egress 최적화의 구체 동작/트리거/명령은 SKILL.md 본문에만 존재하므로 로컬에서 확인할 수 없어 추측으로 채우지 않았다.

## 📌 용도
스킬명으로 볼 때 Neon Postgres의 **egress(데이터 송신) 최적화** — 즉 DB에서 외부로 나가는 데이터 전송량/비용을 줄이는 작업을 돕는 스킬. (Neon은 사용량 기반 과금이며 egress가 비용 요소) 정확한 범위는 SKILL.md 본문 미vendoring으로 로컬 확인 불가.

## 🎯 트리거 조건
- **언제 호출되는가**: (SKILL.md 본문 미vendoring — 정확한 트리거 문구 로컬 확인 불가) 스킬명상 Neon egress/전송량/비용 최적화 요청 시 호출되는 것으로 추정.
- **언제 쓰지 않는가**: (로컬 확인 불가) 일반적인 Neon 프로젝트/DB 관리는 `neon-postgres` 스킬이 담당하는 것으로 보임.

## 💻 사용 예시 / 명령어
- (SKILL.md 본문 미vendoring으로 로컬 확인 불가) 원본 저장소에서 확인 필요.

## 🛠️ 제공 도구 / 주요 파라미터
- `neon` MCP 서버(원격 HTTP, `https://mcp.neon.tech/mcp`)와 연계 추정. MCP 도구는 원격/런타임 노출. (`MCP/_disabled/neon.md` 참조)
- 스킬 프론트매터(allowed-tools 등)는 SKILL.md 본문 미vendoring으로 로컬 확인 불가.

## 📦 출처 / 설치 상태
- 플러그인: neon@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로(스텁): `~/.claude/plugins/cache/claude-plugins-official/neon/1.0.0/skills/neon-postgres-egress-optimizer`
  - 심볼릭 링크 대상: `../../../skills/neon-postgres-egress-optimizer` (로컬에 미존재 — 본문 vendoring 안 됨)
- 매니페스트: `...\neon\1.0.0\.claude-plugin\` (name: neon, version: 1.0.0, author: Neon)
- 원본 저장소: https://github.com/neondatabase/agent-skills (전체 SKILL.md 본문은 여기서 확인 가능)
