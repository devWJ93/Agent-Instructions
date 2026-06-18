# huggingface-datasets

> Hugging Face Dataset Viewer API로 데이터셋 메타데이터·행 페이지네이션·검색·필터·parquet URL을 읽는 스킬.

## 📌 용도
Hugging Face Dataset Viewer API를 사용한 읽기 전용 데이터셋 탐색/추출 워크플로우를 담당한다. subset/split 메타데이터를 가져오고, 행을 페이지네이션하고, 텍스트 검색과 필터(predicate)를 적용하고, parquet URL을 다운로드하고, 크기/통계를 읽는다. 데이터셋 가용성 검증(`/is-valid`)부터 시작해 config·split 해석, 미리보기, 페이지네이션, 검색/필터, parquet/size/statistics 조회까지의 코어 워크플로우를 제공한다. 데이터셋 생성/업로드(Hub UI, `@huggingface/hub` CLI), 에이전트 트레이스(JSONL) 업로드도 다룬다.

## 🎯 트리거 조건
- **언제 호출되는가**: HF 데이터셋의 subset/split 메타데이터 조회, 행 페이지네이션, 텍스트 검색, 필터 적용, parquet URL 다운로드, 크기/통계 읽기 등 Dataset Viewer API 워크플로우. 데이터셋 생성/업로드, 에이전트 세션 트레이스(Claude Code/Codex/Pi)를 데이터셋으로 올리는 작업.
- **언제 쓰지 않는가**: CLI 기반 parquet URL 탐색이나 SQL은 `hf-cli` 스킬의 `hf datasets parquet`/`hf datasets sql` 사용. 데이터셋으로 모델을 학습하는 작업은 학습 전용 스킬.

## 💻 사용 예시 / 명령어
```bash
# 행 페이지네이션 (offset 0-based, length 최대 100)
curl "https://datasets-server.huggingface.co/rows?dataset=stanfordnlp/imdb&config=plain_text&split=train&offset=0&length=100"
curl "https://datasets-server.huggingface.co/rows?dataset=stanfordnlp/imdb&config=plain_text&split=train&offset=100&length=100"

# 데이터셋 생성/업로드 (의존성 최소 CLI)
export HF_TOKEN=<your_hf_token>
npx -y @huggingface/hub upload datasets/<namespace>/<repo> ./local/parquet-folder data
npx -y @huggingface/hub upload datasets/<namespace>/<repo> ./local/parquet-folder data --private

# 에이전트 트레이스 업로드 (기본 private)
hf repos create <namespace>/<repo> --type dataset --private --exist-ok
hf upload <namespace>/<repo> ~/.codex/sessions codex/<project-or-cwd> --type dataset
```

## 🛠️ 제공 도구 / 주요 파라미터
Base URL: `https://datasets-server.huggingface.co` (GET, 쿼리 파라미터 URL 인코딩, gated/private는 `Authorization: Bearer <HF_TOKEN>`).

엔드포인트:
- `/is-valid?dataset=<ns/repo>` — 가용성 검증
- `/splits?dataset=` — subset·split 목록
- `/first-rows?dataset=&config=&split=` — 첫 행 미리보기
- `/rows?dataset=&config=&split=&offset=&length=` — 행 페이지네이션(length 최대 100)
- `/search?...&query=<text>` — 문자열 컬럼 텍스트 검색
- `/filter?...&where=<predicate>&orderby=<sort>` — predicate 필터
- `/parquet?dataset=` — parquet shard 목록
- `/size?dataset=` — 크기 총계
- `/statistics?dataset=&config=&split=` — 컬럼 통계
- `/croissant?dataset=` — Croissant 메타데이터

페이지네이션 응답 필드 `num_rows_total`, `num_rows_per_page`, `partial`로 연속 처리. 모든 검색/필터는 읽기 전용·부작용 없음.

에이전트 트레이스: Claude Code(`~/.claude/projects`), Codex(`~/.codex/sessions`), Pi(`~/.pi/agent/sessions`)의 raw `.jsonl`을 업로드하면 Hub가 자동 감지·`Traces` 태깅·트레이스 뷰어 활성화. 프롬프트·경로·시크릿·PII 가능성 때문에 기본 private 권장, 프로젝트/cwd 별로 중첩 저장.

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-datasets/SKILL.md`
