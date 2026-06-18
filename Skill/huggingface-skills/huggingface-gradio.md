# huggingface-gradio

> Python으로 Gradio 웹 UI와 ML 데모를 만드는 스킬 (Interface/Blocks/ChatInterface, 컴포넌트, 이벤트 리스너).

## 📌 용도
Gradio는 인터랙티브 웹 UI와 ML 데모를 만드는 Python 라이브러리다. 이 스킬은 핵심 API, 패턴, 예제를 커버한다. 고수준 `Interface`(함수를 입출력 컴포넌트로 감쌈), 저수준 `Blocks`(명시적 레이아웃·이벤트 와이어링), `ChatInterface`(챗봇 UI 래퍼) 세 가지 코어 패턴을 제공한다. Textbox, Number, Slider, Image, Audio, Video, Chatbot 등 주요 컴포넌트의 시그니처, 이벤트 리스너 패턴, `gr.HTML` 기반 커스텀 컴포넌트, 그리고 `gradio` CLI(`info`/`predict`)를 통한 프로그래매틱 Spaces 호출까지 다룬다.

## 🎯 트리거 조건
- **언제 호출되는가**: Gradio 앱/데모 생성·편집, 컴포넌트·이벤트 리스너·레이아웃·챗봇 작성. Python으로 ML 데모 웹 UI를 만들 때.
- **언제 쓰지 않는가**: Gradio가 아닌 다른 프레임워크(예: Streamlit, 일반 React 프런트엔드). LoRA용 Space 빌드는 huggingface-lora-space-builder, ZeroGPU 코드 제약은 huggingface-zerogpu, Space 배포/디버깅은 huggingface-spaces가 더 적합.

## 💻 사용 예시 / 명령어
```python
import gradio as gr

# Interface (고수준)
gr.Interface(fn=lambda name: f"Hello {name}!", inputs="text", outputs="text").launch()

# Blocks (저수준)
with gr.Blocks() as demo:
    name = gr.Textbox(label="Name")
    output = gr.Textbox(label="Greeting")
    btn = gr.Button("Greet")
    btn.click(fn=lambda n: f"Hello {n}!", inputs=name, outputs=output)
demo.launch()

# ChatInterface (챗봇)
gr.ChatInterface(fn=lambda message, history: f"You said: {message}").launch()
```

```bash
# Prediction CLI - 엔드포인트/파라미터 발견
gradio info gradio/calculator

# 예측 전송
gradio predict gradio/calculator /predict '{"num1": 5, "operation": "multiply", "num2": 3}'
gradio predict black-forest-labs/FLUX.2-dev /infer '{"prompt": "A majestic dragon"}'
# 파일 업로드는 meta 키 필요
gradio predict gradio/image_mod /predict '{"image": {"path": "/path/to/image.png", "meta": {"_type": "gradio.FileData"}}}'
```

## 🛠️ 제공 도구 / 주요 파라미터
주요 컴포넌트: `Textbox`, `Number`, `Slider`, `Checkbox`, `Dropdown`, `Radio`, `Image`, `Audio`, `Video`, `File`, `Chatbot`, `Button`, `Markdown`, `HTML` 등. 각 컴포넌트의 전체 시그니처(value/type/label/interactive/visible 등)가 SKILL.md에 수록됨.

이벤트 리스너 공통 시그니처: `component.event_name(fn, inputs, outputs, api_name, show_progress, queue, concurrency_limit, ...)`. 컴포넌트별 지원 이벤트(예: Button→click, Textbox→change/input/submit, Chatbot→change/select/like/retry/edit, Audio→stream/play/pause 등) 정리됨.

커스텀 컴포넌트: `gr.HTML`로 `html_template`(`${}` JS, `{{}}` Handlebars), `css_template`(스코프 스타일), `js_on_load`(인터랙션). `props.value`로 값 갱신, `trigger('event_name')`로 이벤트 발화. 서브클래싱 + `api_info()`로 API/MCP 지원.

Prediction CLI: `gradio info <space>`(엔드포인트/파라미터 JSON), `gradio predict <space> <endpoint> <json>`(예측). 둘 다 private Space는 `--token`.

참고 가이드 링크(Quickstart, Blocks, Streaming, Sharing 등)와 `examples.md`(완전 동작 앱).

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-gradio/SKILL.md`
