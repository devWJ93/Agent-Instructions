# train-sentence-transformers

> sentence-transformers 모델(SentenceTransformer/CrossEncoder/SparseEncoder)을 학습/파인튜닝하는 라우터형 스킬.

## 📌 용도
sentence-transformers 모델을 세 가지 타입에 걸쳐 학습/파인튜닝한다: `SentenceTransformer`(bi-encoder; dense 또는 static 임베딩; 검색·유사도·클러스터링·분류·패러프레이즈 마이닝·중복제거·멀티모달), `CrossEncoder`(reranker; 쌍 점수화, 2단계 검색/쌍 분류), `SparseEncoder`(SPLADE; 학습형 sparse 검색, inverted-index 백엔드). loss 선택, hard-negative 마이닝, evaluator, distillation, LoRA, Matryoshka, Hub 발행을 커버한다. **이 SKILL.md는 매뉴얼이 아니라 라우터**로, 작업에 맞는 references와 예제 스크립트를 어디서 로드할지 알려준다. 학습 스크립트는 이 파일만으로 합성하지 말고 타입별 프로덕션 템플릿(`scripts/train_<type>_example.py`)을 복사해 시작해야 한다(autocast helper, model-card 클래스, logger silencing, seed, TF32 등 핵심 scaffolding 포함).

## 🎯 트리거 조건
- **언제 호출되는가**: 임의의 sentence-transformers 학습 작업. "embedding model"/"vector search"/"similarity"→SentenceTransformer, "rerank"/"ranker"/"two-stage"→CrossEncoder, "SPLADE"/"sparse"/"inverted index"→SparseEncoder.
- **언제 쓰지 않는가**: 일반 LM 학습(huggingface-llm-trainer/trl-training), 비전 모델(huggingface-vision-trainer). sentence-transformers가 아닌 임베딩 라이브러리.

## 💻 사용 예시 / 명령어
```bash
# 전제
pip install "sentence-transformers[train]>=5.0"   # 멀티모달은 [train,image]/[audio]/[video]
pip install trackio                                # 선택 tracker (wandb/tensorboard/mlflow 가능)
hf auth login                                      # 또는 HF_TOKEN write scope (Hub push용)
```

워크플로우: ① 모델 타입 식별(모호하면 질문) → ② 타입별 §2 필수 reading 로드 → ③ `scripts/train_<type>_example.py` 복사 → ④ MODEL_NAME/DATASET_NAME/RUN_NAME/loss/evaluator 교체(loss↔data shape, metric_for_best_model 키 교차검증) → ⑤ 스모크 테스트(`max_steps=1`) → ⑥ 실행 → ⑦ `logs/experiments.md` 추가·반복 제안.

## 🛠️ 제공 도구 / 주요 파라미터
모델 타입 3종: `SentenceTransformer`(bi-encoder, 고정차원 dense), `CrossEncoder`(쌍 joint 점수화), `SparseEncoder`(SPLADE, 어휘 sparse 벡터).

타입별 필수 reading + 템플릿:
- SentenceTransformer: `references/losses_sentence_transformer.md`, `evaluators_sentence_transformer.md`, `model_architectures.md`, `scripts/train_sentence_transformer_example.py`
- CrossEncoder: `references/losses_cross_encoder.md`(non-BCE는 `activation_fn=Identity()` 필수), `evaluators_cross_encoder.md`, `scripts/train_cross_encoder_example.py`
- SparseEncoder: `references/losses_sparse_encoder.md`(`SpladeLoss` 래퍼·FLOPS 정규화), `evaluators_sparse_encoder.md`, `scripts/train_sparse_encoder_example.py`

교차 reading(항상): `references/training_args.md`(precision: fp32 로드+autocast bf16/fp16, `torch_dtype=bfloat16` 금지; save_steps는 eval_steps 배수), `dataset_formats.md`(컬럼 매칭·hard-negative 마이닝), `base_model_selection.md`(ModernBERT max_seq_length=8192 함정, datasets>=4 script-loader 거부), `troubleshooting.md`.

적용 시 로드: `hardware_guide.md`(>24GB/멀티GPU/HF Jobs), `hf_jobs_execution.md`, `prompts_and_instructions.md`(E5/BGE/GTE/Qwen3-Embedding 등 프롬프트 튜닝 베이스).

variant 스크립트: matryoshka, multi_dataset, with_lora, distillation, make_multilingual, static_embedding(ST); distillation, listwise(CE); distillation(SE); `mine_hard_negatives.py`.

산출 스크립트 제약(비협상): `trainer.train()` 전 `baseline_eval` 캡처, 종료 라인 `VERDICT: WIN|MARGINAL|REGRESSION | score=... | baseline=... | delta=...`, httpx/httpcore/huggingface_hub 등 WARNING 무음, `logs/{RUN_NAME}.log` tee, `model.push_to_hub(...)` try/except, 긴 실행 전 스모크 테스트. CE는 `EarlyStoppingCallback(patience>=3)`, SE는 verdict에 `query/corpus_active_dims` 로깅.

기본값: 로컬 실행(맞지 않을 때만 HF Jobs), 단일 run, 종료 시 public Hub push(try-except), HF Jobs는 in-trainer push도(`push_to_hub=True`+`hub_strategy="every_save"`).

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/train-sentence-transformers/SKILL.md`
