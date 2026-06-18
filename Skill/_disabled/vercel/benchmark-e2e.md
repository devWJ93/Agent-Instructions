# benchmark-e2e

> vercel-plugin을 현실적 프로젝트로 돌려 스킬 주입·dev 서버 기동·검증·개선 리포트를 한 번에 생성하는 내부 E2E 벤치마크 스킬.

## 📌 용도
**vercel 플러그인 자체 개발/벤치마크용 내부 스킬**이다. 일반 사용자용이 아니라, 야간 자가 개선(overnight self-improvement) 루프를 위한 단일 커맨드 파이프라인을 제공한다.

프로젝트를 생성하고, 스킬 주입을 실행하며, dev 서버를 띄워 동작을 확인하고, 대화 로그를 분석해 실행 가능한 개선 리포트를 만든다. `bun run scripts/benchmark-e2e.ts` 한 줄로 runner → verify → analyze → report 4단계를 순차 실행한다.

(주의: 이 스킬의 runner는 `claude --print`를 사용한다고 명시되어 있는데, 이는 benchmark-agents/vercel-plugin-eval 등 대화형 스킬과 대비되는 자동화 지향 설계다. 이 스킬은 머신 판독용 `report.json`/`run-manifest.json`/`events.jsonl` 산출물을 통해 닫힌 피드백 루프를 만드는 데 초점이 있다.)

## 🎯 트리거 조건
- **언제 호출되는가**: 플러그인 개발자가 9개(또는 quick 모드 3개) 현실 프로젝트에 대해 스킬 커버리지를 자동 측정하고, 야간 반복으로 개선 추세를 추적하려 할 때.
- **언제 쓰지 않는가**: 실제 Vercel 프로덕트 빌드/배포. 이 경우 commands나 vercel MCP를 쓴다.

## 💻 사용 예시 / 명령어
```bash
# 전체 스위트 (9개 프로젝트, ~2-3시간)
bun run scripts/benchmark-e2e.ts

# 빠른 모드 (앞 3개, ~30-45분)
bun run scripts/benchmark-e2e.ts --quick

# 야간 자동화 루프
while true; do bun run scripts/benchmark-e2e.ts; sleep 3600; done
```

| 플래그 | 설명 | 기본값 |
|--------|------|--------|
| `--quick` | 앞 3개 프로젝트만 | `false` |
| `--base <path>` | 기본 디렉토리 변경 | `~/dev/vercel-plugin-testing` |
| `--timeout <ms>` | 프로젝트당 타임아웃 | `900000` (15분) |

## 🛠️ 제공 도구 / 주요 파라미터
| 단계 | 역할 |
|------|------|
| runner | 테스트 디렉토리 생성, 플러그인 설치, `VERCEL_PLUGIN_LOG_LEVEL=trace`로 실행 |
| verify | 패키지 매니저 감지, dev 서버 기동, 200 + 비어있지 않은 HTML 폴링 |
| analyze | `run-manifest.json`으로 JSONL 세션과 프로젝트 매칭, 메트릭 추출 |
| report | `report.md` + `report.json` (스코어카드, 추천 패턴) 생성 |

산출 계약: `run-manifest.json`(runId·프로젝트별 expectedSkills), `events.jsonl`(파이프라인 생명주기 NDJSON), `report.json`(verdict: pass/partial/fail, gaps, suggestedPatterns). 자가 개선 사이클은 Run → gaps 읽기 → suggestedPatterns로 frontmatter 패턴 추가 → 재실행 → verdict가 fail→partial→pass로 수렴하는지 diff.

프롬프트 표 9종: recipe-platform, trivia-game, code-review-bot, conference-tickets, content-aggregator, finance-tracker, multi-tenant-blog, status-page, dog-walking-saas.

## 📦 출처 / 설치 상태
- 플러그인: vercel@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/.claude/skills/benchmark-e2e/SKILL.md`
