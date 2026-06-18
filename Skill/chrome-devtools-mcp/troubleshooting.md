# troubleshooting

> Chrome DevTools MCP의 연결·타겟 문제를 단계별로 진단·해결하는 트러블슈팅 마법사 스킬

## 📌 용도
Chrome DevTools MCP 서버 설정의 연결 문제와 타겟 문제를 진단하고 수정한다. 트러블슈팅 마법사로서 단계별 진단 프로세스를 따라 사용자의 MCP 서버 구성을 고친다.

## 🎯 트리거 조건
- **언제 호출되는가**: `list_pages`, `new_page`, `navigate_page`가 실패하거나 서버 초기화가 실패할 때.
- **언제 쓰지 않는가**: 정상 동작 중인 디버깅/자동화(→ chrome-devtools), 페이지 콘텐츠 문제는 트러블슈팅이 아닌 해당 스킬.

## 💻 사용 예시 / 명령어
- "list_pages가 계속 실패해"
- "MCP 서버가 시작이 안 돼"
- "Could not find DevToolsActivePort 에러가 나"
- "도구가 9개밖에 안 보여"

## 🛠️ 제공 도구 / 주요 파라미터
**단계별 진단 프로세스**:

**1단계: 설정 파일 찾기/읽기**: `.mcp.json`, `gemini-extension.json`, `.claude/settings.json`, `.vscode/launch.json`, `.gemini/settings.json`을 검색. 잘못된 인자/플래그, 누락된 환경변수, 부적합 환경의 `--autoConnect` 사용 등을 식별.

**2단계: 흔한 연결 에러 분류**:
- **`Could not find DevToolsActivePort`**: `--autoConnect` 전용 에러. ① 올바른 Chrome 버전 실행 확인 → ② `chrome://inspect/#remote-debugging`에서 원격 디버깅 활성화 안내 → ③ `list_pages`로 검증(원래 명령 재시도 금지) → ④ 성공 시 해결, 실패 시 고급 단계.
- **새 빈 프로필 생성**: 플래그 오타 확인(예: `--autoBronnect`).
- **도구 누락 / 9개만 표시**: MCP 클라이언트의 read-only 모드 때문. 모든 도구는 `readOnlyHint`로 주석되어 있으며, 전체 도구는 read-only 모드 해제 필요(Gemini CLI의 Plan Mode 종료 등).
- **확장 프로그램 도구 누락/실패**: `--categoryExtensions` 플래그 확인. Chrome 149 미만은 기존 인스턴스 연결 시 확장 로드 불가.
- **기타 에러**: `Target closed`, "Tool not found"(`--slim` 사용 여부), `Network.enable timed out`, `ERR_MODULE_NOT_FOUND`, 샌드박스/호스트 검증 에러.

**3단계: 알려진 이슈 읽기**: [공식 troubleshooting.md](https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/docs/troubleshooting.md) — 샌드박스 제한(macOS Seatbelt, Linux 컨테이너), WSL 요구사항, `--autoConnect` 핸드셰이크(실행 중인 Chrome 144+ 필요).

**4단계: 구성 공식화**: 에러와 환경(OS, MCP 클라이언트)에 맞는 설정 스니펫 작성. `--browser-url=http://127.0.0.1:9222`(샌드박스 환경), 원격 디버깅 활성화, `--logFile <절대경로>`, `startup_timeout_ms` 증가(Windows Codex는 20000).

**5단계: 진단 명령 실행**:
```bash
npx chrome-devtools-mcp@latest --help
DEBUG=* npx chrome-devtools-mcp@latest --logFile=/tmp/cdm-test.log
```

**6단계: GitHub 이슈 확인**: `gh issue list --repo ChromeDevTools/chrome-devtools-mcp --search "<에러>" --state all` 또는 issues/discussions 페이지 안내.

## 📦 출처 / 설치 상태
- 플러그인: chrome-devtools-mcp@claude-plugins-official
- 활성화: ON (Chrome DevTools MCP 서버와 연동)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/chrome-devtools-mcp/1.2.0/skills/troubleshooting/SKILL.md`
