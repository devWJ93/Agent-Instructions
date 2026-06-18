# release

> vercel-plugin을 릴리스 — 게이트 실행, 버전 범프, 아티팩트 생성, 커밋, 푸시까지 처리하는 내부 릴리스 스킬.

## 📌 용도
**vercel 플러그인 자체 개발/배포용 내부 스킬**이다. 일반 사용자의 앱 배포가 아니라, vercel-plugin 프로젝트 자체의 엔드투엔드 릴리스 워크플로우를 수행한다.

"release", "ship", "bump and push", "cut a release" 요청 시 사용한다. 사전 게이트를 통과시키고, 시맨틱 버전을 올리고, 생성 아티팩트를 재빌드한 뒤, 변경 전체를 커밋·푸시한다.

## 🎯 트리거 조건
- **언제 호출되는가**: 플러그인 개발자가 vercel-plugin의 새 버전을 릴리스하려 할 때.
- **언제 쓰지 않는가**: 사용자 앱을 Vercel에 배포할 때(이 경우 `/deploy` 커맨드 사용 — 이 스킬은 플러그인 패키지 자체의 릴리스).

## 💻 사용 예시 / 명령어
```bash
# 1. 사전 게이트 (병렬 실행, 하나라도 실패하면 중단)
bun run typecheck          # tsc --noEmit on hooks/src
bun test                   # 전체 테스트
bun run validate           # 스킬 frontmatter + manifest 무결성

# 4. 생성 아티팩트 재빌드 (hooks tsup + manifest)
bun run build

# 5. 스테이지·커밋·푸시
git add -A
git commit -m "<summary>; bump to <new-version>"
git push
```

## 🛠️ 제공 도구 / 주요 파라미터
버전 범프 기준:
| 범프 | 시점 |
|------|------|
| patch | 버그 수정, 테스트/픽스처 갱신, 문서 (기본값) |
| minor | 새 스킬, 새 훅, 새 기능 |
| major | 훅 API 또는 스킬 맵의 파괴적 변경 |

- **버전 진실 소스**: `.plugin/plugin.json`의 `version` 필드 (동기화할 package.json 버전 없음)
- 커밋 메시지 컨벤션: 서술적 요약 + `; bump to X.Y.Z`
- pre-commit 훅이 typecheck·hooks 재컴파일을 자동 재실행. 실패 시 수정 후 **새 커밋** 생성(amend 금지)
- 체크리스트: typecheck/test/validate 통과 → plugin.json 버전 범프 → `bun run build` 성공 → 전체 변경 커밋 → main 푸시

## 📦 출처 / 설치 상태
- 플러그인: vercel@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/.claude/skills/release/SKILL.md`
