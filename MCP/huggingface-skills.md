# huggingface-skills

> Hugging Face Hub 공식 원격 MCP 서버. OAuth 로그인을 거쳐 HF 생태계(모델·데이터셋·Spaces·Jobs 등) 작업을 연동한다.

## 📌 용도
Hugging Face의 공식 HTTP MCP 서버(`https://huggingface.co/mcp?login`)에 연결한다. MCP 서버가 직접 노출하는 도구는 **인증(OAuth) 2개**이며, 모델 추천/데이터셋 조회/Spaces 배포/Jobs 학습/논문 조회 등 실제 기능은 `huggingface-skills` **스킬군**(hf-cli, huggingface-best, huggingface-datasets, huggingface-spaces 등)을 통해 수행된다. 이 MCP는 HF 계정 인증 게이트웨이 + 스킬의 백엔드 역할을 한다.

## 🎯 트리거 조건 / 사용 맥락
- "hf", "huggingface", "Hugging Face", "허깅페이스" 언급
- 특정 작업에 쓸 최적 모델 추천/비교, 모델·데이터셋 다운로드/업로드
- HF Spaces(Gradio/Docker/Static, ZeroGPU) 앱 생성·배포·디버깅
- HF Jobs 클라우드 GPU로 학습/파인튜닝, 논문 조회 등
- 위 작업 전 HF 계정 연동이 필요할 때 (authenticate → complete_authentication)

## 🛠️ 제공 도구 목록

| 도구명 | 기능 | 주요 파라미터 |
|--------|------|----------------|
| authenticate | Hugging Face OAuth 인증 시작. 로그인/승인용 URL 발급 | (없음/내부 처리) |
| complete_authentication | 사용자가 로그인 완료 후 인증 마무리, 토큰 확정 | 인증 콜백/코드 |

> 실제 HF 기능은 MCP의 deferred tool로 노출되지 않고 `huggingface-skills:*` 스킬을 통해 호출된다. 주요 스킬: `hf-cli`(Hub CLI 전반), `huggingface-best`(모델 추천), `huggingface-datasets`(데이터셋 뷰어 API), `huggingface-spaces`/`huggingface-zerogpu`(Spaces·ZeroGPU), `huggingface-llm-trainer`/`trl-training`(학습), `huggingface-papers`/`huggingface-paper-publisher`(논문) 등.

## 💻 사용 예시

**예시 1 — 최초 연동**
1. HF 관련 작업 요청 발생
2. `authenticate` 호출 → 발급 URL에서 사용자가 Hugging Face 로그인/권한 승인
3. `complete_authentication`으로 연동 완료 → 이후 hf 스킬/CLI 사용 가능

**예시 2 — 작업용 모델 추천**
- 인증 후 `huggingface-skills:huggingface-best` 스킬로 "X 작업에 가장 좋은 모델" 벤치마크 기반 추천 (백엔드로 본 MCP 사용)

## 📦 출처 / 설치 상태
- 제공: **플러그인** `huggingface-skills@claude-plugins-official`
- 활성화: ON (`settings.json` → `enabledPlugins["huggingface-skills@claude-plugins-official"]: true`)
- 연결 방식: **http** — `https://huggingface.co/mcp?login` (원격 서버, OAuth 로그인 필요)
- 캐시에 여러 버전 공존: `huggingface-skills\1.0.3 / 1.0.4 / 1.0.5 / 1.0.8\.mcp.json` (현재 최신 1.0.8 기준 동일 URL)
- 설정/캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/.mcp.json`
- 세션 내 도구 식별자 접두사: `mcp__plugin_huggingface-skills_huggingface-skills__`
