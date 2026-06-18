# MCP 서버 인덱스

현재 세션(`~/Desktop/Agent` 프로젝트)에서 연결/활성된 MCP(Model Context Protocol) 서버 목록입니다.

## 출처별 분류

| MCP 서버 | 제공 경로 | 연결 방식 | 도구 수 | 문서 |
|---|---|---|---|---|
| **notion** | 글로벌 (`~/.claude.json` mcpServers) | stdio (`@notionhq/notion-mcp-server`) | 22 | [notion.md](notion.md) |
| **context7** | 플러그인 `context7` | stdio (npx) | 2 | [context7.md](context7.md) |
| **figma** | 플러그인 `figma` | http (원격 OAuth) | 2 + 스킬연동 | [figma.md](figma.md) |
| **huggingface-skills** | 플러그인 `huggingface-skills` | http (원격 OAuth) | 2 + 스킬연동 | [huggingface-skills.md](huggingface-skills.md) |
| **playwright** | 플러그인 `playwright` | stdio (npx) | 23 | [playwright.md](playwright.md) |
| **claude.ai PlayMCP** | claude.ai 계정 연동 (카카오 공식) | 원격 | 5개 하위 서버 | [claude-ai-PlayMCP/](claude-ai-PlayMCP/README.md) |

## claude.ai PlayMCP 하위 서버

| 하위 서버 | 주제 | 도구 수 | 문서 |
|---|---|---|---|
| NaverSearch | 네이버 검색 + 데이터랩 | 22 | [NaverSearch.md](claude-ai-PlayMCP/NaverSearch.md) |
| KakaotalkChat | 카카오톡 메모챗 | 1 | [KakaotalkChat.md](claude-ai-PlayMCP/KakaotalkChat.md) |
| SaraminMcp | 사람인 채용정보 | 8 | [SaraminMcp.md](claude-ai-PlayMCP/SaraminMcp.md) |
| UsStockInfo | 미국 주식 정보 | 9 | [UsStockInfo.md](claude-ai-PlayMCP/UsStockInfo.md) |
| opendart | 금융감독원 전자공시(DART) | 14 | [opendart.md](claude-ai-PlayMCP/opendart.md) |

## ⛔ 비활성(OFF) 플러그인 MCP (`_disabled/`)

현재 플러그인이 OFF라 연결되지 않음. 플러그인을 켜면 활성화됨.

| MCP 서버 | 제공 플러그인 | 연결 방식 | 문서 |
|---|---|---|---|
| **vercel** | vercel (OFF) | http 원격 (`mcp.vercel.com`, OAuth, 읽기전용) | [_disabled/vercel.md](_disabled/vercel.md) |
| **railway** | railway (OFF) | stdio (`railway mcp`) + 원격 옵션 | [_disabled/railway.md](_disabled/railway.md) |
| **neon** | neon (OFF) | http 원격 (`mcp.neon.tech/mcp`) | [_disabled/neon.md](_disabled/neon.md) |
| **semgrep** | semgrep (OFF) | stdio (`semgrep mcp`) | [_disabled/semgrep.md](_disabled/semgrep.md) |

> 원격 MCP(vercel·neon)는 도구가 OAuth 후 런타임에 동적 노출되어 `.mcp.json`에 정적 도구 목록이 없습니다. 문서는 연결 정보 위주로 기술했습니다.

## ⚠️ 주의사항

- **인증 정보 관리**: Notion·Figma·Hugging Face 등 외부 서비스 토큰과 OAuth 인증 정보는 각 사용자 환경에서 주입한다. 실제 토큰·인증 헤더·개인 설정 파일은 저장소에 포함하지 않는다.
