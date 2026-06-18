# NaverSearch (PlayMCP)

> 네이버 검색 API와 데이터랩(DataLab) 트렌드를 제공하는 PlayMCP 하위 서버.

## 📌 용도
네이버의 다양한 검색 카테고리(블로그·뉴스·이미지·지식인·쇼핑·지역·웹·전문자료·백과·책·카페)와 데이터랩의 검색어/쇼핑 트렌드 분석을 한국어 환경에 맞춰 제공한다. 한국 웹 콘텐츠와 소비 트렌드 조사에 최적이다.

## 🎯 트리거 조건 / 사용 맥락
- 한국 뉴스/블로그/카페/지식인 등 국내 웹 콘텐츠 검색
- 네이버 쇼핑 상품/가격 조회
- 지역(맛집·상호) 검색, 이미지 검색, 책/전문자료/백과 검색
- 검색어 트렌드, 쇼핑 카테고리/키워드의 연령·성별·기기별 트렌드 분석(데이터랩)

## 🛠️ 제공 도구 목록

### 일반 검색
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| search_blog | 블로그 글 검색 | `query`, `display`, `start`, `sort` |
| search_news | 뉴스 검색 | `query`, `display`, `sort` |
| search_cafearticle | 카페 글 검색 | `query`, `display`, `sort` |
| search_kin | 지식iN(Q&A) 검색 | `query`, `display`, `sort` |
| search_webkr | 웹문서 검색 | `query`, `display` |
| search_image | 이미지 검색 | `query`, `display`, `filter` |
| search_shop | 쇼핑 상품 검색 | `query`, `display`, `sort` |
| search_local | 지역(업체/장소) 검색 | `query`, `display`, `sort` |
| search_book | 책 검색 | `query`, `display`, `sort` |
| search_encyc | 백과사전 검색 | `query`, `display` |
| search_academic | 전문자료(학술) 검색 | `query`, `display` |

### 데이터랩 — 검색어 트렌드
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| datalab_search | 검색어 기간별 트렌드 조회 | `startDate`, `endDate`, `timeUnit`, `keywordGroups` |

### 데이터랩 — 쇼핑 인사이트
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| datalab_shopping_category | 쇼핑 카테고리별 클릭 트렌드 | `category`, `startDate`, `endDate`, `timeUnit` |
| datalab_shopping_by_age | 카테고리 트렌드 — 연령별 | `category`, 기간, `ages` |
| datalab_shopping_by_gender | 카테고리 트렌드 — 성별 | `category`, 기간, `gender` |
| datalab_shopping_by_device | 카테고리 트렌드 — 기기(PC/모바일)별 | `category`, 기간, `device` |
| datalab_shopping_keywords | 카테고리 내 키워드 트렌드 | `category`, `keyword`, 기간 |
| datalab_shopping_keyword_by_age | 키워드 트렌드 — 연령별 | `category`, `keyword`, `ages` |
| datalab_shopping_keyword_by_gender | 키워드 트렌드 — 성별 | `category`, `keyword`, `gender` |
| datalab_shopping_keyword_by_device | 키워드 트렌드 — 기기별 | `category`, `keyword`, `device` |

### 보조
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| find_category | 쇼핑 카테고리 코드/이름 조회 (데이터랩 입력값 준비용) | `keyword`/카테고리명 |
| get_current_korean_time | 현재 한국 시각 조회 (기간 파라미터 기준 잡기) | (없음) |

## 💻 사용 예시

**예시 1 — 최신 한국 뉴스 요약**
- `search_news`(query: "반도체 수출", sort: "date", display: 10) → 결과 헤드라인 요약

**예시 2 — 쇼핑 키워드 트렌드 분석**
1. `find_category`로 대상 카테고리 코드 확인
2. `get_current_korean_time`으로 기준 날짜 확인
3. `datalab_shopping_keyword_by_age`(category, keyword, 기간, ages)로 연령대별 관심도 비교

## 📦 출처 / 설치 상태
- 제공: **claude.ai 연동 / PlayMCP 하위 서버** (카카오 공식)
- 활성화: PlayMCP 묶음으로 연결됨 (`claudeAiMcpEverConnected: ["claude.ai PlayMCP"]`)
- 연결 방식: claude.ai 원격 MCP (클라우드 호스팅, 별도 stdio command 없음)
- 세션 내 도구 식별자: `mcp__claude_ai_PlayMCP__NaverSearch-<도구>`
- 묶음 개요: [README.md](./README.md)
