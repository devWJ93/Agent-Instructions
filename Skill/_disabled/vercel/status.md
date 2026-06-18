# /status (커맨드)

> 현재 Vercel 프로젝트의 상태 진단(Doctor) — 최근 배포, 링크 정보, env 개요, 도메인, 빌드 상태, 옵저버빌리티 점검. 읽기 전용.

## 📌 용도
**사용자용 슬래시 커맨드**다. 프로젝트 종합 헬스체크(Doctor)를 수행한다. 배포 상태, 환경 변수 구성, 도메인, 빌드 상태, 옵저버빌리티 자세를 진단한다. 파괴적 작업이 전혀 없는 읽기 전용 커맨드이며, 가능하면 MCP 읽기를 우선하고 CLI를 폴백으로 쓴다.

## 🎯 트리거 조건
- **언제 호출되는가**: `/status` 호출 시. 프로젝트의 최근 배포·env·도메인·옵저버빌리티 현황을 한눈에 점검할 때.
- **언제 쓰지 않는가**: 실제 배포(`/deploy`)나 env 변경(`/env`)이 목적일 때. (이 커맨드는 진단만 함)

중요: 프로젝트가 링크되지 않았으면 `vercel link` 안내 후 중단하며, **GitHub Actions(`gh run list`) 등 다른 CI로 폴백하지 않는다** — Vercel CLI가 유일한 권위 소스다.

## 💻 사용 예시 / 명령어
```bash
vercel ls                          # 최근 배포 (--limit 미지원)
vercel inspect <latest-url>        # 빌드 시간, 함수 수, 리전, 프레임워크, Node 버전
vercel env ls                      # 환경별 카운트 (값 미출력)
vercel domains ls                  # 도메인 DNS/SSL 상태
# vercel.json 읽어 프레임워크·빌드·함수·rewrites·cron 요약

# 옵저버빌리티 진단
curl -s -H "Authorization: Bearer $VERCEL_TOKEN" \
  "https://api.vercel.com/v1/drains?teamId=$TEAM_ID" | jq '.drains | length'
vercel metrics schema --format=json
```

## 🛠️ 제공 도구 / 주요 파라미터
진단 항목: ① 최근 배포 5건(URL/state/target/생성시각) ② 최신 배포 검사 ③ 환경 변수 카운트(값 미출력) ④ 도메인 DNS/SSL ⑤ `vercel.json` 하이라이트 ⑥ 옵저버빌리티(drains 설정·에러 상태, `@vercel/analytics`·`@vercel/speed-insights` 설치 여부, drain 서명 시크릿 `DRAIN_SECRET` 점검). drain 서명 검증 코드(HMAC-SHA1, raw body 기준, timing-safe 비교) 예시 제공. 옵저버빌리티 결정 매트릭스와 Hobby 플랜 폴백 가이드 포함. 최종 "Vercel Doctor" 리포트 블록 출력.

## 📦 출처 / 설치 상태
- 플러그인: vercel@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/commands/status.md`
