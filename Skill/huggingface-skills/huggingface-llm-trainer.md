# huggingface-llm-trainer

> TRL/Unsloth와 Hugging Face Jobs 클라우드 GPU로 언어·비전 모델을 학습/파인튜닝하는 스킬 (SFT/DPO/GRPO/Reward + GGUF 변환).

## 📌 용도
TRL(Transformer Reinforcement Learning) 또는 Unsloth를 사용해 완전 관리형 Hugging Face 인프라(클라우드 GPU)에서 언어·비전 모델을 학습한다. 로컬 GPU 설정 없이 학습하며 결과는 자동으로 Hub에 저장된다. SFT(지도 파인튜닝), DPO(선호 데이터 정렬), GRPO(온라인 RL), Reward Modeling 학습 방법과, 로컬 배포용 GGUF 변환을 커버한다. TRL Jobs 패키지, PEP 723 형식 UV 스크립트, 데이터셋 준비/검증, 하드웨어 선택, 비용 추정, Trackio 모니터링, Hub 인증, 모델 선택/리더보드, 모델 영속화 가이드를 포함한다. 학습 환경은 ephemeral이므로 Hub push가 필수다.

## 🎯 트리거 조건
- **언제 호출되는가**: 로컬 인프라 없이 클라우드 GPU에서 LM 파인튜닝, TRL 방법(SFT/DPO/GRPO) 학습, Hugging Face Jobs 인프라에서 학습 잡 실행, GGUF 변환(Ollama/LM Studio/llama.cpp용), 모델을 Hub에 영속 저장. 사용자가 "train a model", "fine-tune" 또는 로컬 GPU 없이 HF Jobs에서 학습을 언급할 때.
- **언제 쓰지 않는가**: 로컬 GPU에서 직접 평가(huggingface-community-evals), 비전 전용 학습 작업은 huggingface-vision-trainer, sentence-transformers는 train-sentence-transformers, CLI 명령만 다룰 땐 trl-training이 더 적합. 제한 GPU 메모리/속도/대형 모델(>13B)/VLM은 Unsloth(`references/unsloth.md`) 사용.

## 💻 사용 예시 / 명령어
```python
# Approach 1 (기본): hf_jobs() MCP + 인라인 UV 스크립트 (PEP 723)
hf_jobs("uv", {
    "script": """
# /// script
# dependencies = ["trl>=0.12.0", "peft>=0.7.0", "trackio"]
# ///
from datasets import load_dataset
from peft import LoraConfig
from trl import SFTTrainer, SFTConfig
import trackio

dataset = load_dataset("trl-lib/Capybara", split="train")
split = dataset.train_test_split(test_size=0.1, seed=42)
trainer = SFTTrainer(
    model="Qwen/Qwen2.5-0.5B",
    train_dataset=split["train"], eval_dataset=split["test"],
    peft_config=LoraConfig(r=16, lora_alpha=32),
    args=SFTConfig(output_dir="my-model", push_to_hub=True,
        hub_model_id="username/my-model", num_train_epochs=3,
        report_to="trackio", project="proj", run_name="run"),
)
trainer.train(); trainer.push_to_hub()
""",
    "flavor": "a10g-large", "timeout": "2h", "secrets": {"HF_TOKEN": "$HF_TOKEN"}
})
```

```bash
# Approach 3: HF Jobs CLI (플래그는 스크립트 URL 앞에, --secrets 복수형)
hf jobs uv run --flavor a10g-large --timeout 2h --secrets HF_TOKEN \
  "https://huggingface.co/user/repo/resolve/main/train.py"
hf jobs ps; hf jobs logs <job-id>; hf jobs inspect <job-id>; hf jobs cancel <job-id>

# Approach 4: trl-jobs 패키지 (터미널 직접 작업 시)
uvx trl-jobs sft --model_name Qwen/Qwen2.5-0.5B --dataset_name trl-lib/Capybara

# 비용 추정 / 데이터셋 검증
uv run scripts/estimate_cost.py --model meta-llama/Llama-2-7b-hf --dataset trl-lib/Capybara --hardware a10g-large --dataset-size 16000 --epochs 3
```

## 🛠️ 제공 도구 / 주요 파라미터
핵심 디렉티브: ① 항상 `hf_jobs("uv", {...})` MCP 도구로 인라인 스크립트 제출(로컬 파일 경로 불가, 인라인/URL만), ② 항상 Trackio 포함, ③ 제출 후 job ID·모니터링 URL·예상시간 보고, ④ 비동기이므로 폴링 금지·사용자 요청 시 상태 확인.

학습 방법 4가지 접근: (1) UV 스크립트 인라인(기본), (2) TRL 공식 유지보수 스크립트 URL+`script_args`, (3) `hf jobs uv run` CLI, (4) `uvx trl-jobs` 패키지.

시퀀스 길이: TRL config는 `max_length`(NOT `max_seq_length`), 기본 1024, 비전 모델은 `max_length=None`.

하드웨어: <1B t4-small, 1-3B t4-medium/l4x1, 3-7B a10g-small/large, 7-13B a10g-large/a100-large(LoRA), 13B+ a100-large/a10g-largex2(LoRA). >7B는 LoRA/PEFT 사용.

필수 설정: `push_to_hub=True` + `hub_model_id="username/model"` + `secrets={"HF_TOKEN": "$HF_TOKEN"}`. timeout 기본 30분은 너무 짧음 — 최소 1-2시간, 20-30% 버퍼 추가.

데이터셋 검증: GPU 학습 전 `dataset_inspector.py`(CPU ~$0.01)로 형식 검증, 특히 DPO(prompt/chosen/rejected). 출력 마커 ✓READY/✗NEEDS MAPPING/✗INCOMPATIBLE.

GGUF 변환: 학습 후 4/5/8-bit 양자화로 llama.cpp/Ollama/LM Studio용 변환(`references/gguf_conversion.md`).

스크립트: `scripts/train_{sft,dpo,grpo}_example.py`, `unsloth_sft_example.py`, `estimate_cost.py`, `convert_to_gguf.py`, `hf_benchmarks.py`. 실패 모드: OOM(batch size↓, gradient_checkpointing, 하드웨어↑), 데이터셋 형식, timeout, Hub push 실패.

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구), hf_jobs / hf_whoami / hf_doc_search MCP 도구
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-llm-trainer/SKILL.md`
