# vercel-plugin-eval

> 실제 Claude Code 세션을 띄워 vercel-plugin의 훅 발화·스킬 주입·dedup 정확성·커버리지를 검증하는 내부 eval 스킬.

## 📌 용도
**vercel 플러그인 자체 개발/검증용 내부 스킬**이다. 실제 Claude Code 세션을 WezTerm으로 띄우고 디버그 로그를 실시간 모니터링하며, 8개 등록 훅이 모두 올바르게 발화하는지와 dedup(중복 제거)이 정확한지를 검증한다.

benchmark-agents가 "고급 AI 시스템 시나리오 + 코드 품질 검증"에 무게를 둔다면, 이 스킬은 **훅 발화 정확성과 dedup 정합성**(주입 횟수 == 클레임 수)에 초점을 맞춘 라이브 eval이다.

## 🎯 트리거 조건
- **언제 호출되는가**: 플러그인 개발자가 모든 훅(SessionStart·PreToolUse·UserPromptSubmit·PostToolUse·SessionEnd)의 발화와 스킬 dedup을 라이브로 검증하고 커버리지 리포트를 만들 때. macOS + WezTerm 로컬 환경 전제.
- **언제 쓰지 않는가**: 일반 사용자의 실제 Vercel 작업, 또는 이미 끝난 세션의 사후 분석(plugin-audit 사용).

## 💻 사용 예시 / 명령어
```bash
# 1. 테스트 디렉토리 생성 + 플러그인 설치 (타임스탬프 필수)
TS=$(date +%Y%m%d-%H%M)
SLUG="my-eval-$TS"
mkdir -p ~/dev/vercel-plugin-testing/$SLUG
cd ~/dev/vercel-plugin-testing/$SLUG
npx add-plugin https://github.com/vercel/vercel-plugin -s project -y

# 2. WezTerm 세션 기동
wezterm cli spawn --cwd .../$SLUG -- /bin/zsh -ic \
  "unset CLAUDECODE; VERCEL_PLUGIN_LOG_LEVEL=debug x '<PROMPT>' --settings .claude/settings.json; exec zsh"

# 3. 디버그 로그 찾기 (~25초 대기)
find ~/.claude/debug -name "*.txt" -mmin -2 -exec grep -l "$SLUG" {} +
```

## 🛠️ 제공 도구 / 주요 파라미터
모니터링 대상:
- **훅 발화(8개)**: SessionStart(3개), PreToolUse 주입, UserPromptSubmit, PostToolUse validate + shadcn font-fix, SessionEnd cleanup
- **dedup 정확성(핵심 메트릭)**: 클레임 디렉토리 파일 수 == 주입 수. `skillInjection:`은 로그에 주입당 3회 나오므로 3으로 나눔
- **PostToolUse validate 품질**: API 키 우회·구형 모델·잘못된 패턴 등 실제 catch(VALIDATION 그렙)

절대 금지: `claude --print`/`-p`, `--dangerously-skip-permissions`, `/tmp/` 사용, 수동 훅 설정, `CLAUDE_PLUGIN_ROOT` 수동 설정, `bash -c`(→ `/bin/zsh -ic`), claude 풀패스(→ `x` 별칭), eval 스크립트 작성.

시나리오 설계: 프로덕트/기능만 묘사. 트리거 어려운 스킬 목록 제공(ai-elements, v0-dev, vercel-firewall, marketplace, geist, json-render는 명시적 기술 언급 필요). 커버리지 리포트는 `.notes/COVERAGE.md`에 세션 인덱스·훅 매트릭스·44개 스킬 주입 표·dedup 통계.

## 📦 출처 / 설치 상태
- 플러그인: vercel@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/.claude/skills/vercel-plugin-eval/SKILL.md`
