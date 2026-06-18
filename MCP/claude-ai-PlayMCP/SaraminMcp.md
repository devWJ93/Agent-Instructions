# SaraminMcp (PlayMCP)

> 사람인(Saramin) 채용 데이터를 제공하는 PlayMCP 하위 서버.

## 📌 용도
사람인 채용공고 검색·추천, 직무/지역/지하철 코드 조회, 기업 정보 검색 등 한국 채용 시장 데이터를 제공한다. 구직·이직·채용 분석에 활용한다.

## 🎯 트리거 조건 / 사용 맥락
- "채용공고 찾아줘 / 어떤 회사가 채용 중이야 / 이 직무 공고 추천해줘"
- 기업 정보, 연봉/직무 카테고리, 지역·지하철 기준 검색
- 구직 조건(직무·지역·키워드)에 맞는 공고 추천

## 🛠️ 제공 도구 목록

### 채용공고 검색·추천
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| search_saramin_jobs | 조건(키워드/직무/지역 등)으로 채용공고 검색 | `keyword`, `job_category`, `location`, 페이징 |
| recommend_saramin_recruit | 조건 기반 채용공고 추천 | 직무/지역/관심사 등 |

### 코드/카테고리 조회 (검색 입력값 준비용)
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| fetch_job_categories | 직무 대분류 카테고리 목록 조회 | (없음) |
| fetch_sub_job_categories | 특정 직무의 하위 카테고리 조회 | 상위 직무 코드 |
| search_location_codes | 지역 코드 검색 | 지역명 |
| search_subway_info | 지하철역 정보/코드 검색 | 역명 |

### 기업 / 부가 정보
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| search_company_info | 기업 정보 검색 | 회사명/조건 |
| search_saramin_store_products | 사람인 스토어 상품 검색 | 상품 키워드 |

## 💻 사용 예시

**예시 1 — 특정 직무·지역 채용 검색**
1. `fetch_job_categories` → `fetch_sub_job_categories`로 직무 코드 확인
2. `search_location_codes`로 지역 코드 확인
3. `search_saramin_jobs`(키워드/직무코드/지역코드)로 공고 목록 조회

**예시 2 — 맞춤 추천**
- 관심 직무/지역을 넣어 `recommend_saramin_recruit` 호출 → 추천 공고 리스트 수신

## 📦 출처 / 설치 상태
- 제공: **claude.ai 연동 / PlayMCP 하위 서버** (카카오 공식)
- 활성화: PlayMCP 묶음으로 연결됨 (`claudeAiMcpEverConnected: ["claude.ai PlayMCP"]`)
- 연결 방식: claude.ai 원격 MCP (클라우드 호스팅, 사람인 API 백엔드)
- 세션 내 도구 식별자: `mcp__claude_ai_PlayMCP__SaraminMcp-<도구>`
- 묶음 개요: [README.md](./README.md)
