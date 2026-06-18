# PORTABILITY.md — 다른 AI 에이전트에서 사용하기

> 이 저장소의 **스킬·MCP를 Claude 이외의 AI 에이전트**(OpenAI Codex/Agents SDK, Gemini, Cursor, Copilot 등)에서 쓰는 방법.
> 핵심 원칙: **MCP는 그대로 이식**(오픈 표준), **Skill은 실물을 재배포하지 않고 "출처 포인터 + 각자 설치"**.

---

## 0. 30초 요약

| 대상 | 다른 AI에서 | 방법 |
|---|---|---|
| **MCP** | ✅ 그대로 | 오픈 표준. 각 클라이언트 설정에 **서버 등록**(아래 §2) |
| **Skill** | ⚠️ 조건부 | **실물 재배포 금지**. 공개 **원본에서 각자 설치**(§3-A), 미지원 AI는 참조 활용(§3-B)·MCP 래핑(§3-C) |

> 전제: 이 저장소의 `Skill/`·`MCP/`는 **설명 문서(카탈로그)**다. 실행 실물이 아니다. 실물 MCP는 각 서버, 실물 스킬은 원본 저장소에 있다.

---

## 1. 무엇이 왜 이식되는가

- **MCP (Model Context Protocol)** — 오픈 표준. 2026년 기준 Anthropic·OpenAI·Google·Cloudflare·AWS·Microsoft·Cursor 등이 모두 지원(Linux Foundation 산하 관리). → 서버를 등록만 하면 어느 AI든 같은 도구를 쓴다.
- **Agent Skills (SKILL.md)** — 역시 오픈 표준이나, **"skills-호환 에이전트"**여야 자동 작동한다. 실물 SKILL.md가 필요하며, 이 저장소는 그 실물을 담지 않는다(재배포 회피).

---

## 1.5 Claude 전용 용어 → 외부 AI 대체 (번역표)

저장소 지침(`AGENTS.md`·`rules/`·`Skill·MCP` 문서)에 등장하는 Claude Code 전용 용어와 외부 AI에서의 대체. (README §5.5의 🔴/🟡 항목과 대응)

| Claude 전용 | 무엇인가 | 외부 AI 대체 |
|---|---|---|
| `rag_agent` (rag-search MCP) | 코드/프로젝트 RAG 검색 도구 | 자체 검색·임베딩, 또는 동등 RAG MCP |
| `Skill` 도구 / "Using [skill]" | SKILL.md를 트리거·로드 | §3 경로 A(설치)/B(참조)/C(MCP 래핑) |
| `Task` / `Explore` 서브에이전트 | 탐색·작업 위임 | 그 AI의 서브에이전트/위임 기능 |
| `PreToolUse 훅` 자동차단 | 위험 명령(rm -rf·시크릿) 사전 차단 | **없음 → 수동 준수** (안전망 부재) |
| `claude mcp add` | MCP 등록 CLI | 자기 `mcp.json`/SDK 설정 (§2.2) |
| `TeamCreate`/`TeamDelete` · `team-monitor` clone(`rules/02 §3`) | 멀티에이전트 팀 + 모니터 | 그 AI의 멀티에이전트 기능, team-monitor clone은 건너뜀 |
| `MEMORY.md` / `Compact` | 자동 메모리 / 컨텍스트 압축 | 동등 기능에 매핑, 없으면 제외 |
| `~/.claude/` 설치체계 · `dotfiles`/`sync.py` 프로토콜(`rules/03 §6`) | Claude Code 설정 디렉토리·동기화 | 해당 없음 — 설치/동기화 절차는 CC 전용, 제외 |

