# claude-news

> Claude Code 릴리스 노트 + Anthropic 모델/제품 소식을 **한국어로 요약**하는 개인 글로벌 스킬. 새 기능과 주요 변경을 카테고리별로 정리해 보여준다.

## 📌 용도
두 출처의 소식을 모아 한국어로 정리한다.
1. **Anthropic 공식 뉴스** (`https://www.anthropic.com/news`) — **모델 출시/제품 발표만** 필터링
2. **Claude Code 릴리스 노트** (GitHub `anthropics/claude-code`) — 새 기능·주요 변경 위주

개발자 실무와 무관한 항목(정치/정책 성명, 사업 투자, 채용/오피스 확장, 연구 논문)은 소식에서 제외한다. 릴리스 노트는 새 기능·주요 변경만 항목으로 보이고, 버그 수정·기타 개선은 건수만 집계한다.

## 🎯 트리거 조건
- **언제 호출되는가**
  - "Claude 뉴스 / Claude Code 업데이트 알려줘"
  - 최근 릴리스·신기능·모델 소식을 한국어 요약으로 보고 싶을 때
  - `/claude-news`, `/claude-news 5`(개수), `/claude-news <버전>` 호출
- **언제 쓰지 않는가**
  - 특정 코드/프로젝트 질문 (그건 일반 도구나 RAG)
  - Anthropic 외 제공자 소식

## 💻 사용 예시 / 명령어
- `/claude-news` — 기본 선택 로직(10일 이내 우선, 최대 5개)으로 소식+릴리스 요약
- `/claude-news 5` — 최근 릴리스 5개 가져오기 (숫자 인자)
- `/claude-news v2.x.xx` — 특정 버전 릴리스만
- 후속: "A1 자세히" / "3번 더 알려줘" / "MCP 관련만" — 번호로 특정 항목 심화 설명

## 🛠️ 제공 도구 / 주요 파라미터
- **사용 도구**: `WebFetch`(Anthropic 뉴스), `mcp__github__list_releases`(릴리스)
- **인자($ARGUMENTS)**: 숫자=개수 / 버전=특정 릴리스 / 생략=기본 선택 로직
- **기본 선택 로직**: 최근 10개 중 10일 이내 필터 → 5개 미만이면 이전 릴리스로 5개 채움 → 10일 이내=[NEW], 그 외=[최근]
- **분류 규칙**: Added=새 기능, 주요 Changed/Removed/Deprecated=주요 변경(항목), Fixed=버그 수정 N건, Improved/나머지=기타 개선 N건
- **번역 규칙**: 기술 용어(MCP, worktree, hook, slash command)는 원어 유지, Windows/Git Bash 항목은 **[Windows]** 태그
- **번호 체계**: 소식 A1·A2…, 릴리스 항목 1·2·3 연속
- **주의**: body 빈 릴리스 건너뜀, WebFetch 실패 시 소식 생략하고 릴리스만, GitHub API 실패 시 에러 안내·재시도

## 📦 출처 / 설치 상태
- 분류: **개인 글로벌 스킬** (`~/.claude/skills`)
- 경로: `~/.claude/skills/claude-news/SKILL.md`
