# UsStockInfo (PlayMCP)

> 미국 주식 시세·재무·옵션·뉴스 정보를 제공하는 PlayMCP 하위 서버 (Yahoo Finance 계열 데이터).

## 📌 용도
미국 상장 종목의 현재 정보, 과거 시세, 재무제표, 옵션 체인, 주주/보유 현황, 배당·분할 이력, 애널리스트 추천, 금융 뉴스 등을 제공한다. 미국 주식 분석·리서치에 활용한다.

## 🎯 트리거 조건 / 사용 맥락
- "AAPL 주가 / 테슬라 재무제표 / 엔비디아 옵션 / 이 종목 애널리스트 의견" 등 미국 주식 질의
- 종목 시세·차트 데이터, 재무 분석, 옵션 전략 검토, 배당 이력 확인

## 🛠️ 제공 도구 목록

### 종목 기본/시세
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| get_stock_info | 종목 기본 정보(현재가, 시총, 지표 등) | `ticker` |
| get_historical_stock_prices | 과거 주가(OHLCV) 시계열 | `ticker`, `period`, `interval` |
| get_stock_actions | 배당·액면분할 등 기업 액션 이력 | `ticker` |

### 재무
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| get_financial_statement | 재무제표(손익/재무상태/현금흐름) 조회 | `ticker`, `statement_type`, `freq` |

### 옵션
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| get_option_expiration_dates | 옵션 만기일 목록 조회 | `ticker` |
| get_option_chain | 특정 만기의 옵션 체인(콜/풋) 조회 | `ticker`, `expiration_date` |

### 보유/추천/뉴스
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| get_holder_info | 주주/기관·내부자 보유 현황 | `ticker` |
| get_recommendations | 애널리스트 추천(매수/보유/매도) | `ticker` |
| get_finance_news | 종목/시장 금융 뉴스 | `ticker`/키워드 |

## 💻 사용 예시

**예시 1 — 종목 스냅샷 + 추천**
- `get_stock_info`(ticker: "AAPL")로 현재가/지표 → `get_recommendations`(ticker: "AAPL")로 애널리스트 의견 종합

**예시 2 — 옵션 체인 조회**
1. `get_option_expiration_dates`(ticker: "NVDA")로 만기일 확인
2. `get_option_chain`(ticker: "NVDA", expiration_date: 선택일)로 콜/풋 체인 조회

## 📦 출처 / 설치 상태
- 제공: **claude.ai 연동 / PlayMCP 하위 서버** (카카오 공식)
- 활성화: PlayMCP 묶음으로 연결됨 (`claudeAiMcpEverConnected: ["claude.ai PlayMCP"]`)
- 연결 방식: claude.ai 원격 MCP (클라우드 호스팅)
- 데이터 특성: 시세/재무는 지연·참고용일 수 있으므로 실거래 의사결정 전 원본 확인 권장
- 세션 내 도구 식별자: `mcp__claude_ai_PlayMCP__UsStockInfo-<도구>`
- 묶음 개요: [README.md](./README.md)
