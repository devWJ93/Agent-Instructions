# firecrawl-agent

> AI가 복잡한 사이트를 자율 탐색해 구조화된 JSON 데이터를 추출하는 스킬.

## 📌 용도
AI 기반 자율 추출(autonomous extraction) 에이전트를 실행한다. 에이전트가 사이트를 스스로 돌아다니며 어디에 데이터가 있는지 파악하고, 구조화된 데이터를 반환한다. 실행에 보통 2~5분이 걸린다. 여러 페이지에 걸친 구조화 추출에서 단순 스크래핑보다 강력하다.

- 복잡한 다중 페이지 사이트에서 구조화된 데이터가 필요할 때
- 수동 스크래핑이라면 여러 페이지를 직접 돌아다녀야 하는 경우
- 데이터가 어디 있는지 AI가 알아서 찾아주길 원할 때

## 🎯 트리거 조건
- **언제 호출되는가**: "구조화된 데이터 추출", "모든 제품 가져와", "가격 정보 뽑아줘", "JSON으로 추출", 또는 사용자가 웹 데이터용 JSON 스키마를 제공할 때. 가격 티어/제품 목록/디렉터리 항목 등을 스키마 기반 JSON으로 원할 때.
- **언제 쓰지 않는가**: 단일 페이지 단순 추출(→ `firecrawl-scrape`가 더 빠르고 저렴), 링크만 따라가는 비-AI 대량 추출(→ `firecrawl-crawl`), 수동 클릭/폼 제어가 필요한 경우(→ `firecrawl-interact`).

## 💻 사용 예시 / 명령어
```bash
# 구조화된 데이터 추출
firecrawl agent "extract all pricing tiers" --wait -o .firecrawl/pricing.json

# JSON 스키마로 구조화 출력
firecrawl agent "extract products" \
  --schema '{"type":"object","properties":{"name":{"type":"string"},"price":{"type":"number"}}}' \
  --wait -o .firecrawl/products.json

# 특정 페이지에 집중
firecrawl agent "get feature list" --urls "<url>" --wait -o .firecrawl/features.json
```

## 🛠️ 제공 도구 / 주요 파라미터
- 사용 도구: `Bash(firecrawl *)`, `Bash(npx firecrawl *)`

| 옵션 | 설명 |
| --- | --- |
| `--urls <urls>` | 에이전트 시작 URL |
| `--model <model>` | 모델 선택: `spark-1-mini` 또는 `spark-1-pro` |
| `--schema <json>` | 구조화 출력용 JSON 스키마 |
| `--schema-file <path>` | JSON 스키마 파일 경로 |
| `--max-credits <n>` | 이번 실행의 크레딧 상한 |
| `--wait` | 에이전트 완료까지 대기 |
| `--pretty` | JSON 보기 좋게 출력 |
| `-o, --output <path>` | 출력 파일 경로 |

**팁**: `--wait`를 항상 사용해 결과를 인라인으로 받는다(없으면 job ID만 반환). 예측 가능한 출력을 원하면 `--schema` 사용. agent 실행은 단순 스크래프보다 크레딧을 많이 소모하므로 `--max-credits`로 상한을 둔다.

## 📦 출처 / 설치 상태
- 플러그인: firecrawl@claude-plugins-official
- 활성화: ON
- 관련 MCP: firecrawl은 CLI 기반(`firecrawl agent`)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/firecrawl/1.0.9/skills/firecrawl-agent/SKILL.md`
