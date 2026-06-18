# semgrep (MCP)

> Semgrep CLI를 MCP 서버(`semgrep mcp`)로 띄워 코드 보안 스캔과 보안 기본값 주입을 제공하는 MCP.

## 📌 용도
Semgrep 정적 분석 엔진을 MCP 서버로 연결한다. 로컬에 설치된 `semgrep` CLI를 `semgrep mcp` 서브커맨드로 실행하여, Claude가 코드 보안 취약점 스캔과 보안 기본값 주입을 도구로 호출할 수 있게 한다. 플러그인이 켜지면 `.mcp.json`을 통해 이 서버가 등록되고, 함께 정의된 훅들이 파일 수정·세션 시작·프롬프트 제출 시점에 `semgrep mcp`를 자동 호출한다.

> 사전 준비: `semgrep` CLI 설치·인증이 필요하다 (`/setup-semgrep-plugin` 커맨드로 셋업). MCP의 `command`가 `semgrep`이므로 PATH에 semgrep 실행 파일이 있어야 한다.

## 🎯 사용 맥락
- 코드 변경(Write/Edit) 후 보안 관점에서 자동 스캔이 필요할 때.
- 세션 시작·프롬프트 제출 시 보안 기본값(secure defaults)을 컨텍스트에 주입하고 싶을 때.
- 정적 분석(SAST) 기반으로 취약 패턴을 찾아내고자 할 때.

## 🛠️ 제공 도구 목록
MCP 서버는 단일 진입점 `semgrep mcp`로 구동되며, 플러그인의 훅 정의에서 확인되는 동작(`-k` 키)은 다음과 같다. (아래는 캐시의 `hooks/hooks.json`에 명시된 호출만 근거로 정리한 것이다. 개별 MCP 도구의 세부 파라미터는 원본 파일에 명시되어 있지 않다.)

| 도구/동작 (호출 키) | 기능 | 호출 시점(훅) |
|---|---|---|
| `semgrep mcp -k post-tool-cli-scan` | 코드 수정 후 Semgrep CLI 보안 스캔 수행 | PostToolUse (matcher: `Write|Edit`) |
| `semgrep mcp -k inject-secure-defaults` | 세션 시작 시 보안 기본값 주입 | SessionStart (matcher: `startup`) |
| `semgrep mcp -k inject-secure-defaults-short` | 사용자 프롬프트 제출 시 보안 기본값(축약) 주입 | UserPromptSubmit |
| `${CLAUDE_PLUGIN_ROOT}/scripts/check_version.sh` | Semgrep 버전 호환성 확인(스크립트) | SessionStart (matcher: `startup|resume|clear|compact`) |

## 💻 사용 예시
`.mcp.json` 등록 형태(연결 정의):
```json
{
  "mcpServers": {
    "semgrep": {
      "command": "semgrep",
      "args": ["mcp"]
    }
  }
}
```
플러그인이 활성화되면 위 서버가 자동 등록되고, 훅이 `semgrep mcp -k <key>` 형태로 각 이벤트에서 서버를 호출한다. 사전에 `/setup-semgrep-plugin`으로 CLI 설치·로그인을 마쳐야 한다.

## 📦 출처 / 설치 상태
- 제공: semgrep 플러그인 (semgrep@claude-plugins-official)
- 활성화: ⛔ OFF (settings.json enabledPlugins = false)
- 연결 방식: **stdio** — `command: "semgrep"`, `args: ["mcp"]` (로컬 semgrep CLI 실행)
- 캐시 경로:
  - `.mcp.json`: `~/.claude/plugins/cache/claude-plugins-official/semgrep/0.5.3/.mcp.json`
  - 훅 정의: `~/.claude/plugins/cache/claude-plugins-official/semgrep/0.5.3/hooks/hooks.json`
