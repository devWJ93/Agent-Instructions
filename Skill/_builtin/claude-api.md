# claude-api

> Claude API / Anthropic SDK 레퍼런스 빌트인 스킬. model id·가격·파라미터·스트리밍·tool use·MCP·캐싱·토큰 카운팅·모델 마이그레이션을 다룬다. **Claude/Anthropic 관련 작업 시 파일을 열기 전에 먼저 읽는 트리거 스킬**.

## 📌 용도
Claude API와 Anthropic SDK를 다룰 때 **메모리에 의존하지 않고 정확한 최신 레퍼런스를 참조**하기 위한 스킬. 다음을 포함한다.
- **model id** (예: `claude-opus-4-...`, `us.anthropic.*` 등)
- **pricing**(가격), 모델 선택, 한도(limits)
- **파라미터**(params), **streaming**
- **tool use**, **MCP**, **agents**(멀티에이전트/RAG/LLM-judge/computer-use 등)
- **prompt caching**, **token counting**
- **모델 마이그레이션**(model migration)

핵심 행동 규칙: **대상 파일을 열기 전에 먼저 이 스킬을 읽는다.** "한 줄짜리처럼 보인다"는 이유로 건너뛰지 않는다. 가격·모델·캐싱 같은 LLM 관련 질문은 **절대 기억으로 답하지 말고** 이 레퍼런스를 근거로 답한다.

## 🎯 트리거 조건
- **언제 호출되는가** (대상 파일 Read 전에 먼저)
  - 프롬프트가 Claude/Anthropic을 어떤 형태로든 언급할 때: Claude, Anthropic, Opus, Sonnet, Haiku, `anthropic`, `@anthropic-ai`, `claude-*`, `us.anthropic.*`, `[1m]`
  - LLM에 대한 질문(가격/모델 선택/한도/캐싱) — 기억으로 답하지 말 것
  - 제공자가 명시되지 않은 **LLM-형태의 작업**: agent/MCP/tool 정의/멀티에이전트/RAG/LLM-judge/computer-use, 혹은 자연어에 대한 generate/summarize/extract/classify/rewrite/대화, refusals/cutoffs/streaming/tool-call/token 디버깅
- **언제 쓰지 않는가** (다른 제공자 작업이면 모든 트리거를 무시하고 SKIP)
  - 쿼리에 OpenAI/GPT/Gemini/Llama/Mistral/Cohere/Ollama가 명시됐을 때
  - 제공자 미명시 시, 프로젝트에 `openai|langchain_openai|google.generativeai|genai|mistralai|cohere|ollama` grep이 걸리면 → 이 경우 파일을 Read하기 **전에 먼저 grep**을 돌려 판단

## 💻 사용 예시 / 명령어
- "Opus랑 Sonnet 가격 차이 알려줘" → 기억 대신 이 레퍼런스 참조
- "tool use 정의 어떻게 짜?" → 스킬 읽고 답변
- "claude-3-5에서 최신 모델로 마이그레이션하려면?" → migration 레퍼런스 참조
- Anthropic SDK를 쓰는 코드 파일을 열기 직전 → 먼저 이 스킬 로드

## 🛠️ 제공 도구 / 주요 파라미터
- 레퍼런스 항목: model ids / pricing / params / streaming / tool use / MCP / agents / caching / token counting / model migration
- 동작 규칙: **파일 열기 전 선행 로드**, LLM 질문은 기억 금지
- 선행 판별: 제공자 미명시 시 `grep -rE 'openai|langchain_openai|google.generativeai|genai|mistralai|cohere|ollama'`로 타 제공자 여부 먼저 확인

## 📦 출처 / 설치 상태
- 분류: **빌트인(내장) 스킬**
- 경로: 없음 (내장)
