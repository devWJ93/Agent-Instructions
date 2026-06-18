# /deploy (커맨드)

> 현재 프로젝트를 Vercel에 배포 — 사전 안전 점검, 프로덕션 명시 확인, 배포 후 검증 포함. 인자로 "prod"/"production" 전달 시 프로덕션 배포.

## 📌 용도
**사용자용 슬래시 커맨드**다. Vercel CLI로 현재 프로젝트를 배포한다. 기본은 프리뷰(preview) 배포이고, `$ARGUMENTS`에 "prod"/"production"이 있으면 프로덕션 배포다. 프로덕션은 실제 사용자에게 영향을 주므로 명시적 "yes" 확인 없이는 진행하지 않는다.

## 🎯 트리거 조건
- **언제 호출되는가**: `/deploy`(프리뷰) 또는 `/deploy prod`(프로덕션) 호출 시. 코드를 Vercel에 올려 URL을 받고 싶을 때.
- **언제 쓰지 않는가**: env 변수만 관리하려 할 때(`/env`), 프로젝트 상태만 보려 할 때(`/status`), 초기 셋업이 안 된 신규 저장소(`/bootstrap` 먼저).

## 💻 사용 예시 / 명령어
```bash
# 프리뷰 배포 (기본 — 격리된 고유 URL)
vercel
vercel deploy

# 프로덕션 배포 (명시적 확인 필요)
vercel --prod
vercel --prod --force      # 캐시 스킵 강제 재배포

# 배포 검사
vercel inspect <deployment-url>
vercel ls
vercel logs <deployment-url> --follow
git rev-parse --short HEAD   # 요약용 커밋 SHA
```

## 🛠️ 제공 도구 / 주요 파라미터
사전 점검: CLI 존재, `.vercel/project.json` 링크, 모노레포 감지(`turbo.json`/`pnpm-workspace.yaml`), `git status --porcelain` 미커밋 변경 경고, 프로덕션 전 옵저버빌리티 점검(drains 설정 여부, 에러 드레인 상태, 에러 모니터링).

검증: 배포 상태(READY/ERROR/BUILDING/QUEUED) 추출, 실패 시 빌드 로그 마지막 50줄에서 error/ERR!/FATAL 강조. 프로덕션은 READY 후 60초 대기 후 `vercel logs --level error --since 1h`로 런타임 에러 스캔. 배포 결과 블록(URL, Target, Status, Commit, Framework, Build Duration) 출력. 500/타임아웃 에러 해석 표 제공.

## 📦 출처 / 설치 상태
- 플러그인: vercel@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/commands/deploy.md`
