# benchmark-testing

> 현실적·기술명 비공개 프롬프트로 vercel-plugin 스킬 주입을 시험할 격리 테스트 프로젝트를 만드는 내부 벤치마크 스킬.

## 📌 용도
**vercel 플러그인 자체 개발/벤치마크용 내부 스킬**이다. benchmark-agents의 간소화된 셋업 버전으로, 격리된 테스트 디렉토리를 만들고 플러그인을 설치한 뒤, 크래프팅된 프롬프트로 Claude Code를 WezTerm 패널에 띄우는 데 집중한다.

목표는 현실적이고 기술-중립적인(technology-agnostic) 프롬프트로 스킬 주입을 자극하는 것이다. 프롬프트에 특정 기술(Next.js, Stripe 등)을 절대 언급하지 않고 프로덕트와 기능만 묘사해, 플러그인이 자연어에서 올바른 스킬을 추론하는지 본다.

## 🎯 트리거 조건
- **언제 호출되는가**: 플러그인 개발자가 여러 현실 시나리오 테스트 프로젝트를 빠르게 셋업·기동하려 할 때. macOS + WezTerm 로컬 환경 전제.
- **언제 쓰지 않는가**: 일반 사용자의 실제 Vercel 빌드/배포(commands·MCP 사용), 모니터링·커버리지 리포트가 필요한 정밀 eval(benchmark-agents/vercel-plugin-eval 사용).

## 💻 사용 예시 / 명령어
```bash
# 1. 테스트 디렉토리 생성
BASE=~/dev/vercel-plugin-testing
mkdir -p "$BASE"/{01-slug,02-slug,...}

# 2. 각 디렉토리에 플러그인 설치
for dir in "$BASE"/*/; do
  cd "$dir" && npx add-plugin https://github.com/vercel/vercel-plugin -s project -y 2>&1 | tail -1
done

# 3. WezTerm 패널에서 Claude Code 기동
wezterm cli spawn --cwd /absolute/path/to/test-dir -- /bin/zsh -ic \
  "unset CLAUDECODE; x 'YOUR PROMPT. Link the project to my vercel-labs team so we can deploy it later.' --settings .claude/settings.json; exec zsh"
```

## 🛠️ 제공 도구 / 주요 파라미터
필수 준수 사항:
- `--cwd <절대경로>`로 작업 디렉토리 지정
- `x` 앞에 `unset CLAUDECODE`로 중첩 세션 에러 방지
- `--settings .claude/settings.json` (NOT `--settings project`)
- 외부 `-ic` 문자열은 큰따옴표, 프롬프트는 작은따옴표
- 각 기동 사이 **10초** 대기
- `split-pane`은 ~4패널 후 공간 부족 → 항상 `spawn` 사용

프롬프트 가이드: 특정 기술명 금지, 프로덕트/기능 묘사, 여러 스킬 트리거를 자극하는 야심찬 멀티 기능, 항상 "Link the project to my vercel-labs team..." 추가. 예시 9종(recipe-platform, trivia-game, code-review-bot, conference-tickets 등)과 기대 스킬 표 제공.

## 📦 출처 / 설치 상태
- 플러그인: vercel@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/.claude/skills/benchmark-testing/SKILL.md`
