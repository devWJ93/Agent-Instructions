# benchmark-agents

> vercel-plugin의 스킬 주입을 고난도 AI 시스템 시나리오로 스트레스 테스트하는 내부 벤치마크 스킬.

## 📌 용도
**vercel 플러그인 자체 개발/벤치마크용 내부 스킬**이다. 일반 사용자의 Vercel 배포 작업과는 무관하며, 플러그인 개발자가 플러그인의 스킬 주입(skill injection) 품질을 검증할 때 쓴다.

Workflow DevKit, AI Gateway, MCP, Chat SDK, Queues, Flags, Sandbox, 멀티 에이전트 오케스트레이션 등 Vercel 플랫폼의 첨단 기능을 요구하는 복잡한 빌드 시나리오를 만들어, 실제 Claude Code 세션을 띄우고 플러그인이 올바른 스킬을 주입하는지, PostToolUse 검증이 잘못된 패턴을 잡아내는지 확인한다. setup → launch → monitor → verify → fix → release → repeat의 전체 eval 루프를 다룬다.

핵심 철학은 "eval은 스크립트가 아니라 대화 안에서 사람(에이전트)이 직접 실행한다"이다. `claude --print`, eval 스크립트, `Bun.spawn` 방식은 금지된다 — 훅(PreToolUse/PostToolUse/UserPromptSubmit)이 대화형 도구 호출 세션에서만 발화하기 때문이다. WezTerm 패널에서 대화형 Claude Code 세션을 띄우는 방식만 플러그인을 올바르게 작동시킨다.

## 🎯 트리거 조건
- **언제 호출되는가**: 플러그인 개발자가 고급 AI 에이전트 시나리오(멀티 시스템 빌드)로 스킬 주입을 평가하려 할 때. macOS + WezTerm + `~/dev/vercel-plugin-testing/` 로컬 환경 전제.
- **언제 쓰지 않는가**: 실제 프로덕트를 만들거나 Vercel에 배포하려는 일반 사용자 작업. 이 경우 commands(/deploy, /env 등)나 vercel MCP를 쓴다.

## 💻 사용 예시 / 명령어
```bash
# 1. 테스트 디렉토리 생성 + 플러그인 설치 (타임스탬프 필수)
TS=$(date +%Y%m%d-%H%M)
SLUG="my-app-$TS"
mkdir -p ~/dev/vercel-plugin-testing/$SLUG
cd ~/dev/vercel-plugin-testing/$SLUG
npx add-plugin https://github.com/vercel/vercel-plugin -s project -y

# 2. WezTerm 패널에서 대화형 세션 띄우기
wezterm cli spawn --cwd .../$SLUG -- /bin/zsh -ic \
  "unset CLAUDECODE; VERCEL_PLUGIN_LOG_LEVEL=debug x '<PROMPT>' --settings .claude/settings.json; exec zsh"

# 3. 디버그 로그 찾기 (SessionStart 훅 발화까지 ~25초 대기)
find ~/.claude/debug -name "*.txt" -mmin -2 -exec grep -l "$SLUG" {} +
```

프롬프트 설계 규칙: **기술이 아니라 프로덕트를 묘사**하라(예: "각 단계를 스트리밍하는 멀티스텝 생성 파이프라인"). 항상 "Link the project to my vercel-labs team... Skip any planning and just build it. Get the dev server running."로 끝낸다.

## 🛠️ 제공 도구 / 주요 파라미터
| 구분 | 내용 |
|------|------|
| 모니터링 | 클레임 디렉토리(`/tmp/vercel-plugin-<session-id>-seen-skills.d`)로 주입된 스킬 추적, 디버그 로그에서 훅 발화/주입/PostToolUse VALIDATION 카운트 |
| 검증 포인트 | WDK 프로젝트 구조(`src/` 없어야 함), `withWorkflow`, 이미지 모델(`gemini-3.1-flash-image-preview`), gateway 사용 여부, AI Elements 설치 |
| 시나리오 | 12개 시나리오(doc-qa-agent, multi-agent-research 등), 3개 복잡도 티어 |
| 커버리지 리포트 | `.notes/COVERAGE.md`에 세션 인덱스/훅 매트릭스/스킬 주입 표/코드 품질 |
| 절대 금지 | `claude --print`, `--dangerously-skip-permissions`, `/tmp/` 사용, 수동 훅 설정, 디렉토리명 대문자 |

## 📦 출처 / 설치 상태
- 플러그인: vercel@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/.claude/skills/benchmark-agents/SKILL.md`
