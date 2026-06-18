# /bootstrap (커맨드)

> Vercel 연동 리소스로 저장소를 부트스트랩 — 사전 점검, 통합 프로비저닝, env 키 검증 후 db/dev 시작 명령을 안전하게 실행.

## 📌 용도
**사용자용 슬래시 커맨드**다. 신규이거나 부분 설정된 저장소를 결정론적 부트스트랩 플로우로 셋업한다. Vercel CLI 인증·링크 확인 → DB 등 리소스 프로비저닝(Vercel 관리형 Neon 우선) → 시크릿/env 셋업 → env 키 검증을 모두 통과한 뒤에야 `db:*`/`dev` 명령을 실행한다.

핵심 안전 규칙: Vercel 링크 완료 + env 키 검증 전에는 `db:push`/`db:migrate`/`db:seed`/`dev`를 실행하지 않으며, 시크릿 값을 터미널/로그/요약에 절대 출력하지 않는다.

## 🎯 트리거 조건
- **언제 호출되는가**: `/bootstrap` 호출 시. Vercel과 연동된 새 프로젝트의 초기 환경(DB·인증·env)을 한 번에 셋업하고 싶을 때.
- **언제 쓰지 않는가**: 이미 셋업이 끝난 프로젝트의 단순 배포(`/deploy`)나 env 관리(`/env`)만 필요할 때.

## 💻 사용 예시 / 명령어
```bash
# 사전 점검
vercel --version
vercel whoami
# .vercel/project.json 으로 링크 확인, 없으면:
vercel teams ls
vercel projects ls --scope <team>
vercel link --yes --scope <team> --project <project>

# 로컬 env 템플릿 (.env.example → .env.sample → .env.template 우선순위)
cp .env.example .env.local

# Postgres 프로비저닝 (Vercel 관리형 Neon 우선)
vercel integration guide neon
vercel integration add neon --scope <team>
vercel env ls
vercel env pull .env.local --yes

# AUTH_SECRET 생성 (값 출력 없이 Vercel에 저장)
AUTH_SECRET="$(node -e "console.log(require('node:crypto').randomBytes(32).toString('base64url'))")"
printf "%s" "$AUTH_SECRET" | vercel env add AUTH_SECRET development preview production
unset AUTH_SECRET
vercel env pull .env.local --yes

# 검증 후 앱 부트스트랩
npm run db:push
npm run db:seed
npm run dev
```

## 🛠️ 제공 도구 / 주요 파라미터
실행 순서: ① 사전 점검·링크 → ② 리소스 프로비저닝(Vercel 관리형 우선, 프로바이더 CLI는 폴백) → ③ 시크릿/env 셋업 → ④ env 키 검증(`comm`으로 템플릿 키 vs `.env.local` 키 비교, 이름만) → ⑤ db/dev 실행. 검증 단계마다 체크포인트 확인 후 실패 시 정확한 실패 단계와 해결책 보고. 최종 부트스트랩 요약(Linked Project, Resource Path, Env Keys, Secrets, Migration Status, Dev Result) 출력.

## 📦 출처 / 설치 상태
- 플러그인: vercel@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/commands/bootstrap.md`
