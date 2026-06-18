# new-sdk-app

> Claude Agent SDK 애플리케이션(Python / TypeScript)을 대화형으로 새로 만들고, 최신 SDK 설치·검증까지 끝내는 슬래시 명령어.

## 📌 용도

`/new-sdk-app`은 Claude Agent SDK 기반 애플리케이션의 **초기 스캐폴딩부터 검증까지 전체 생애주기**를 자동화하는 명령어다. 빈 디렉터리에서 시작해 다음을 수행한다.

- 프로젝트에 대한 질문(언어, 이름, 에이전트 종류, 시작점, 패키지 매니저)을 **한 번에 하나씩** 던져 요구사항을 수집
- npm/PyPI에서 **최신 SDK 버전을 확인**한 뒤 설치 (구버전 박제 방지)
- 프로젝트 디렉터리, 패키지 매니저 초기화, 설정 파일(`tsconfig.json`, `package.json`, `requirements.txt` 등) 생성
- 사용자의 에이전트 유형에 맞춘 **동작하는 예제 코드** 작성
- `.env.example` / `.gitignore` 등 환경·보안 파일 세팅
- TypeScript는 `npx tsc --noEmit` 타입 체크, Python은 import·문법 검증으로 **코드가 실제로 통과할 때까지** 수정
- 마지막에 언어별 **검증 에이전트**(`agent-sdk-verifier-ts` / `agent-sdk-verifier-py`)를 실행해 베스트 프랙티스 준수 여부 리포트

핵심 철학은 "항상 최신 버전 사용 + 검증 전엔 완료로 간주하지 않음 + 대화형·교육적 경험"이다.

## 🎯 트리거 조건

- **언제 호출되는가**
  - 사용자가 `/new-sdk-app` 또는 `/agent-sdk-dev:new-sdk-app`을 직접 입력할 때
  - 새 Claude Agent SDK 프로젝트를 처음부터 만들고 싶을 때 (예: "고객 지원 에이전트용 SDK 프로젝트 만들어줘")
  - Python/TypeScript 중 어느 쪽이든 SDK 스타터를 빠르게 세팅하고 싶을 때

- **언제 쓰지 않는가**
  - 이미 존재하는 SDK 프로젝트를 *수정*하거나 *검증*만 하고 싶을 때 → 검증 에이전트(`agent-sdk-verifier-*`)를 직접 부르는 편이 적합
  - SDK와 무관한 일반 애플리케이션 개발일 때 (`/feature-dev` 등이 적합)
  - 단순히 SDK 사용법만 묻는 경우 (코드 생성 없이 문서 참고로 충분)

## 💻 사용 예시 / 명령어

기본 호출 (프로젝트명 미지정 → 대화로 물어봄):
```
/new-sdk-app
```

프로젝트명을 인자로 전달 (`argument-hint: [project-name]`):
```
/new-sdk-app my-project-name
/new-sdk-app customer-support-agent
```

호출 시나리오 예시:
```
/new-sdk-app code-reviewer-agent
# → 언어/에이전트 유형/시작점 질문에 답변
#   Language: TypeScript
#   Agent type: Coding agent (code review)
#   Starting point: Basic agent with common features
# → 최신 SDK 설치 → 예제 생성 → npx tsc --noEmit 통과
# → agent-sdk-verifier-ts 자동 실행으로 설정 검증
```

대화 중 명령어가 **순서대로 한 번에 하나씩** 묻는 질문:
1. 언어 (TypeScript or Python) — 가장 먼저
2. 프로젝트 이름 (인자로 줬으면 생략)
3. 에이전트 유형 (코딩 / 비즈니스 / 커스텀)
4. 시작점 (최소 Hello World / 공통 기능 포함 기본 / 유스케이스별 예제)
5. 툴링 선호 (npm/yarn/pnpm/bun, pip/poetry)

## 🛠️ 제공 도구 / 주요 파라미터

**참조 문서 (WebFetch로 최신 내용 확인 후 진행):**
- 개요: `https://docs.claude.com/en/api/agent-sdk/overview`
- TypeScript 레퍼런스: `https://docs.claude.com/en/api/agent-sdk/typescript`
- Python 레퍼런스: `https://docs.claude.com/en/api/agent-sdk/python`
- Streaming, Permissions, Custom Tools, MCP, Subagents, Sessions 등 관련 가이드

**최신 버전 확인 대상:**
- TypeScript: `https://www.npmjs.com/package/@anthropic-ai/claude-agent-sdk`
- Python: `https://pypi.org/project/claude-agent-sdk/`

**설치 명령:**
- TypeScript: `npm install @anthropic-ai/claude-agent-sdk@latest` (설치 후 `npm list`로 버전 확인)
- Python: `pip install claude-agent-sdk` (설치 후 `pip show claude-agent-sdk`로 확인)

**생성하는 파일:**
- TypeScript: `package.json`(type: module, typecheck 스크립트 포함), `tsconfig.json`, `index.ts` 또는 `src/index.ts`
- Python: `requirements.txt` 또는 `poetry init`, `main.py`
- 공통: `.env.example`(`ANTHROPIC_API_KEY=...`), `.gitignore`(`.env` 추가), 선택적으로 `.claude/` 구조

**검증 절차 (완료 전 필수):**
- TypeScript: `npx tsc --noEmit` 실행 → **모든 타입 에러 해결 전까지 완료 아님**
- Python: import·기본 문법 검증

**연계 에이전트 (명령어가 자동 위임):**
- `agent-sdk-verifier-ts` — TypeScript 프로젝트 검증. SDK 설치/버전, tsconfig, 타입 안정성, 에이전트 초기화, 환경·보안(.env, API 키), 에러 처리, 문서 완성도 점검 → PASS / PASS WITH WARNINGS / FAIL 리포트
- `agent-sdk-verifier-py` — Python 프로젝트 검증. SDK 설치/버전, 환경 설정(requirements.txt/pyproject.toml), SDK 사용 패턴, 보안, 에러 처리 점검 → 동일 형식 리포트

**완료 후 안내:** API 키 설정 방법, 실행 방법(`npm start` / `python main.py`), 시스템 프롬프트·권한·커스텀 툴·서브에이전트 커스터마이징 가이드.

## 📦 출처 / 설치 상태

- 플러그인: agent-sdk-dev@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/agent-sdk-dev/unknown/commands/new-sdk-app.md`
