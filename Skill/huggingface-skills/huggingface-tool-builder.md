# huggingface-tool-builder

> Hugging Face API 데이터를 fetch·가공·결합하는 재사용 가능한 CLI 스크립트/유틸리티를 만드는 스킬.

## 📌 용도
Hugging Face API를 사용해 데이터를 fetch·enrich·process하는 재사용 가능한 명령줄 스크립트와 유틸리티를 만든다. API 직접 호출과 `hf` CLI 도구 둘 다 사용하며, 체이닝·파이핑·중간 처리가 도움이 될 때 특히 유용하다. 작업이 반복/자동화되거나 여러 API 호출을 연결/결합해야 할 때 적합하다. 모델/데이터셋 카드는 repo에서 직접 접근한다. 결과의 형태(shape)를 먼저 조사한 뒤 최종 설계를 확정하고, 합성성이 유리한 곳에 파이핑/체이닝을 활용하되 가능한 한 단순한 해법을 선호한다.

## 🎯 트리거 조건
- **언제 호출되는가**: HF API 데이터를 쓰는 도구/스크립트를 만들거나, API 호출을 연결/결합하는 작업, 반복·자동화될 작업. 데이터를 fetch/enrich/process하는 재사용 스크립트가 필요할 때.
- **언제 쓰지 않는가**: 일회성 단일 명령 실행(hf-cli로 충분), 학습/평가/배포 같은 특정 워크플로우(전용 스킬). Dataset Viewer API 단순 조회는 huggingface-datasets.

## 💻 사용 예시 / 명령어
```bash
# HF_TOKEN을 Authorization 헤더로 (높은 rate limit + 권한)
curl -H "Authorization: Bearer ${HF_TOKEN}" https://huggingface.co/api/models

# OpenAPI 스펙 탐색 (직접 읽지 말고 jq로 추출 — 파일이 너무 큼)
curl -s "https://huggingface.co/.well-known/openapi.json" | jq '.paths | keys | sort'
curl -s "https://huggingface.co/.well-known/openapi.json" | jq '.paths["/api/models"]'

# 합성성 — 파이핑 예시
references/baseline_hf_api.sh 25 | jq -r '.[].id' | references/hf_enrich_models.sh \
  | jq -s 'sort_by(.downloads) | reverse | .[:10]'
references/baseline_hf_api.sh 50 | jq '[.[] | {id, downloads}] | sort_by(.downloads) | reverse | .[:10]'
printf '%s\n' openai/gpt-oss-120b meta-llama/Meta-Llama-3.1-8B \
  | references/hf_model_card_frontmatter.sh | jq -s 'map({id, license, has_extra_gated_prompt})'
```

## 🛠️ 제공 도구 / 주요 파라미터
스크립트 작성 규칙:
- 모든 스크립트는 `--help`로 입출력 설명 제공.
- 비파괴적 스크립트는 인계 전 테스트.
- 셸 스크립트 선호, 복잡도/필요 시 Python 또는 TSX.
- `HF_TOKEN` 환경변수를 Authorization 헤더로 사용.
- 최종 설계 전 API 결과 shape 조사, 합성성 유리한 곳에 파이핑/체이닝, 가능한 단순하게.
- 완성 후 사용 예시 공유.

주요 엔드포인트: `/api/datasets`, `/api/models`, `/api/spaces`, `/api/collections`, `/api/daily_papers`, `/api/notifications`, `/api/settings`, `/api/whoami-v2`, `/api/trending`, `/oauth/userinfo`. (전체 ~160개는 OpenAPI 스펙을 jq로 조회)

참조 예제 스크립트(skill 디렉토리 기준):
- `references/hf_model_papers_auth.sh` — trending → 모델 메타데이터 → 모델카드 파싱 체이닝 + auth 위생
- `references/find_models_by_paper.sh` — `--token` 옵션, arXiv 접두 검색 retry 경로
- `references/hf_model_card_frontmatter.sh` — `hf` CLI로 카드 다운로드, YAML frontmatter 추출, NDJSON 출력
- `references/baseline_hf_api.{sh,py,tsx}` — 초간단 raw JSON 베이스라인 (3개 언어)
- `references/hf_enrich_models.sh` — stdin의 모델 ID → 메타데이터 → 라인당 1 JSON (스트리밍 파이프)

`hf` CLI 서브커맨드 그룹: auth, buckets, cache, collections, datasets, discussions, download, endpoints, env, extensions, jobs, models, papers, repos, skills, spaces, sync, upload, webhooks 등 (deprecated `huggingface-cli` 대체).

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-tool-builder/SKILL.md`
