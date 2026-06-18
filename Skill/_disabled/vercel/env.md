# /env (커맨드)

> Vercel 환경 변수 관리 — list, pull, add, remove, diff. Vercel과 로컬 개발 환경 간 env 변수 동기화. 시크릿 값은 절대 출력하지 않음.

## 📌 용도
**사용자용 슬래시 커맨드**다. 현재 Vercel 프로젝트의 환경 변수를 관리하되, 시크릿 누출을 막는 안전 가드를 둔다. **🔒 Never-Echo-Secrets 규칙**: env 변수 **값**은 출력/요약/대화에 절대 나타나지 않으며, 이름·환경·메타데이터(생성일·타입)만 표시한다.

## 🎯 트리거 조건
- **언제 호출되는가**: `/env`, `/env pull`, `/env add <NAME>`, `/env rm <NAME>`, `/env diff` 등으로 env 변수를 조회·동기화·추가·삭제·비교할 때.
- **언제 쓰지 않는가**: 배포(`/deploy`)나 통합 설치(`/marketplace`)가 주 목적일 때. (단 이들 작업 후속으로 자주 함께 쓰임)

## 💻 사용 예시 / 명령어
```bash
# list / ls / (인자 없음) — 이름만 표시
vercel env ls
vercel env ls production

# pull — .env.local로 다운로드
vercel env pull .env.local
vercel env pull .env.local --environment=production
vercel env pull .env.local --yes

# add — 값은 대화형으로 입력(인자로 echo 금지)
vercel env add MY_SECRET
echo "secret-value" | vercel env add MY_SECRET production preview development
vercel env add MY_SECRET --sensitive

# rm / remove
vercel env rm MY_SECRET production
vercel env rm MY_SECRET

# diff — 로컬 vs Vercel 키 이름 비교(값 미열람)
grep -v '^#' .env.local | grep '=' | cut -d'=' -f1 | sort
```

## 🛠️ 제공 도구 / 주요 파라미터
| 인자 | 동작 | 파괴적? |
|------|------|---------|
| `list`/`ls`/(없음) | 환경별 변수 이름 목록 | No |
| `pull` | 로컬 `.env.local`로 다운로드 | No (로컬 파일 덮어씀) |
| `add <NAME>` | 새 변수 추가 | 프로덕션이면 Yes |
| `rm`/`remove <NAME>` | 변수 삭제 | **Yes** |
| `diff` | 로컬 vs Vercel 키 이름 비교 | No |

프로덕션 변경(add/rm)은 명시적 확인 필요. Git 브랜치 오버라이드(`--git-branch=staging`), 환경 스코핑(Production/Preview/Development) 설명. 주의: `vercel env pull`은 파일 전체를 덮어쓰므로 커스텀 변수는 백업·재추가 필요. mutation 후 `vercel env ls`로 검증.

## 📦 출처 / 설치 상태
- 플러그인: vercel@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/commands/env.md`
