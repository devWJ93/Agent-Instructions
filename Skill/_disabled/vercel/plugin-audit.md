# plugin-audit

> 실제 Claude Code 세션 로그를 분석해 vercel-plugin 스킬 주입 성능과 패턴 커버리지 갭을 진단하는 내부 감사 스킬.

## 📌 용도
**vercel 플러그인 자체 개발/감사용 내부 스킬**이다. 실제 프로젝트에서 vercel-plugin의 스킬 주입이 얼마나 잘 작동했는지 사후 감사한다.

Claude Code 대화 로그(JSONL)에서 도구 호출을 추출하고, 실제 입력에 대해 훅 매칭을 테스트하며, 패턴 커버리지 갭을 식별하고, 플러그인 캐시의 staleness(설치 버전 vs 개발 버전)를 확인한다. "플러그인 스킬 주입을 실제 프로젝트에서 감사/테스트/조사해줘"라는 요청에 사용한다.

## 🎯 트리거 조건
- **언제 호출되는가**: 플러그인 개발자가 실제 세션에서 어떤 스킬이 주입되어야 했는데 안 됐는지, 어떤 패턴이 누락됐는지 진단할 때.
- **언제 쓰지 않는가**: 일반 사용자의 Vercel 작업, 또는 라이브 eval 세션 실행(이 경우 vercel-plugin-eval/benchmark-* 사용 — 이 스킬은 이미 존재하는 로그를 사후 분석).

## 💻 사용 예시 / 명령어
```bash
# 1. 대상 프로젝트의 대화 로그 찾기
ls -lt ~/.claude/projects/-Users-*-<project-name>/*.jsonl

# 5. 플러그인 캐시 staleness 확인
diff <(grep 'pattern' skills/<skill>/SKILL.md) \
     <(grep 'pattern' ~/.claude/plugins/cache/.../skills/<skill>/SKILL.md)
```

훅 매칭 테스트는 셸 아웃 대신 파이프라인 함수를 직접 import한다:
```js
import { loadSkills, matchSkills } from "./hooks/pretooluse-skill-inject.mjs";
import { createLogger } from "./hooks/logger.mjs";
// loadSkills() 1회 호출 후, 각 도구 호출마다 matchSkills(toolName, toolInput, compiledSkills)
```

## 🛠️ 제공 도구 / 주요 파라미터
워크플로우 5단계:
1. **로그 위치 파악** — 프로젝트 절대경로의 슬래시를 하이픈으로 치환한 JSONL 경로
2. **도구 호출 추출** — `message.content[]`의 `type: "tool_use"` 블록에서 name·input 추출, 도구별(Bash/Read/Write/Edit) 그룹화
3. **훅 매칭 테스트** — exported 함수 직접 호출로 정확한 매치 결과
4. **갭 식별** — path 패턴 갭, bash 패턴 갭, dedup 마스킹, budget/cap 드롭(12KB 예산·3스킬 상한)
5. **캐시 staleness** — `installed_plugins.json`의 버전·git SHA 확인

리포트: 세션 요약, 매치 매트릭스(도구×스킬), 커버리지 갭(추천 패턴 추가안), dedup 타임라인, 캐시 상태(diff). 참조: `references/log-format.md`, `scripts/batch-match.mjs`.

## 📦 출처 / 설치 상태
- 플러그인: vercel@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/.claude/skills/plugin-audit/SKILL.md`
