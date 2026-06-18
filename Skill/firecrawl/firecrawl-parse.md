# firecrawl-parse

> 로컬 파일(PDF/DOCX/XLSX/HTML 등)을 클린 마크다운으로 변환해 디스크에 저장하는 스킬.

## 📌 용도
로컬 문서를 디스크상의 클린 마크다운으로 변환한다. 지원 포맷: **PDF, DOCX, DOC, ODT, RTF, XLSX, XLS, HTML/HTM/XHTML**. AI 요약 생성, 문서 내용 기반 질문 답변 같은 고급 옵션도 제공한다. 로컬 파일을 다룰 때는 `scrape`보다 이 스킬을 선호한다.

- 디스크에 파일이 있고(URL 아님) 그 텍스트를 마크다운으로 원할 때
- 사용자가 PDF/DOCX를 주며 내용/요약을 물을 때
- 소스가 URL이면 대신 `scrape` 사용

## 🎯 트리거 조건
- **언제 호출되는가**: "이 PDF 파싱해줘", "이 문서 변환", "이 파일 읽어줘", "텍스트 추출", 또는 (URL이 아닌) 로컬 파일 경로가 제공될 때.
- **언제 쓰지 않는가**: 소스가 URL일 때(→ `firecrawl-scrape`).

## 💻 사용 예시 / 명령어
결과는 항상 `-o`로 `.firecrawl/`에 저장(파싱 문서는 수백 KB가 될 수 있어 stdout 스트리밍 시 컨텍스트 폭증). `.gitignore`에 추가.
```bash
mkdir -p .firecrawl

# 파일 → 마크다운
firecrawl parse ./paper.pdf -o .firecrawl/paper.md

# AI 요약
firecrawl parse ./paper.pdf -S -o .firecrawl/paper-summary.md

# 문서에 대한 질문
firecrawl parse ./paper.pdf -Q "What are the main conclusions?" \
  -o .firecrawl/paper-qa.md
```
이후 `head`/`grep`/`rg` 또는 증분 읽기 사용 — 통째로 로드 금지.

## 🛠️ 제공 도구 / 주요 파라미터
- 사용 도구: `Bash(firecrawl *)`, `Bash(npx firecrawl *)`

| 옵션 | 설명 |
| --- | --- |
| `-S, --summary` | AI 생성 요약 |
| `-Q, --query <prompt>` | 파싱 내용에 대한 질문 |
| `-o, --output <path>` | 출력 파일 경로 — **항상 사용** |
| `-f, --format <fmt>` | `markdown`(기본), `html`, `summary` |
| `--timeout <ms>` | 파싱 작업 타임아웃 |
| `--timing` | 요청 소요 시간 표시 |

**팁**: 공백 포함 경로는 따옴표(`"./My Doc.pdf"`). 최대 업로드 크기 **50 MB**/파일. 크레딧: PDF 페이지당 ~1, HTML은 1 고정. 재파싱 전 `.firecrawl/` 확인. 배치 처리 시 `firecrawl credit-usage`로 잔액 확인.

## 📦 출처 / 설치 상태
- 플러그인: firecrawl@claude-plugins-official
- 활성화: ON
- 관련 MCP: firecrawl은 CLI 기반(`firecrawl parse`)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/firecrawl/1.0.9/skills/firecrawl-parse/SKILL.md`
