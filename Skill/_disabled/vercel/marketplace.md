# /marketplace (커맨드)

> Vercel Marketplace 통합 발견·설치 — 데이터베이스, CMS, 인증, 결제, 옵저버빌리티 등 서비스를 가이드 셋업과 로컬 검증으로 추가.

## 📌 용도
**사용자용 슬래시 커맨드**다. Vercel Marketplace 카탈로그에서 통합(integration)을 발견·설치하고 가이드에 따라 코드를 적용한다. apply-guide 루프(Discover → Select → Guide → Install → env 검증 → 코드 적용 → drain 검증 → 로컬 헬스체크)를 따른다.

## 🎯 트리거 조건
- **언제 호출되는가**: `/marketplace` 또는 `/marketplace <name>` 호출 시. DB(Neon/PlanetScale/Supabase), 캐시(Upstash), 인증(Clerk/Auth0), CMS(Sanity/Contentful), 결제(Stripe), 모니터링(Datadog/Sentry/Axiom) 등을 프로젝트에 붙일 때.
- **언제 쓰지 않는가**: 이미 설치된 통합의 env 동기화만 필요할 때(`/env pull`), 단순 배포(`/deploy`).

## 💻 사용 예시 / 명령어
```bash
# 1. 카탈로그 검색
vercel integration discover
vercel integration discover --category databases
vercel integration list           # 이미 설치된 통합

# 3. 가이드 가져오기 (프레임워크별)
vercel integration guide <name> --framework <fw>

# 4. 설치 (env 변수·로그/트레이스 drain 자동 프로비저닝)
vercel integration add <name>
vercel integration open <name>    # 대시보드 폴백

# 5. env 검증 (이름만)
vercel env ls
vercel env pull .env.local --yes

# 6. SDK 설치 + 코드 스캐폴딩
npm install <sdk-package>
```

## 🛠️ 제공 도구 / 주요 파라미터
옵저버빌리티 통합(Datadog/Sentry/Axiom/Honeycomb)은 drain 검증 단계가 추가된다. 데이터 타입 분리: **Logs/Traces**는 설치 시 네이티브 drain 자동 생성, **Speed Insights/Web Analytics**는 REST API/대시보드로 별도 수동 drain 필요. drain 생성·테스트(`curl POST /v1/drains/<id>/test`)와 페이로드 서명 검증(`x-vercel-signature` HMAC-SHA1)은 observability 스킬 참조. 로컬 헬스체크: DB는 `SELECT 1`, 인증은 리다이렉트, 캐시는 set/get 라운드트립. 결과 블록(Integration, Status, Package, Env Vars, Drain, Health Check, Files Changed) 출력.

## 📦 출처 / 설치 상태
- 플러그인: vercel@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/commands/marketplace.md`
