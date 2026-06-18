# huggingface-local-models

> llama.cpp + GGUF로 CPU/Mac Metal/CUDA/ROCm에서 모델을 로컬 실행하기 위한 모델 선택·실행 스킬.

## 📌 용도
Hugging Face Hub에서 llama.cpp 호환 GGUF repo를 검색하고, 알맞은 양자화(quant)를 고르고, `llama-cli` 또는 `llama-server`로 모델을 실행하는 작업을 담당한다. GGUF 찾기, quant 선택, 서버 실행, 정확한 GGUF 파일 조회, 변환, OpenAI 호환 로컬 서빙을 커버한다. 기본 워크플로우는 `apps=llama.cpp` 필터로 Hub 검색 → `?local-app=llama.cpp` 페이지에서 권장 snippet/quant 확인 → tree API로 정확한 `.gguf` 파일명 확인 → `-hf <repo>:<QUANT>`로 실행 → 커스텀 파일명이면 `--hf-repo`+`--hf-file` 폴백 → GGUF가 없을 때만 Transformers 가중치에서 변환. CPU, Mac Metal, CUDA, ROCm 가속을 지원한다.

## 🎯 트리거 조건
- **언제 호출되는가**: llama.cpp와 GGUF로 모델을 로컬 실행할 때. GGUF repo 검색, quant 선택, 로컬 서버 실행, 정확한 GGUF 파일 조회, GGUF 변환, OpenAI 호환 로컬 서빙.
- **언제 쓰지 않는가**: 클라우드 GPU 학습(huggingface-llm-trainer), 모델 추천/벤치마크 비교(huggingface-best), 브라우저/JS 추론(transformers-js). 모델 선택보다 학습/배포가 목적인 경우.

## 💻 사용 예시 / 명령어
```bash
# 설치
brew install llama.cpp          # 또는 winget install llama.cpp
git clone https://github.com/ggml-org/llama.cpp && cd llama.cpp && make

# gated repo 인증
hf auth login

# Hub 검색 (브라우저 URL)
# https://huggingface.co/models?apps=llama.cpp&sort=trending
# https://huggingface.co/models?search=<term>&apps=llama.cpp&num_parameters=min:0,max:24B&sort=trending

# Hub에서 직접 실행
llama-cli    -hf unsloth/Qwen3.6-35B-A3B-GGUF:UD-Q4_K_M
llama-server -hf unsloth/Qwen3.6-35B-A3B-GGUF:UD-Q4_K_M

# 정확한 GGUF 파일 지정 (커스텀 파일명)
llama-server --hf-repo unsloth/Qwen3.6-35B-A3B-GGUF \
    --hf-file Qwen3.6-35B-A3B-UD-Q4_K_M.gguf -c 4096

# GGUF가 없을 때만 변환
hf download <repo-without-gguf> --local-dir ./model-src
python convert_hf_to_gguf.py ./model-src --outfile model-f16.gguf --outtype f16
llama-quantize model-f16.gguf model-q4_k_m.gguf Q4_K_M

# OpenAI 호환 서버 스모크 테스트
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" -H "Authorization: Bearer no-key" \
  -d '{"messages":[{"role":"user","content":"Write a limerick about exception handling"}]}'
```

## 🛠️ 제공 도구 / 주요 파라미터
정확한 파일명 확인: `https://huggingface.co/api/models/<repo>/tree/main?recursive=true`.

Quant 선택 가이드:
- HF `?local-app=llama.cpp` 페이지가 호환으로 표시한 정확한 quant 우선.
- repo-native 라벨(예: `UD-Q4_K_M`)을 정규화하지 말고 그대로 사용.
- 기본값 `Q4_K_M`. 코드/기술 워크로드는 메모리 여유 시 `Q5_K_M`/`Q6_K`.
- 빠듯한 RAM/VRAM은 `Q3_K_M`, `Q4_K_S`, repo별 `IQ`/`UD-*` 변형.
- `mmproj-*.gguf`는 projector 가중치이지 메인 체크포인트가 아님.

참조 파일: `references/hub-discovery.md`(URL-우선 워크플로우·tree API·명령 재구성), `references/quantization.md`(포맷 테이블·스케일링·imatrix), `references/hardware.md`(Metal/CUDA/ROCm/CPU 빌드·가속).

리소스: llama.cpp GitHub, HF GGUF+llama.cpp 문서, Local Apps/Local Agents 문서, GGUF 변환 Space(`ggml-org/gguf-my-repo`).

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-local-models/SKILL.md`
