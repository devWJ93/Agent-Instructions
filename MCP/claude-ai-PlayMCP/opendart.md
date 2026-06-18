# opendart (PlayMCP)

> 금융감독원 전자공시시스템(DART)의 기업 공시·재무·지분 정보를 제공하는 PlayMCP 하위 서버.

## 📌 용도
DART OpenAPI를 통해 국내 상장/공시대상 법인의 공시 목록, 재무제표, 지분·주주 현황, 임원·직원, 배당, 자본변동, 자기주식 등 기업 분석 핵심 데이터를 제공한다. 국내 기업 리서치·재무분석에 활용한다.

## 🎯 트리거 조건 / 사용 맥락
- "삼성전자 재무제표 / OO기업 공시 / 최대주주 현황 / 임원 명단 / 배당 내역" 등 국내 상장사 분석
- DART 기준 기업 식별(고유번호) 후 재무·지분·공시 데이터 조회

## 🛠️ 제공 도구 목록

### 기업 식별/기본
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| find_company | 회사명으로 DART 고유번호(corp_code) 등 식별 정보 검색 | 회사명 |
| get_company_info | 기업 개황(대표자·주소·업종 등) | `corp_code` |
| search_disclosures | 공시 목록 검색 | `corp_code`, 기간, 공시유형 |

### 재무
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| get_financial_account | 주요 계정 재무정보 | `corp_code`, `bsns_year`, `reprt_code` |
| get_full_financial_statement | 전체 재무제표(전 계정) | `corp_code`, `bsns_year`, `reprt_code` |
| get_financial_index | 재무비율/지표 | `corp_code`, `bsns_year`, 지표분류 |

### 지분 / 주주
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| get_largest_shareholders | 최대주주 현황 | `corp_code`, 연도/보고서 |
| get_major_stock | 대량보유(5% 이상) 상황 | `corp_code` |
| get_executive_stock | 임원·주요주주 소유 주식 | `corp_code` |
| get_treasury_stock | 자기주식 취득/처분 현황 | `corp_code`, 연도/보고서 |
| get_capital_change | 증자·감자 등 자본금 변동 | `corp_code`, 연도/보고서 |

### 임원 / 직원 / 배당
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| get_executives | 임원 현황(이사·감사) | `corp_code`, 연도/보고서 |
| get_employees | 직원 현황(인원·급여 등) | `corp_code`, 연도/보고서 |
| get_dividend_info | 배당 관련 사항 | `corp_code`, 연도/보고서 |

## 💻 사용 예시

**예시 1 — 기업 재무제표 조회**
1. `find_company`(회사명: "카카오")로 `corp_code` 획득
2. `get_full_financial_statement`(corp_code, bsns_year: 2024, reprt_code: 사업보고서)로 전체 재무제표 조회

**예시 2 — 지배구조 분석**
- `find_company` → `get_largest_shareholders` + `get_executives`로 최대주주·임원 현황 종합

## 📦 출처 / 설치 상태
- 제공: **claude.ai 연동 / PlayMCP 하위 서버** (카카오 공식)
- 활성화: PlayMCP 묶음으로 연결됨 (`claudeAiMcpEverConnected: ["claude.ai PlayMCP"]`)
- 연결 방식: claude.ai 원격 MCP (클라우드 호스팅, 금감원 DART OpenAPI 백엔드)
- 입력 규칙: 대부분 도구가 DART 고유번호(`corp_code`)를 요구 → 먼저 `find_company`로 식별
- 세션 내 도구 식별자: `mcp__claude_ai_PlayMCP__opendart-<도구>`
- 묶음 개요: [README.md](./README.md)
