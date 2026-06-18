# huggingface-papers

> HF paper page를 마크다운으로 읽고, papers API로 저자·연결된 모델/데이터셋/스페이스 등 구조화 메타데이터를 조회하는 스킬.

## 📌 용도
Hugging Face Paper Pages(hf.co/papers, arXiv 기반)의 논문을 마크다운으로 가져와 읽고, papers API로 저자명·HF 사용자명·연결된 모델/데이터셋/스페이스·GitHub repo·프로젝트 페이지·요약(abstract)·AI 생성 요약·업보트 같은 구조화 메타데이터를 조회한다. 사용자가 HF paper page URL, arXiv URL/ID를 공유하거나, AI 연구 논문을 요약·설명·분석해 달라고 할 때 사용한다. 마크다운 콘텐츠는 arxiv.org/html 버전에 의존하며, 없으면 HF paper page HTML로 폴백한다. 공개 paper page는 인증 불필요, 쓰기(claim/index/links) 엔드포인트만 토큰 필요.

## 🎯 트리거 조건
- **언제 호출되는가**: HF paper page URL(`https://huggingface.co/papers/2602.08025`), `.md` URL, arXiv URL(`abs`/`pdf`), arXiv ID(`2602.08025`, 버전 접미사 포함)를 공유할 때. AI 연구 논문을 요약·설명·분석해 달라고 할 때.
- **언제 쓰지 않는가**: 논문을 새로 발행/생성하거나 연구 아티클을 만드는 작업(huggingface-paper-publisher). 단순 읽기/분석이 아니라 모델/데이터셋에 논문을 연결·게시하는 워크플로우.

## 💻 사용 예시 / 명령어
Paper ID 파싱: 어떤 입력이든 arXiv ID로 추출 (예: `.../papers/2602.08025.md` → `2602.08025`, `2602.08025v1` → 버전 유지).

```bash
# 마크다운으로 논문 읽기 (권장: .md 엔드포인트)
curl -s "https://huggingface.co/papers/{PAPER_ID}.md"
curl -s -H "Accept: text/markdown" "https://huggingface.co/papers/{PAPER_ID}"

# 구조화 메타데이터 (JSON)
curl -s "https://huggingface.co/api/papers/{PAPER_ID}"

# 논문에 연결된 모델/데이터셋/스페이스 찾기
curl "https://huggingface.co/api/models?filter=arxiv:{PAPER_ID}"
curl "https://huggingface.co/api/datasets?filter=arxiv:{PAPER_ID}"
curl "https://huggingface.co/api/spaces?filter=arxiv:{PAPER_ID}"

# Daily Papers / 목록 / 검색
curl -s -H "Authorization: Bearer $HF_TOKEN" "https://huggingface.co/api/daily_papers?p=0&limit=20&sort=trending"
curl -s -H "Authorization: Bearer $HF_TOKEN" "https://huggingface.co/api/papers?cursor={CURSOR}&limit=20"
curl -s -H "Authorization: Bearer $HF_TOKEN" "https://huggingface.co/api/papers/search?q=vision+language&limit=20"
```

## 🛠️ 제공 도구 / 주요 파라미터
API base: `https://huggingface.co`.

읽기 엔드포인트:
- `GET /papers/{ID}.md` — 마크다운 본문 (권장)
- `GET /api/papers/{ID}` — 구조화 JSON (authors, summary, AI summary, projectPage, githubRepo, upvotes 등)
- `GET /api/models|datasets|spaces?filter=arxiv:{ID}` — 연결된 아티팩트
- `GET /api/daily_papers` — `p`, `limit`(1~100), `date`(RFC3339), `week`(ISO), `month`, `submitter`, `sort`(publishedAt|trending)
- `GET /api/papers` — `cursor`, `limit`
- `GET /api/papers/search` — `q`(max 250), `limit`(1~120). 제목·저자·본문 하이브리드 시맨틱+전문 검색

쓰기 엔드포인트(`Authorization: Bearer $HF_TOKEN` 필요):
- `POST /api/settings/papers/claim` — 저자권 주장 (`paperId`, `claimAuthorId`, `targetUserId`)
- `POST /api/papers/index` — arXiv ID 인덱싱 (패턴 `^\d{4}\.\d{4,5}$`)
- `POST /api/papers/{paperId}/links` — projectPage/githubRepo/organizationId 갱신

에러: 404(아직 미인덱싱), Paper ID 미발견(버전 접미사 확인). 폴백: HF paper page → arXiv abs/pdf.

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-papers/SKILL.md`
