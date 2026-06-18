# huggingface-zerogpu

> Gradio Spaces의 ZeroGPU 하드웨어 코드 제약(`@spaces.GPU`, 프로세스 격리, duration/quota, CUDA 빌드)을 다루는 스킬.

## 📌 용도
Hugging Face Spaces의 ZeroGPU 하드웨어에서 ML 데모를 만들 때의 규칙과 패턴을 다룬다. `@spaces.GPU` 데코레이터, duration·quota 튜닝, 프로세스 격리(pickle), CUDA 가용성 모델, 동시성 안전성, CUDA 빌드 제약을 커버한다. ZeroGPU는 Gradio SDK Space 전용이며(Docker/Static은 스케줄 불가), PyTorch 우선이다. `import spaces`가 `torch.cuda.*`를 몽키패치해 module scope에서 `device="cuda"`가 동작하지만 실제 GPU는 `@spaces.GPU` 함수 안에서만 붙는다. `torch.compile`은 미지원이며 AoTI를 사용한다. backing GPU는 예고 없이 바뀌므로 정확한 VRAM/티어는 ZeroGPU 공식 문서를 확인한다.

## 🎯 트리거 조건
- **언제 호출되는가**: `@spaces.GPU`를 쓰는 코드 작성/리뷰, ZeroGPU Space의 `python_version`/`requirements.txt` 구성, ZeroGPU 코드 제약(pickle 프로세스 격리, gr.State 시맨틱, no torch.compile, CUDA wheel-only 빌드, large/xlarge 사이징, dynamic duration). 사용자가 ZeroGPU·`@spaces.GPU`·`spaces` 패키지를 언급하거나 `PicklingError`/`illegal duration`/`flash-attn` wheel 빌드 실패를 겪을 때. `import spaces`/`@spaces.GPU` 코드만 보여도 트리거.
- **언제 쓰지 않는가**: 일반 Gradio 코딩(huggingface-gradio), Space 전반 빌드/배포/디버깅(huggingface-spaces), LoRA Space 빌드(huggingface-lora-space-builder). Docker/Static Space나 dedicated GPU.

## 💻 사용 예시 / 명령어
```python
# 기본 패턴
import spaces           # torch보다 먼저
import torch
from transformers import pipeline
pipe = pipeline("text-generation", model="...", device="cuda")  # module scope

@spaces.GPU(duration=120)              # 현실적 worst-case로
def generate(prompt: str) -> str:
    return pipe(prompt, max_new_tokens=100)[0]["generated_text"]

# 디바이스 선택 관용구 (하드코딩 금지)
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
model = AutoModel.from_pretrained("...").to(device)

# 가변 워크로드용 dynamic duration
def estimate_duration(prompt, steps):
    return int(steps * 3.5)
@spaces.GPU(duration=estimate_duration)
def generate(prompt, steps):
    return pipe(prompt, num_inference_steps=steps).images[0]

# 프로세스 경계: CUDA 텐서 직접 반환 금지 → CPU로
# return tensor.cpu()  또는  tensor.cpu().numpy()
```

```bash
# ZeroGPU 예제 캐싱 동작 로컬 재현
GRADIO_CACHE_EXAMPLES=true GRADIO_CACHE_MODE=lazy python app.py

# uv로 requirements 내보내기 (spaces 제외)
uv export --no-hashes --no-dev --no-emit-package spaces -o requirements.txt
```

## 🛠️ 제공 도구 / 주요 파라미터
하드웨어 size: `large`(기본, 반쪽 GPU, 1x quota), `xlarge`(전체, 2x quota — 요청도 N×2로 계산).

핵심 규칙: ① 모델은 module scope에서 `.to("cuda")` 즉시 로드, ② GPU 함수에 `@spaces.GPU`(off-ZeroGPU에서는 no-op), ③ `duration`은 worst-case(기본 60s, 작을수록 큐 우선·quota 통과), ④ `torch.compile` 금지→AoTI(torch 2.8+), ⑤ `xlarge` 절약 사용.

duration/quota 3검사: tier-max(`illegal duration`), quota pre-check(`quota exceeded`, requested vs remaining), 노드 큐 우선순위. 작은 duration이 모두 유리.

프로세스 격리: `@spaces.GPU` 함수는 별도 프로세스, 인자/반환은 pickle. picklable만 가능(파일핸들/lock/lambda/closure는 `PicklingError`). CUDA 텐서 직접 반환 금지(`torch.cuda._lazy_init` 차단)→`.cpu()`. `gr.State`는 yield마다 pickle 복사(참조 시맨틱 아님), CUDA 텐서는 yield 전 CPU로.

동시성: 핸들러는 기본 병렬. ① mutable global 금지, ② 고정 출력 경로 금지(`tempfile`), ③ read-only global은 안전.

호출 단위: 핫루프 안에서 `@spaces.GPU` 호출 금지 — 루프 소유 외부 함수를 데코레이트.

CUDA 빌드: 빌드 단계 `nvcc` 없음(base `python:3.13`) — bare `flash-attn` 등 sdist 불가, prebuilt wheel만. 런타임은 `/cuda-image`에 `nvcc` 있음(AoTI용).

의존성: README frontmatter `python_version: "3.12"` 핀 사실상 필수. `spaces`는 requirements.txt에 넣지 않음(플랫폼 핀). 직접 URL wheel 설치 시 `torch==X.Y.Z`를 wheel 태그에 맞춰 핀.

로컬 개발: `import spaces`를 try/except no-op으로 감싸지 말 것(off-ZeroGPU에서 이미 no-op, `SPACES_ZERO_GPU` env로 게이트). 그냥 의존성에 추가하고 무조건 import.

참조: `references/concurrency.md`, `how-zerogpu-works.md`, `how-quota-works.md`, `cuda-and-deps.md`.

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-zerogpu/SKILL.md`
