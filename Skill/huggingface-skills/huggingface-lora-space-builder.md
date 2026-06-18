# huggingface-lora-space-builder

> 사용자가 제공한 LoRA를 위한 Gradio 데모를 Hugging Face Spaces(ZeroGPU, 기본 private)에 빌드·발행하는 스킬.

## 📌 용도
사용자가 제공한 LoRA로 추론을 실행하는 Gradio 데모를 Hugging Face Spaces에 빌드하고 발행한다. Qwen-Image, Qwen-Image-Edit, LTX-Video, Wan, FLUX, SDXL 등 diffusion 베이스 모델의 LoRA를 대상으로 한다. 알맞은 베이스 파이프라인과 `diffusers` 추론 레시피 선택, LoRA의 작업·입력(Union/멀티태스크 컨트롤, 편집, 비디오, 이미지 등)에 맞춘 UI 설계, 모델카드 권장사항(트리거 워드, 스텝, 가이던스, LoRA scale, 예시 입력) 준수, ZeroGPU 하드웨어에 기본 private Space로 배포를 커버한다. 출력은 로컬 스크립트가 아니라 브라우저에서 바로 시험할 수 있는 실제 발행된 Space다. 핵심은 "이 LoRA만을 위한 손수 만든 데모"이지 범용 템플릿이 아니다.

## 🎯 트리거 조건
- **언제 호출되는가**: LoRA를 위한 Space/데모/Gradio 앱/플레이그라운드를 만들거나(create/generate/ship/publish) 발행해 달라고 할 때. Qwen-Image(-Edit), LTX-Video, Wan, FLUX, SDXL 등 diffusion 베이스의 LoRA. 학습했거나 Hub에 호스팅한 LoRA를 공유하고 싶다고 묘사할 때.
- **언제 쓰지 않는가**: 일반 Gradio 앱 작성(huggingface-gradio), ZeroGPU 코드 제약 일반(huggingface-zerogpu), 비-LoRA Space 배포/디버깅(huggingface-spaces). LoRA 없이 베이스 모델만 데모하는 경우.

## 💻 사용 예시 / 명령어
6단계 워크플로우: ① LoRA 정보 수집 → ② 베이스 파이프라인 선택 → ③ 이 LoRA만의 UI 설계 → ④ app.py/requirements.txt/README.md 함께 작성 후 한 번에 승인 → ⑤ Space 발행(private) → ⑥ 스모크 테스트.

```python
# Phase 1: 인증 + repo 읽기
from huggingface_hub import HfApi, get_token, ModelCard
cached_token = get_token()  # HF_TOKEN env 또는 캐시된 CLI 로그인
api = HfApi(token=cached_token)
files = api.list_repo_files(repo_id)        # .safetensors, README 등 확인
card = ModelCard.load(repo_id)              # data dict + text(README)

# Phase 2: 베이스 모델 카드의 diffusers snippet에서 파이프라인 클래스 검증(필수)
base_card = ModelCard.load(base_model_id)   # 임포트된 클래스가 진실의 원천

# Phase 5: Space 발행 (private, ZeroGPU)
from huggingface_hub import HfApi, SpaceHardware
api = HfApi(token=hf_token)
repo_id = f"{api.whoami()['name']}/{space_name}"
api.create_repo(repo_id=repo_id, repo_type="space", space_sdk="gradio",
    space_hardware=SpaceHardware.ZERO_A10G, private=True, exist_ok=True)
for path in ["app.py", "requirements.txt", "README.md"]:
    api.upload_file(path_or_fileobj=path, path_in_repo=path, repo_id=repo_id, repo_type="space")
api.add_space_secret(repo_id=repo_id, key="HF_TOKEN", value=HF_TOKEN)  # private LoRA 런타임용
```

```bash
# Phase 6: 스모크 테스트 (gradio CLI)
gradio info {repo_id} --token $HF_TOKEN
gradio predict {repo_id} /predict '{"prompt": "...", "aspect_ratio": "1:1"}' --token $HF_TOKEN
```

## 🛠️ 제공 도구 / 주요 파라미터
참조 파일: `references/base-models/qwen-image.md`(Qwen-Image/Edit), `references/base-models/ltx.md`(LTX 패밀리), `references/tasks.md`(T2I/I2I/T2V/I2V/V2V 베이스 UI), `references/adapting-to-the-lora.md`(이 LoRA만의 필요 분석 — 가장 중요), `references/creative-mode.md`(gr.HTML 커스텀 JS), `references/zerogpu-and-publishing.md`(ZeroGPU 규칙·발행).

처리 5작업: `text-to-image`, `image-to-image`, `text-to-video`, `image-to-video`, `video-to-video`.

파이프라인 클래스 검증(필수): 베이스 모델 카드의 diffusers snippet이 진실의 원천. 예) Qwen-Image-Edit는 `QwenImageEditPipeline`, 2509/2511은 `QwenImageEditPlusPipeline`(클래스·기본 파라미터·입력 형태 다름). LTX-Video는 `LTXPipeline` 등, LTX-2는 `LTX2Pipeline`.

ZeroGPU 규칙: 모델은 module-level에서 `cuda`에 배치(lazy 로딩 금지), 추론 함수는 `@spaces.GPU(duration=...)`, `torch.compile` 금지.

requirements.txt: app.py 임포트 + 베이스 모델 reference의 "Required dependencies" + LoRA 카드 pip + diffusers/transformers/accelerate/peft/safetensors. `gradio`·`torch`·`spaces`·`huggingface_hub`는 넣지 않음(런타임 제공, README의 `sdk_version`로 gradio 제어). `xformers`/`flash-attn`은 반사적 추가 금지.

README YAML: `sdk: gradio`, `sdk_version`(현재 버전), `hardware: zero-a10g`(실제는 RTX Pro 6000 Blackwell, PRO/Team/Enterprise 필요), `models:`(base+lora), `short_description`(~60자, 초과 시 validate-yaml 400).

발행/빌드 실패 처리: validate-yaml 400(short_description 단축), zero-a10g 403(PRO 아님 → space_hardware 빼고 생성, CPU 폴백), weight_name 불일치, gated 베이스 모델 토큰 누락, diffusers 버전(git 설치) 등.

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-lora-space-builder/SKILL.md`
