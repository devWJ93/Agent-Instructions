# skill-gen

> Firecrawl로 문서 URL을 스크래핑해 완전한 Agent Skill을 자동 생성하는 슬래시 명령어.

## 📌 용도
임의의 라이브러리/도구/서비스 공식 문서 URL을 입력받아, 그 내용을 Firecrawl로 수집한 뒤 AI 에이전트가 사용할 수 있는 프로덕션 수준의 Agent Skill(SKILL.md + scripts/references/assets)을 처음부터 끝까지 만들어 주는 명령어다. 단순 요약이 아니라 "에이전트가 작업을 더 잘 수행하게 해 줄 비자명한 절차적 지식·도메인 세부사항·재사용 자산"을 식별해 스킬로 구조화하는 것이 목표다.

전체 흐름은 8단계로 구성된다.
1. **문서 스크래핑** — `firecrawl map`으로 관련 페이지를 발견하고, `firecrawl scrape`로 핵심 페이지(API 레퍼런스, 퀵스타트, 핵심 개념, 인증, 예제)를 가져온다. 대형 사이트는 `firecrawl crawl --maxDepth 2 --limit 15`로 수집한다. 체인지로그/블로그/커뮤니티 페이지는 제외한다.
2. **스킬 범위 명확화** — 스킬 이름(kebab-case 제안), 2~3개 핵심 유스케이스(트리거 발화)를 사용자에게 1~2개 질문으로 확인.
3. **스킬 내용 설계** — 각 유스케이스를 처음부터 수행하는 방식을 분석해 재사용 리소스 유형(scripts/references/assets)을 결정.
4. **계획 승인** (필수, 파일 작성 전) — 디렉터리 트리, 각 파일 요약, SKILL.md frontmatter(name/description)를 제시하고 승인 대기.
5. **저장 위치 질문** (필수) — Project(`.claude/skills/`), Global(`~/.claude/skills/`), Custom 중 사용자 선택. 기본값 지정 금지.
6. **스킬 빌드** — 승인+위치 확정 후에만 파일 작성. 스크립트가 있으면 실행해 검증.
7. **검증** — frontmatter 체크(name kebab-case·디렉터리명 일치, description 비어있지 않음), SKILL.md 500줄 미만(`wc -l`), README/CHANGELOG 등 잡파일 없음, references 1단계 깊이. 각 항목 PASS/FAIL 보고.
8. **전달** — 빌드 요약, 줄 수 포함 디렉터리 트리, 검증 결과, 저장 위치 제시.

## 🎯 트리거 조건
- **언제 호출되는가**: `/skill-gen <documentation-url>` 슬래시 명령어를 명시적으로 실행할 때. "이 문서로 스킬 만들어줘", "이 API 문서를 스킬화해줘" 같은 요청.
- **언제 쓰지 않는가**: 단순히 문서를 읽거나 요약만 원할 때(그건 `firecrawl-scrape`/`firecrawl-crawl`), 스킬 구조 일반론을 묻는 경우(별도 skill-creator/plugin-dev 영역).

## 💻 사용 예시 / 명령어
```
/skill-gen https://docs.stripe.com/api
/skill-gen https://docs.firecrawl.dev
```
- 인자(`$ARGUMENTS`): 문서 사이트의 시작 URL 하나. `argument-hint: <documentation-url>`.
- 내부적으로 `firecrawl map/scrape/crawl`를 호출하므로 firecrawl CLI 인증이 선행되어야 함.

## 🛠️ 제공 도구 / 주요 파라미터
- **사용 도구**: `firecrawl` 스킬(map/scrape/crawl) — 문서 수집 단계에서만 사용.
- **산출물 형식 규칙(스킬 포맷 레퍼런스 내장)**:
  - frontmatter `name`(kebab-case, 최대 64자, 디렉터리명과 일치), `description`(최대 1024자, 트리거·컨텍스트 포함 — 활성화의 핵심 필드).
  - body 500줄 미만, 명령형 작성, "When to Use" 섹션은 body가 아니라 description에 둔다.
  - 리소스 유형: `scripts/`(반복 코드), `references/`(반복 참조 스키마·도메인 지식), `assets/`(반복 보일러플레이트).
  - Degrees of freedom(high/medium/low)와 progressive disclosure 패턴 3종 제공.
  - 금지: 보조 문서(README/CHANGELOG) 생성, 정보 중복, references 중첩 디렉터리.

## 📦 출처 / 설치 상태
- 플러그인: firecrawl@claude-plugins-official
- 활성화: ON
- 관련 MCP: firecrawl은 CLI 기반(`firecrawl` 명령). 별도 MCP 서버 아님.
- 원본 유형: command (`commands/skill-gen.md`) — 슬래시 명령어
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/firecrawl/1.0.9/commands/skill-gen.md`
