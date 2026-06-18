# huggingface-vision-trainer

> HF Transformers + HF Jobs 클라우드 GPU로 객체탐지·이미지분류·SAM/SAM2 분할 비전 모델을 학습/파인튜닝하는 스킬.

## 📌 용도
객체 탐지(D-FINE, RT-DETR v2, DETR, YOLOS), 이미지 분류(timm: MobileNetV3, MobileViT, ResNet, ViT/DINOv3 및 임의 Transformers 분류기), SAM/SAM2 분할 모델을 관리형 클라우드 GPU(HF Jobs)에서 학습한다. 로컬 GPU 설정 없이 학습하며 결과는 자동으로 Hub에 저장된다. COCO 형식 데이터셋 준비, Albumentations 증강, mAP/mAR 평가, accuracy 메트릭, bbox/point 프롬프트 SAM 분할, DiceCE loss, 하드웨어 선택, 비용 추정, Trackio 모니터링, Hub 영속화를 커버한다. 학습 스크립트는 `HfArgumentParser`를 쓰므로 모든 설정은 Python 변수 편집이 아니라 `script_args`로 전달한다.

## 🎯 트리거 조건
- **언제 호출되는가**: 객체 탐지, 이미지 분류, SAM/SAM2, segmentation, image matting, DETR/D-FINE/RT-DETR/ViT/timm/MobileNet/ResNet, bounding box 모델 학습 또는 비전 모델을 HF Jobs에서 파인튜닝.
- **언제 쓰지 않는가**: 텍스트/언어 모델 학습(huggingface-llm-trainer/trl-training), Jobs 인프라 비-학습 질문(hugging-face-jobs 스킬: secrets/하드웨어/토큰 등), sentence-transformers(train-sentence-transformers).

## 💻 사용 예시 / 명령어
워크플로우: ① 전제 검증 → ② 데이터셋 검증(`dataset_inspector.py`) → ③ AskUserQuestion으로 데이터 크기·검증 분할·하드웨어 질문 → ④ 학습 스크립트 준비 → ⑤ `submitted_jobs/`에 저장·제출·보고.

```python
# 객체 탐지 (Python API — hf_jobs MCP 미가용 시)
from huggingface_hub import HfApi, get_token
api = HfApi()
OD_SCRIPT_ARGS = [
    "--model_name_or_path", "ustc-community/dfine-small-coco",
    "--dataset_name", "cppe-5", "--image_square_size", "640",
    "--output_dir", "dfine_finetuned", "--num_train_epochs", "30",
    "--per_device_train_batch_size", "8", "--learning_rate", "5e-5",
    "--eval_strategy", "epoch", "--save_strategy", "epoch",
    "--load_best_model_at_end", "--metric_for_best_model", "eval_map",
    "--greater_is_better", "True",         # Optional[bool]은 값 명시 필수
    "--no_remove_unused_columns",          # 필수: image 컬럼 보존
    "--no_eval_do_concat_batches",         # 필수: 이미지별 박스 수 다름
    "--push_to_hub", "--hub_model_id", "username/model-name",
    "--do_train", "--do_eval",
]
job_info = api.run_uv_job(
    script="scripts/object_detection_training.py",   # MCP는 코드/URL, run_uv_job은 파일 경로
    script_args=OD_SCRIPT_ARGS, flavor="t4-small", timeout=14400,
    env={"PYTHONUNBUFFERED": "1"},
    secrets={"HF_TOKEN": get_token()},     # run_uv_job은 get_token(), MCP는 "$HF_TOKEN"
)
print(f"Job ID: {job_info.id}")            # .id (NOT .job_id/.name)
```

```python
# hf_jobs MCP 도구 (선호) — script는 코드 문자열, timeout은 "4h", secrets는 "$HF_TOKEN"
hf_jobs("uv", {"script": training_script_content, "flavor": "a10g-large",
               "timeout": "4h", "secrets": {"HF_TOKEN": "$HF_TOKEN"}})
```

## 🛠️ 제공 도구 / 주요 파라미터
스크립트: `scripts/object_detection_training.py`, `image_classification_training.py`, `sam_segmentation_training.py`, `dataset_inspector.py`, `estimate_cost.py`.

제출 방법 차이: `hf_jobs()` MCP(script=코드/URL, timeout="4h", secrets="$HF_TOKEN") vs `HfApi().run_uv_job()`(script=파일 경로, timeout=초, secrets=get_token()). 둘 다 PEP 723 인라인 메타데이터 필요. `image`/`command` 파라미터 사용 금지(run_job용).

토큰 주입: 커스텀 스크립트는 `Trainer(...)` 생성 전 `training_args.hub_token = os.environ["HF_TOKEN"]` 주입 필수.

필수 플래그(OD): `--no_remove_unused_columns`, `--no_eval_do_concat_batches`, `--push_to_hub`, `--metric_for_best_model eval_map`, `--greater_is_better True`. (IC) `eval_accuracy`. (SAM) `--remove_unused_columns False`, `--prompt_type bbox|point`, `--dataloader_pin_memory False`.

데이터셋 요구: OD는 `objects.bbox`+`objects.category`(xywh/xyxy 자동 감지, 문자열 카테고리 자동 리매핑). IC는 `image`+`label`. SAM은 `image`+`mask`+프롬프트(`prompt` JSON 또는 `bbox`/`point` 컬럼, xyxy). 검증 결과: ✓READY / ✗NEEDS FORMATTING.

추천 모델: OD `ustc-community/dfine-small-coco`(10.4M, 시작점), IC `timm/mobilenetv3_small_100.lamb_in1k`(2.5M), SAM `facebook/sam2.1-hiera-small`(46M). 모두 <100M는 `t4-small`($0.40/hr) 충분.

타임아웃: 기본 30분 부족 — OD 최소 2-4시간, +30% 버퍼. 빠른테스트 1h, 개발 2-3h, 프로덕션 4-6h, 대형 6-12h.

Trackio: OD 스크립트는 자동 활성(`trackio.init/finish`), IC/SAM은 `--report_to trackio`. 대시보드 `https://huggingface.co/spaces/{username}/trackio`.

실패 모드: OOM(batch size↓), 데이터셋 형식, Hub push 401(토큰/hub_token/권한), timeout, KeyError 'test'(validation 폴백), 단일 클래스 0-d 텐서, 낮은 mAP. 참조: `references/timm_trainer.md`, `finetune_sam2_trainer.md`, `reliability_principles.md` 등.

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구), hf_jobs / hf_whoami MCP 도구
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-vision-trainer/SKILL.md`
