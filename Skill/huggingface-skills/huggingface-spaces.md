# huggingface-spaces

> Hugging Face Spaces 앱(Gradio/Docker/Static, ZeroGPU·전용 하드웨어)을 빌드·배포·디버깅·유지보수하는 스킬.

## 📌 용도
Hugging Face Spaces에서 머신러닝 애플리케이션을 만들고, 빌드하고, 디버깅하고, 유지보수하는 작업을 담당한다. 각 Space는 git repo이며 Gradio/Docker/Static SDK를 가질 수 있다. ZeroGPU와 전용 하드웨어, 모델 로딩, 디버깅, 버킷(영속 스토리지), 인퍼런스 프로바이더, 커뮤니티 그랜트를 커버한다. 코드를 ZeroGPU로 포팅하거나, 빌드/실행이 안 되는 Space를 고치거나, `hf spaces …`·`@spaces.GPU`·Space README frontmatter·`spaces` Python 패키지를 다루는 작업에 사용한다. 핵심 원칙은 "로컬이 아니라 라이브 Space에서 반복(iterate)"하는 것이다.

## 🎯 트리거 조건
- **언제 호출되는가**: HF에 앱 생성/호스팅, 코드를 ZeroGPU로 포팅, 빌드/실행 안 되는 Space 수정, `hf spaces` 명령·`@spaces.GPU`·Space README frontmatter·`spaces` Python 패키지 작업.
- **언제 쓰지 않는가**: LoRA 전용 Space 빌드(huggingface-lora-space-builder), ZeroGPU 코드 제약 디테일만(huggingface-zerogpu — 단 본 스킬도 references/zerogpu.md로 다룸), Gradio 앱 자체 작성(huggingface-gradio).

## 💻 사용 예시 / 명령어
```bash
# 0. 준비
which hf || pip install -U huggingface_hub
hf auth whoami           # canPay, isPro 플래그가 하드웨어 선택 좌우

# 2. 기존 데모 먼저 검색
hf spaces search "<model name or task>" --sdk gradio --limit 10

# 4. Space 생성
hf repos create <ns>/<name> --type space --space-sdk <gradio|docker|static> \
    --flavor zero-a10g|cpu-basic|<paid> --secrets KEY=val --env KEY=val \
    --public|--private|--protected --exist-ok

# 7. 검증 (RUNNING만 믿지 말 것)
hf spaces info <ns>/<name> --expand runtime
hf spaces logs <ns>/<name> --tail 200
hf spaces logs <ns>/<name> --follow

# 8. 영속 스토리지 (버킷)
hf buckets create <ns>/<bucket-name>
hf spaces volumes set <ns>/<space> -v hf://buckets/<ns>/<bucket-name>:/data

# 9. 디버깅
hf spaces logs <id> --build --follow    # 빌드 에러
hf spaces logs <id> --follow            # 런타임 에러 (첫 에러를 찾을 것)
```

```python
# 최소 ZeroGPU Gradio 앱
import spaces           # torch/diffusers/transformers보다 먼저!
import torch, gradio as gr
from diffusers import DiffusionPipeline
pipe = DiffusionPipeline.from_pretrained("<repo>", torch_dtype=torch.bfloat16).to("cuda")

@spaces.GPU(duration=60)
def generate(prompt):
    return pipe(prompt).images[0]
gr.Interface(fn=generate, inputs=gr.Text(), outputs=gr.Image()).launch()
```

## 🛠️ 제공 도구 / 주요 파라미터
SDK 3종: Gradio(대부분, ZeroGPU 지원), Docker(임의 컨테이너, ZeroGPU 미지원), Static(브라우저 ML/프로젝트 페이지, 하드웨어 불필요).

하드웨어: 무료 — `cpu-basic`(2vCPU/16GB), `zero-a10g`(ZeroGPU, RTX PRO 6000 Blackwell sm_120, large 48GB/xlarge 96GB, Gradio 전용·PyTorch 우선·PRO/Team/Enterprise 필요). 전용 GPU(T4/L4/A10G/L40S/A100/H200, 시간당 과금, `canPay=True`). 비-PRO는 cpu-basic 생성 후 커뮤니티 그랜트 신청.

README frontmatter: `title`, `emoji`, `colorFrom`/`colorTo`(지정 8색만), `sdk`, `sdk_version`(최신 안정, 5.x는 5.50.0), `app_file`, `short_description`(≤60자), `python_version`("3.10.13"/"3.12.12"), `startup_duration_timeout`. 주의: `hardware:`는 README에서 무시됨 — `--flavor` 또는 `hf spaces settings --hardware`로만 설정.

ZeroGPU 3규칙: ① `import spaces`를 torch보다 먼저(`torch.cuda.*` 몽키패치), ② 모델은 module scope에서 `.to("cuda")` 즉시 로드, ③ Gradio가 바인딩하는 함수에 `@spaces.GPU(duration=...)`.

requirements.txt: `gradio`/`spaces`/`huggingface_hub` 미기재(플랫폼 관리), `torchvision`/`torchaudio` 등은 기재. torch는 `2.8.0`/`2.9.1`/`2.10.0`/`2.11.0`만, 기본 unpinned. CUDA-extension wheel(flash_attn/xformers 등)은 Blackwell prebuilt wheel 사용.

검증 4단계: A.Alive(stage/hardware), B.로그 클린(폴백·dtype 다운그레이드 없는지), C.API 응답(`gradio_client`로 `view_api`→`predict`), D.출력+로그 동시 확인(HTTP 200≠정답).

참조: `references/zerogpu.md`, `debugging.md`, `known-errors.md`, `requirements.md`, `gradio.md`, `buckets.md`, `grants.md`, `inference-providers.md`. 동반 스킬 `hf-cli`(`hf skills add hf-cli`).

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-spaces/SKILL.md`
