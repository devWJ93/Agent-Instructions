# trl-training

> TRL(Transformers Reinforcement Learning) CLI 명령으로 LM을 학습/파인튜닝하는 스킬 (SFT/DPO/GRPO/KTO/RLOO/Reward).

## 📌 용도
TRL 라이브러리의 CLI 명령으로 트랜스포머 언어 모델을 학습/파인튜닝한다. SFT(지도 파인튜닝), DPO(선호 데이터 정렬), GRPO(여러 샘플 출력을 상대 보상으로 랭킹·최적화), RLOO(생성 기반 보상 온라인 RL), Reward Model 학습(RLHF용)을 지원한다. Hugging Face Transformers와 Accelerate 위에 구축되어 HF 생태계와 매끄럽게 통합된다. YAML 설정 파일로 재현 가능한 학습을 지원하고, Accelerate로 멀티 GPU/멀티 노드(FSDP, DeepSpeed ZeRO) 분산 학습을 한다. LoRA(`--use_peft`)로 효율적 학습이 가능하다.

## 🎯 트리거 조건
- **언제 호출되는가**: TRL CLI 명령(`trl sft`/`dpo`/`grpo`/`kto`/`rloo`/`reward`)으로 LM 학습/파인튜닝. SFT/DPO/GRPO/RLOO/Reward Model 학습.
- **언제 쓰지 않는가**: 클라우드 GPU/HF Jobs 기반 학습이나 인라인 UV 스크립트(huggingface-llm-trainer가 hf_jobs MCP로 다룸), 비전 모델(huggingface-vision-trainer), sentence-transformers(train-sentence-transformers). 본 스킬은 로컬/터미널 TRL CLI 중심.

## 💻 사용 예시 / 명령어
```bash
# SFT (지도 파인튜닝)
trl sft --model_name_or_path Qwen/Qwen2-0.5B --dataset_name trl-lib/Capybara \
  --learning_rate 2.0e-5 --num_train_epochs 1 --packing \
  --per_device_train_batch_size 2 --gradient_accumulation_steps 8 \
  --eos_token '<|im_end|>' --eval_strategy steps --eval_steps 100 \
  --output_dir Qwen2-0.5B-SFT --push_to_hub
# LoRA: --use_peft --lora_r 32 --lora_alpha 16 (learning_rate는 2.0e-4)

# DPO (선호 정렬)
trl dpo --dataset_name trl-lib/ultrafeedback_binarized --model_name_or_path Qwen/Qwen2-0.5B-Instruct \
  --learning_rate 5.0e-7 --max_steps 1000 --per_device_train_batch_size 2 \
  --gradient_accumulation_steps 8 --eval_strategy steps --eval_steps 50 \
  --output_dir Qwen2-0.5B-DPO --no_remove_unused_columns

# GRPO (보상 함수/LLM-as-judge)
trl grpo --model_name_or_path Qwen/Qwen2.5-0.5B --dataset_name trl-lib/gsm8k \
  --reward_funcs accuracy_reward --output_dir Qwen2-0.5B-GRPO --push_to_hub

# RLOO (온라인 RL)
trl rloo --model_name_or_path Qwen/Qwen2.5-0.5B --dataset_name trl-lib/tldr \
  --reward_model_name_or_path sentiment-analysis:nlptown/bert-base-multilingual-uncased-sentiment \
  --output_dir Qwen2-0.5B-RLOO --push_to_hub

# Reward Model
trl reward --model_name_or_path Qwen/Qwen2-0.5B-Instruct --dataset_name trl-lib/ultrafeedback_binarized \
  --output_dir Qwen2-0.5B-Reward --per_device_train_batch_size 8 --num_train_epochs 1 \
  --learning_rate 1.0e-5 --eval_strategy steps --eval_steps 50 --max_length 2048
# LoRA: --use_peft --lora_task_type SEQ_CLS --lora_r 32 --lora_alpha 16
```

```bash
# YAML 설정 + 오버라이드
trl sft --config sft_config.yaml
trl sft --config sft_config.yaml --learning_rate 1.0e-5

# 분산 학습 (Accelerate)
trl sft --config sft_config.yaml --num_processes 4
trl sft --config sft_config.yaml --accelerate_config zero2     # fsdp2, zero3 등
```

## 🛠️ 제공 도구 / 주요 파라미터
코어 명령: `trl sft`, `trl dpo`, `trl grpo`, `trl kto`, `trl rloo`, `trl reward`.

학습 방법: SFT(명령/대화 데이터셋), DPO(chosen/rejected 선호), GRPO(상대 보상 랭킹), RLOO(생성 기반 보상), Reward Model(텍스트 품질 점수).

설정 파일: YAML로 모든 CLI 인자 지정, `--config`로 실행, CLI 플래그로 오버라이드.

분산 학습: `--num_processes`(멀티 GPU), 사전 정의 config `single_gpu`/`multi_gpu`/`fsdp1`/`fsdp2`/`zero1`/`zero2`/`zero3`, `--accelerate_config`, 커스텀은 `accelerate config` + `--config_file`.

주요 옵션: `--use_peft`(LoRA), `--lora_r`/`--lora_alpha`/`--lora_task_type`, `--packing`(짧은 시퀀스), `--gradient_checkpointing`, `--tf32`/`--bf16`, `--push_to_hub`, `--report_to trackio|wandb|tensorboard`, `--reward_funcs`(GRPO), `--reward_model_name_or_path`(RLOO).

트러블슈팅: CUDA OOM(batch size↓/grad accum↑, use_peft, gradient_checkpointing), 데이터셋 로딩(형식·`--dataset_config`), 모델 로딩(`hf auth login` for gated), 느린 학습(packing, bf16, tf32, 멀티GPU), 생성 이슈(GRPO/RLOO의 temperature/top_p/reward 함수).

베스트 프랙티스: 선호 정렬 전 SFT 먼저, LoRA로 효율, `--report_to`로 모니터링, 작은 데이터셋 선검증, YAML config 재현성, Accelerate 활용.

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/trl-training/SKILL.md`
