# /setup-semgrep-plugin

> Semgrep 설치·인증·버전 호환성 확인을 통해 Semgrep 플러그인을 셋업하는 커맨드.

## 📌 용도
Semgrep 플러그인을 사용할 수 있도록 환경을 준비한다. Semgrep CLI를 설치하고, Semgrep 계정에 로그인(인증)하며, Pro 엔진을 설치하고, 설치 결과와 버전 호환성(>= 1.146.0)을 확인하는 단계별 절차를 수행한다.

## 🎯 트리거 조건
- **언제 호출되는가**: Semgrep 플러그인을 처음 설정하거나, Semgrep MCP 사용을 위해 CLI·인증·Pro 엔진 준비가 필요할 때.
- **언제 쓰지 않는가**: 이미 Semgrep 설치·인증이 완료되어 스캔만 수행하면 될 때.

## 💻 사용 예시 / 명령어
```
/setup-semgrep-plugin
```
- 인자 없음. 실행 시 아래 단계의 bash 명령들을 순서대로 수행한다.

## 🛠️ 제공 도구 / 주요 파라미터
절차(각 단계의 명령):
1. **Semgrep 설치**: `which semgrep || brew install semgrep`
2. **인증**: `semgrep login --force` (브라우저 창이 열림)
3. **Pro 엔진 설치**: `semgrep install-semgrep-pro || true` (향상된 스캔 기능)
4. **설치 검증**: `semgrep --pro --version`
5. **버전 호환성 확인**: `semgrep --version` — 1.146.0 미만이면 `brew upgrade semgrep`로 업데이트

> 참고: 설치 명령이 `brew` 기반이라 macOS/Homebrew 환경을 전제로 한다. Windows 환경에서는 Semgrep 설치 방식이 다를 수 있다(원본 커맨드는 brew만 명시).

## 📦 출처 / 설치 상태
- 플러그인: semgrep@claude-plugins-official
- 활성화: ⛔ **OFF** (settings.json enabledPlugins = false) — 켜야 사용 가능
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/semgrep/0.5.3/commands/setup-semgrep-plugin.md`