> 행동 규율(작업 3단계·DO/DON'T·검증 게이트·디버깅·GC)은 용어와 무관하게 **그대로 적용**된다(README §5.5 🟢). 막히는 건 위 도구·하네스 종속 항목뿐이며, 표대로 번역/제외한다.

---

## 2. MCP 이식

### 2.1 서버별 연결정보

| 서버 | 타입 | 등록값 | 타 AI 사용 |
|---|---|---|---|
| `context7` | stdio | `npx -y @upstash/context7-mcp` | ✅ |
| `playwright` | stdio | `npx @playwright/mcp@latest` | ✅ |
| `figma` | http | `https://mcp.figma.com/mcp` (OAuth) | ✅ (http MCP 지원 시) |
| `huggingface-skills` | http | `https://huggingface.co/mcp?login` (OAuth) | ✅ |
| `notion` | stdio | `npx @notionhq/notion-mcp-server` (+ 통합 토큰) | ✅ (자기 토큰 필요) |
| **claude.ai PlayMCP** | 연동 | — | ❌ **claude.ai 계정 종속, 이식 불가** (§2.4) |

### 2.2 클라이언트별 등록법

대부분의 AI 클라이언트는 **Claude와 동일한 `mcpServers` 스키마**를 쓴다. 그대로 복사하면 된다.

**범용 `mcp.json` (Cursor `.cursor/mcp.json`, VS Code `.vscode/mcp.json`, 기타 다수)**
```jsonc
{
  "mcpServers": {
    "context7":  { "command": "npx", "args": ["-y", "@upstash/context7-mcp"] },
    "playwright":{ "command": "npx", "args": ["@playwright/mcp@latest"] },
    "figma":     { "type": "http", "url": "https://mcp.figma.com/mcp" }
  }
}
```

**OpenAI Agents SDK (Python)**
```python
from agents.mcp import MCPServerStdio, MCPServerStreamableHttp

context7 = MCPServerStdio(params={"command": "npx", "args": ["-y", "@upstash/context7-mcp"]})
figma    = MCPServerStreamableHttp(params={"url": "https://mcp.figma.com/mcp"})
# agent = Agent(..., mcp_servers=[context7, figma])
```

**Gemini CLI** — `~/.gemini/settings.json` 의 `mcpServers`에 위 범용 스키마와 동일하게 추가.

**Claude Code(참고)** — `claude mcp add <name> --scope user -- npx -y @upstash/context7-mcp`

### 2.3 인증·OAuth 주의
- `figma`·`huggingface-skills` = **원격 http + OAuth**. 등록 후 브라우저 로그인으로 인증. 다른 AI 클라이언트도 http MCP + OAuth를 지원해야 한다.
- `notion` = stdio지만 **통합 토큰** 필요. 받는 사람이 자기 Notion 토큰을 환경변수(`OPENAPI_MCP_HEADERS` 등)로 넣는다. **토큰을 저장소에 커밋하지 말 것.**

### 2.4 이식 불가 — claude.ai PlayMCP
`NaverSearch`·`KakaotalkChat`·`SaraminMcp`·`UsStockInfo`·`opendart`는 **claude.ai 계정에 연동된 MCP**라 다른 AI에서 직접 못 쓴다.
→ 대안: 각 서비스(네이버/카카오/사람인 등)의 **독립 MCP 서버나 공개 API**를 찾아 별도 등록.

---

## 3. Skill 이식 (실물 재배포 없이)

### 3.1 원칙
**스킬 실물을 이 패키지에 복사·재배포하지 않는다.** 대부분이 타사 플러그인이라 재배포는 라이선스 위반이다. 대신 **공개 원본을 가리키고, 받는 팀이 직접 설치**한다(= 정당한 최종 사용, 재배포 아님).

### 3.2 경로 A — 출처에서 각자 설치 (주력)

**공식 플러그인 스킬**(`superpowers`·`firecrawl`·`huggingface-skills`·`plugin-dev` 등 카탈로그의 ON 플러그인)의 공개 원본:
```
github.com/anthropics/claude-plugins-official   (= claude-plugins-official 마켓플레이스)
```

- **받는 AI가 Claude Code면**: `/plugin marketplace add anthropics/claude-plugins-official` → 원하는 플러그인 설치. (이 저장소를 거치지 않고 원본에서 직접)
- **받는 AI가 Agent Skills를 지원하면**: 위 저장소에서 해당 스킬의 **`SKILL.md` 디렉토리만** 가져와 그 AI의 skills 폴더에 배치. (플러그인의 커맨드·훅 등 Claude 전용 부분은 제외하고 **순수 SKILL.md만**)
- **스킬별 정확한 공개 원본·License**는 [`Skill/README.md`](Skill/README.md)의 **"📦 출처·설치 매핑"** 표 참조. 주의: 전부 마켓플레이스 번들이 아니다 — 일부는 **외부 repo가 원본**이다:
  - `superpowers` → `github.com/obra/superpowers` (MIT) · `huggingface-skills` → `github.com/huggingface/skills` (Apache-2.0) · `qodo-skills` → `github.com/qodo-ai/qodo-skills` (MIT) · `vercel`/`railway`/`neon`도 각사 repo
  - 나머지(Anthropic 제작 + Figma·Microsoft·Upstash·Semgrep·Firecrawl 번들)는 `github.com/anthropics/claude-plugins-official`에서 배포되며, **plugin.json에 license 미기재 항목은 재배포 전 제작사 약관 확인**.

### 3.3 경로 B — 참조 활용 (자동 트리거 없이)
받는 AI가 Agent Skills를 **미지원**하면, 카탈로그 문서(설명) 자체를 **컨텍스트에 넣어 지침으로** 쓴다. 자동 트리거·progressive disclosure는 없지만, "이 스킬이 무엇을 어떻게 하는지"를 AI가 읽고 같은 작업을 수행할 수 있다. (설명 문서 활용은 라이선스 자유)

### 3.4 경로 C — MCP/도구로 래핑 (가장 이식성 높음)
스킬의 **기능**을 직접 MCP 서버나 함수 도구로 구현해 노출하면, MCP를 지원하는 **모든 AI**가 쓴다. 재배포가 아니라 자체 구현이므로 라이선스 자유. 노력은 크지만 가장 보편적.

### 3.5 라이선스 가드 (필수 확인)
- 이 저장소엔 **스킬 실물을 넣지 않는다**(출처 링크만). 그래야 재배포 문제가 없다.
- 받는 팀은 원본(`anthropics/claude-plugins-official` 등)의 **LICENSE**를 확인하고 그 조건에 따라 설치·사용한다.
- 자유롭게 재배포 가능한 건 **당신이 직접 작성한 스킬**뿐. (현 환경에서 실물이 있는 자체 스킬은 사실상 없음 — `_personal`은 대부분 하네스 등록형)

### 3.6 사전 점검 — 받는 AI가 Agent Skills를 지원하는가?
- **지원** → 경로 A(출처 설치)로 SKILL.md 배치.
- **미지원** → 경로 B(참조) 또는 경로 C(MCP 래핑).

---

## 4. 이식 체크리스트

- [ ] **MCP**: 필요한 서버를 대상 AI의 `mcpServers` 설정에 등록 (§2.2)
- [ ] **MCP 인증**: figma·hf는 OAuth 로그인, notion은 자기 토큰 주입(커밋 금지)
- [ ] **PlayMCP 제외**: claude.ai 종속이므로 대체 MCP/API로 교체 (§2.4)
- [ ] **Skill 출처 확인**: 쓰려는 스킬의 공개 원본(`anthropics/claude-plugins-official` 등) 확보
- [ ] **Agent Skills 지원 여부** 확인 → 경로 A / B / C 결정
- [ ] **LICENSE 확인**: 원본 라이선스 조건 준수, 실물 재배포 금지
- [ ] (선택) 자주 쓰는 스킬을 **경로 C(MCP 래핑)**로 만들어 크로스-AI 표준화

---

*이 문서는 "이식 방법"을 안내한다. 실물 스킬은 담지 않으며(재배포 회피), MCP 연결정보는 현 환경 `.mcp.json` 기준이다.*
