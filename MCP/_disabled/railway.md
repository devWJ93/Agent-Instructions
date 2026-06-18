# railway (MCP)

> Railway CLI를 백엔드로 하는 로컬 stdio MCP 서버. CLI 기반 플랫폼 작업(변수·도메인·서비스 설정·메트릭·버킷·문서·디렉토리 배포 등)을 MCP 도구로 노출.

## 📌 용도
Railway 플랫폼 운영 작업을 MCP 인터페이스로 제공한다. `use-railway` 스킬의 "Local CLI MCP" 경로에 해당하며, 로컬 CLI 인증·상태를 그대로 활용한다.
- 변수, 도메인, 서비스 설정, 템플릿, 메트릭, HTTP 요약, 버킷, 볼륨, 문서 조회, 디렉토리 배포 등
- (별개로 Railway는 원격 MCP `https://mcp.railway.com`도 제공 — OAuth 기반, 로컬 상태 불필요. 이 mcp.json은 **로컬 stdio** 버전임)

## 🎯 사용 맥락
- Railway 플랫폼 상태를 읽거나 변경하되, 작업이 플랫폼 스코프이고 해당 MCP 도구가 있을 때 CLI 셸 호출 대신 사용
- 로컬 레포/셸/SSH/DB 스크립트가 필요한 작업은 CLI 직접 호출이 더 적합 (MCP는 CLI의 cwd 링크를 공유하지 않을 수 있으므로 project/environment/service ID를 명시적으로 전달해야 함)

## 🛠️ 제공 도구 목록
| 도구명 | 기능 | 주요 파라미터 |
|---|---|---|
| (런타임 노출) | mcp.json에는 개별 도구 목록이 정의되어 있지 않음 | — |

mcp.json은 도구를 정적으로 나열하지 않고 `railway mcp` 프로세스를 stdio로 띄워 **런타임에 도구를 노출**한다. 따라서 실제 도구 카탈로그는 서버 실행 시점에 결정되며(설치된 Railway CLI 버전에 의존), 변수/도메인/서비스 설정/메트릭/HTTP 요약/버킷/볼륨/문서/디렉토리 배포 등의 CLI 기반 작업에 대응한다.

**연결 정의 (실제 mcp.json 내용):**
```json
{
  "mcpServers": {
    "railway": {
      "command": "railway",
      "args": ["mcp"]
    }
  }
}
```
- 전제: 로컬에 Railway CLI(`railway`)가 설치·인증되어 있어야 함 (`npm i -g @railway/cli` 또는 `curl -fsSL https://railway.com/install.sh`)

## 💻 사용 예시
```bash
# MCP 서버 설치 (에이전트 설정에 등록)
railway mcp install                       # 로컬 stdio MCP
railway mcp install --agent claude-code   # 특정 에이전트 대상
railway mcp install --remote              # 원격 MCP(https://mcp.railway.com)로 대체 설정

# 서버 자체 실행 (mcp.json이 호출하는 형태)
railway mcp
```
지원 대상 에이전트: `claude-code`, `cursor`, `codex`, `opencode`, `copilot`, `factory-droid`.

## 📦 출처 / 설치 상태
- 제공: railway@claude-plugins-official
- 활성화: ⛔ OFF (enabledPlugins = false)
- 연결 방식: 로컬 stdio (`command: railway`, `args: ["mcp"]`) — 도구는 런타임 노출 / 별도 원격 MCP(HTTP, `https://mcp.railway.com`) 옵션도 존재
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/railway/1.2.4/.cursor-plugin/mcp.json`
  - (지시상 경로는 `1.2.1`이었으나 현재 설치 최신본은 `1.2.4`. 동일 mcp.json 구조가 각 버전 디렉토리에 존재)
