# benchmark-sandbox

> 로컬 WezTerm 대신 Vercel Sandbox(원격 microVM)에서 eval 시나리오를 돌리는 내부 벤치마크 스킬.

## 📌 용도
**vercel 플러그인 자체 개발/벤치마크용 내부 스킬**이다. benchmark-agents가 로컬 macOS 터미널 패널을 쓰는 반면, 이 스킬은 Vercel Sandbox(node24 기반 Firecracker microVM)에서 eval을 원격·병렬로 실행한다.

각 샌드박스는 Claude Code + Vercel CLI + agent-browser가 새로 설치되고, 로컬 vercel-plugin이 업로드되며, **3단계 파이프라인**을 돈다:
- **Phase 1 (BUILD)**: `--dangerously-skip-permissions --debug`로 앱 빌드
- **Phase 2 (VERIFY)**: 후속 세션이 `agent-browser`로 유저 스토리를 검증·수정 (20분 타임아웃)
- **Phase 3 (DEPLOY)**: 세 번째 세션이 vercel-labs에 링크 후 `vercel deploy`, 빌드 에러 최대 3회 수정

각 단계 후 haiku 모델(`claude -p --json-schema --model haiku`)로 구조화 JSON 스코어링을 한다. 스킬은 3단계 전체에 걸쳐 추적된다.

## 🎯 트리거 조건
- **언제 호출되는가**: 자동화된 병렬 커버리지 + 검증 + 배포 런이 필요할 때. 로컬 자원 한계를 넘어 동시 다수(최대 10개) 시나리오를 돌리고, 자동 agent-browser 검증과 영구 `*.vercel.app` URL이 필요할 때.
- **언제 쓰지 않는가**: 수동 eval/반복 루프(이 경우 benchmark-agents), 또는 일반 사용자의 실제 배포 작업.

## 💻 사용 예시 / 명령어
```bash
# 기본 시나리오 + 전체 3단계 파이프라인
bun run .claude/skills/benchmark-sandbox/run-eval.ts

# 동적 시나리오 JSON 파일 사용 (권장)
bun run .claude/skills/benchmark-sandbox/run-eval.ts --scenarios-file /tmp/scenarios.json

# 야간 keep-alive (공개 URL 유지)
bun run .claude/skills/benchmark-sandbox/run-eval.ts --keep-alive --keep-hours 8

# 빌드만 (검증·배포 생략)
bun run .claude/skills/benchmark-sandbox/run-eval.ts --skip-verify --skip-deploy
```

## 🛠️ 제공 도구 / 주요 파라미터
| 플래그 | 기본값 | 설명 |
|--------|--------|------|
| `--concurrency N` | 5 | 병렬 샌드박스 수 (최대 10) |
| `--timeout MS` | 1800000 (30분) | 단계당 타임아웃 |
| `--keep-alive` / `--keep-hours N` | off / 8 | eval 후 샌드박스 유지 |
| `--skip-verify` / `--skip-deploy` | off | 검증/배포 단계 생략 |
| `--scenarios a,b,c` / `--scenarios-file path` | all / — | 슬러그 필터 / JSON 파일 로드 |

핵심 환경 사실: 홈은 `/home/vercel-sandbox`(NOT `/home/user`), SDK는 `@vercel/sandbox@1.8.0`(v2 beta는 404), 스냅샷은 파일+npm 글로벌 보존, 포트 노출은 `ports:[3000]` → `https://...vercel.run`. 파일 업로드는 `sandbox.writeFiles()`(heredoc 금지), 타임아웃은 `AbortSignal.timeout(ms)`. 산출물은 `~/dev/vercel-plugin-testing/sandbox-results/<run-id>/`에 result.json·source.tar.gz·report.md.

## 📦 출처 / 설치 상태
- 플러그인: vercel@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/.claude/skills/benchmark-sandbox/SKILL.md`
