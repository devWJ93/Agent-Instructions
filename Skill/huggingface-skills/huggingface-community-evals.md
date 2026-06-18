# huggingface-community-evals

> 로컬 하드웨어에서 inspect-ai와 lighteval로 HF Hub 모델 평가(eval)를 실행하는 스킬.

## 📌 용도
Hugging Face Hub의 모델을 로컬 하드웨어에서 평가(evaluation)하는 작업을 담당한다. `inspect-ai`와 `lighteval` 두 평가 프레임워크를 로컬 추론으로 실행하며, 백엔드로 `vllm` / Hugging Face Transformers / `accelerate` 중 어떤 것을 쓸지 선택하도록 돕는다. 스모크 테스트(소량 샘플로 빠른 검증), 태스크 선택, 백엔드 폴백 전략을 포함한다. HF Jobs 오케스트레이션, model-card PR, `.eval_results` 발행, community-evals 자동화는 다루지 않는다.

## 🎯 트리거 조건
- **언제 호출되는가**: HF Hub 모델을 로컬 GPU/하드웨어에서 평가할 때. 백엔드 선택(vLLM vs Transformers vs accelerate), inspect-ai 또는 lighteval 사용, 로컬 GPU eval 실행.
- **언제 쓰지 않는가**: HF Jobs에서 원격 실행(→ `hugging-face-jobs` 스킬로 핸드오프), model-card/`model-index` 편집, README 테이블 추출, Artificial Analysis 임포트, `.eval_results` 생성/발행, PR 생성, community-evals 자동화(→ `~/code/community-evals`로 핸드오프).

## 💻 사용 예시 / 명령어
```bash
# 사전 검증
uv --version
printenv HF_TOKEN >/dev/null
nvidia-smi

# Option A: inspect-ai + Inference Providers (로컬 설정 최소)
uv run scripts/inspect_eval_uv.py --model meta-llama/Llama-3.2-1B --task mmlu --limit 20

# Option B: inspect-ai 로컬 GPU (vllm)
uv run scripts/inspect_vllm_uv.py --model meta-llama/Llama-3.2-1B --task gsm8k --limit 20
# Transformers 폴백
uv run scripts/inspect_vllm_uv.py --model microsoft/phi-2 --task mmlu --backend hf --trust-remote-code --limit 20

# Option C: lighteval 로컬 GPU (vllm)
uv run scripts/lighteval_vllm_uv.py --model meta-llama/Llama-3.2-3B-Instruct \
  --tasks "leaderboard|mmlu|5,leaderboard|gsm8k|5" --max-samples 20 --use-chat-template
# accelerate 폴백
uv run scripts/lighteval_vllm_uv.py --model microsoft/phi-2 --tasks "leaderboard|mmlu|5" \
  --backend accelerate --trust-remote-code --max-samples 20
```

## 🛠️ 제공 도구 / 주요 파라미터
제공 스크립트:
- `scripts/inspect_eval_uv.py` — Inference Providers 경유 inspect-ai eval
- `scripts/inspect_vllm_uv.py` — 로컬 GPU inspect-ai (vllm/Transformers)
- `scripts/lighteval_vllm_uv.py` — 로컬 GPU lighteval (vllm/accelerate)
- `examples/USAGE_EXAMPLES.md` — 추가 명령 패턴

태스크: inspect-ai는 `mmlu`, `gsm8k`, `hellaswag`, `arc_challenge`, `truthfulqa`, `winogrande`, `humaneval`. lighteval은 `suite|task|num_fewshot` 형식(예: `leaderboard|mmlu|5`), 콤마로 다중 지정.

주요 플래그: `--limit`(inspect 스모크), `--max-samples`(lighteval 스모크), `--backend [vllm|hf|accelerate]`, `--trust-remote-code`, `--use-chat-template`, `--batch-size`, `--gpu-memory-utilization`.

하드웨어 가이드: <3B 소비자 GPU/Apple Silicon, 3B~13B 강력 로컬 GPU, 13B+ 고메모리 GPU 또는 Jobs 핸드오프. OOM 시 batch-size/gpu-memory-utilization 축소 또는 모델 축소.

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-community-evals/SKILL.md`
