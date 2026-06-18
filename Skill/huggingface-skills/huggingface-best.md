# huggingface-best

> 특정 작업에 가장 좋은/추천 AI 모델을 HF 공식 벤치마크 리더보드로 찾아 비교표로 제시하는 스킬.

## 📌 용도
사용자의 작업(코딩, 수학/추론, 채팅, OCR, RAG/검색, 음성 인식, 이미지 분류, 멀티모달, 에이전트 등)과 디바이스 제약을 파싱하여, Hugging Face의 공식 벤치마크 리더보드를 조회한다. 모델 크기 데이터로 결과를 보강하고, 사용자의 디바이스에 맞는 모델만 필터링한 뒤 벤치마크 점수가 포함된 비교표를 반환한다. 디바이스가 명시되지 않으면 크기 필터링을 생략하고 최고 성능 모델을 반환한다. 모델 추천이나 비교가 필요할 때, 사용자가 HuggingFace나 벤치마크를 명시하지 않아도 항상 사용한다.

## 🎯 트리거 조건
- **언제 호출되는가**: "best model for X", "what model should I use for", "top models for [task]", "which model runs on my laptop/machine/device", "recommend a model for", "what LLM should I use for", "compare models for", "what's state of the art for" 등 특정 용도의 AI 모델 선택/비교 질문. HuggingFace나 벤치마크를 명시하지 않아도 트리거됨.
- **언제 쓰지 않는가**: 모델 선택/추천/비교가 아닌, 이미 정해진 모델의 학습·배포·실행 작업(해당 전용 스킬 사용).

## 💻 사용 예시 / 명령어
```bash
# Step 2: 공식 벤치마크 데이터셋 목록 조회
curl -s -H "Authorization: Bearer $(cat ~/.cache/huggingface/token)" \
  "https://huggingface.co/api/datasets?filter=benchmark:official&limit=500" \
  | jq '[.[] | {id, tags, description}]'

# Step 3: 리더보드에서 상위 모델 가져오기
curl -s -H "Authorization: Bearer $(cat ~/.cache/huggingface/token)" \
  "https://huggingface.co/api/datasets/<namespace>/<repo>/leaderboard" \
  | jq '[.[:15] | .[] | {rank, modelId, value, verified}]'

# Step 4: 모델 메타데이터 보강
hf models info org/model1 --json | jq '{safetensors, tags, cardData}'
```

디바이스 → 파라미터 예산 변환:
- fp16 최대 파라미터(B) ≈ 메모리(GB) ÷ 2
- Q4 최대 파라미터(B) ≈ 메모리(GB) × 2
- 예: 16GB → 8B fp16 / 32B Q4, 24GB VRAM → 12B fp16 / 48B Q4

## 🛠️ 제공 도구 / 주요 파라미터
6단계 워크플로우: (1) 요청 파싱(task/device) → (2) 관련 공식 벤치마크 데이터셋 선택 → (3) 리더보드 상위 모델 수집 → (4) `safetensors.total`로 파라미터·라이선스 보강 → (5) 디바이스 예산 기준 필터·랭킹 → (6) 비교표 출력.

출력 비교표 컬럼: `# / Model(링크) / Params / [Benchmark...] / License / On device`. On device 값은 `Yes (fp16)`, `Q4 only`, `Too large`, `API only`. 최상위 추천은 ⭐ 표시. 평가 안 된 벤치마크는 `—`.

에러 처리: 리더보드 404/401 → 스킵 후 표기, `safetensors` 없으면 모델명에서 크기 파싱(7b/13b/70b 등), 벤치마크 미발견 시 `hub_repo_search`로 트렌딩/인기 기반 폴백.

후속: 표 제시 후 "Run locally" 또는 "Run on HF Jobs" 안내.

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-best/SKILL.md`
