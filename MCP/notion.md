# notion

> Notion 워크스페이스의 페이지·데이터베이스·블록·댓글·사용자를 Notion 공식 REST API로 직접 읽고 쓰는 MCP 서버.

## 📌 용도
Notion의 공식 `@notionhq/notion-mcp-server`를 stdio로 띄워, Claude가 Notion 워크스페이스를 프로그래밍 방식으로 조작할 수 있게 한다. 주요 기능은 다음과 같다.

- 페이지 생성/조회/수정/이동
- 블록(텍스트, 제목, 불릿, 토글 등) 추가/조회/수정/삭제
- 데이터베이스(데이터 소스) 조회/생성/수정 및 쿼리(필터·정렬)
- 댓글 작성/조회
- 워크스페이스 검색
- 사용자(봇 자신 포함) 정보 조회

> ⚠️ 본 워크스페이스 규칙(전역 CLAUDE.md): Notion 페이지 수정 시 **부분 블록 수정(partial update) 금지** — API 에러가 반복됨. 전체 블록 삭제 후 처음부터 재생성 방식으로 작업할 것.

## 🎯 트리거 조건 / 사용 맥락
- "노션에 정리해줘 / 노션 페이지 만들어줘 / 노션에서 찾아줘" 같은 요청
- 회의록·문서·DB를 Notion에 적재하거나 동기화할 때
- Notion DB를 조건으로 쿼리해서 데이터를 가져올 때
- Notion 페이지의 댓글을 읽거나 남길 때

## 🛠️ 제공 도구 목록

### 페이지(Page)
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| API-post-page | 새 페이지 생성 | `parent`(상위 페이지/DB), `properties`, `children` |
| API-retrieve-a-page | 페이지 메타데이터 조회 | `page_id` |
| API-retrieve-a-page-property | 페이지의 특정 프로퍼티 값 조회 | `page_id`, `property_id` |
| API-patch-page | 페이지 프로퍼티/아이콘/커버 수정 | `page_id`, `properties` |
| API-move-page | 페이지를 다른 상위로 이동 | `page_id`, 새 `parent` |

### 블록(Block)
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| API-get-block-children | 블록의 자식 블록 목록 조회 | `block_id`, `start_cursor`, `page_size` |
| API-patch-block-children | 블록에 자식 블록 추가(append) | `block_id`, `children` |
| API-retrieve-a-block | 단일 블록 조회 | `block_id` |
| API-update-a-block | 블록 내용 수정 | `block_id`, 블록 타입별 본문 |
| API-delete-a-block | 블록 삭제(휴지통 이동) | `block_id` |

### 데이터베이스 / 데이터 소스(Data Source)
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| API-retrieve-a-database | 데이터베이스 메타데이터 조회 | `database_id` |
| API-create-a-data-source | 데이터 소스(DB) 생성 | `parent`, `properties`(스키마) |
| API-retrieve-a-data-source | 데이터 소스 조회 | `data_source_id` |
| API-update-a-data-source | 데이터 소스 스키마/설정 수정 | `data_source_id`, `properties` |
| API-query-data-source | 데이터 소스 쿼리(필터·정렬·페이징) | `data_source_id`, `filter`, `sorts` |
| API-list-data-source-templates | 데이터 소스 템플릿 목록 조회 | `data_source_id` |

### 댓글(Comment)
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| API-create-a-comment | 페이지/블록에 댓글 작성 | `parent` 또는 `discussion_id`, `rich_text` |
| API-retrieve-a-comment | 댓글 목록 조회 | `block_id` |

### 검색 / 사용자(Search / User)
| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| API-post-search | 워크스페이스 전체 검색(페이지/DB) | `query`, `filter`, `sort` |
| API-get-self | 연결된 봇(통합) 자신 정보 조회 | (없음) |
| API-get-user | 특정 사용자 조회 | `user_id` |
| API-get-users | 사용자 목록 조회 | `start_cursor`, `page_size` |

## 💻 사용 예시

**예시 1 — 워크스페이스에서 페이지 검색 후 내용 추가**
1. `API-post-search`로 `query: "회의록"` 검색 → 대상 페이지 `page_id` 획득
2. `API-patch-block-children`로 해당 페이지에 새 불릿/문단 블록 append

**예시 2 — 데이터베이스 쿼리**
1. `API-post-search`(filter: database)로 DB의 `data_source_id` 확보
2. `API-query-data-source`로 `filter`(예: 상태=진행중), `sorts`(마감일 오름차순) 적용해 행 조회

## 📦 출처 / 설치 상태
- 제공: **글로벌 mcpServers** (`~/.claude.json` 최상위 `mcpServers`에 등록)
- 활성화: ON (전역 등록이므로 모든 프로젝트에서 사용 가능)
- 연결 방식: **stdio** — `npx -y @notionhq/notion-mcp-server`
- 인증: 환경변수나 클라이언트의 보안 저장소를 통해 Notion 통합 토큰과 `Notion-Version` 헤더를 주입
  - ⚠️ 실제 토큰·인증 헤더·개인 설정 파일은 저장소에 포함하지 않는다.
- 설정 경로: `~/.claude.json` (최상위 `mcpServers.notion`)
