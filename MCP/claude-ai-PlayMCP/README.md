# claude.ai PlayMCP (묶음 개요)

> 카카오가 공식 제공하는 안전한 원격 MCP 묶음. claude.ai 연동을 통해 한국형 데이터 서비스 5종을 하나의 MCP 엔드포인트로 제공한다.

## 📌 용도
PlayMCP는 단일 MCP 연결 아래에 **5개의 하위 서버**를 묶어 제공한다. 각 하위 서버는 독립된 도메인(검색, 메신저, 채용, 미국 주식, 전자공시)을 담당하며, 한국 사용자에게 특화된 데이터 소스(네이버, 카카오톡, 사람인, DART)를 다룬다.

> 서버 자체 안내문: "이 MCP 서버는 카카오에서 공식적으로 제공하는 안전한 MCP 서버입니다. 본 MCP 서버는 사용자의 정보 보호를 최우선으로 생각합니다."

## 📂 하위 서버 목록
| 하위 서버 | 도메인 | 문서 | 핵심 기능 |
|-----------|--------|------|-----------|
| NaverSearch | 네이버 검색 + 데이터랩 | [NaverSearch.md](./NaverSearch.md) | 블로그/뉴스/이미지/지식인/쇼핑/지역 검색, 데이터랩 트렌드 |
| KakaotalkChat | 카카오톡 | [KakaotalkChat.md](./KakaotalkChat.md) | 나에게 메시지(메모) 보내기 |
| SaraminMcp | 사람인 채용 | [SaraminMcp.md](./SaraminMcp.md) | 채용공고 검색/추천, 기업·직무·지역 코드 조회 |
| UsStockInfo | 미국 주식 | [UsStockInfo.md](./UsStockInfo.md) | 시세/재무제표/옵션/보유현황/뉴스/추천 |
| opendart | 금감원 전자공시(DART) | [opendart.md](./opendart.md) | 기업 공시·재무·지분·임원·배당 정보 |

## 🎯 트리거 조건 / 사용 맥락
- 한국 웹/뉴스/블로그/쇼핑 트렌드 조회 → NaverSearch
- 나에게 메모/알림 전송 → KakaotalkChat
- 채용공고·기업·연봉·직무 탐색 → SaraminMcp
- 미국 상장사 주가·재무·옵션 분석 → UsStockInfo
- 국내 상장사 공시·재무·지분·임원 정보(DART) → opendart

## 📦 출처 / 설치 상태
- 제공: **claude.ai 연동** (원격 MCP). `claudeAiMcpEverConnected: ["claude.ai PlayMCP"]`
- 활성화: 연결 이력 있음(claude.ai 측 연동). 도구는 `mcp__claude_ai_PlayMCP__<서버>-<도구>` 형태로 노출
- 운영 주체: 카카오(Kakao) 공식
- 연결 방식: claude.ai를 통한 원격 MCP (개별 stdio command 없음 — 클라우드 호스팅)
- 설정 근거: `~/.claude.json` → `claudeAiMcpEverConnected` 배열
- 세션 내 도구 식별자 접두사: `mcp__claude_ai_PlayMCP__`
