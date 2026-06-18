# transformers-js

> Transformers.js로 최신 ML 모델을 JavaScript/TypeScript에서 직접 실행하는 스킬 (브라우저·Node.js·Bun·Deno, WebGPU/WASM).

## 📌 용도
Transformers.js를 사용해 최신 머신러닝 모델을 Python 서버 없이 JavaScript에서 직접 실행한다. 브라우저와 서버사이드 런타임(Node.js, Bun, Deno)에서 WebGPU/WASM으로 Hugging Face Hub의 사전학습 모델을 구동한다. NLP(텍스트 분류·번역·요약·생성), 컴퓨터 비전(이미지 분류·객체 탐지·분할·깊이 추정), 오디오(음성 인식·오디오 분류·TTS), 멀티모달(이미지 캡셔닝·문서 QA·zero-shot) 작업을 지원한다. 핵심은 `pipeline` API(전처리+추론+후처리 묶음)이며, 모든 파이프라인은 사용 후 `pipe.dispose()`로 메모리를 해제해야 한다.

## 🎯 트리거 조건
- **언제 호출되는가**: JavaScript에서 텍스트 분석/생성/번역 ML 모델 실행, 이미지 분류/객체탐지/분할, 음성 인식/오디오 처리, 멀티모달 AI 앱(텍스트→이미지, 이미지→텍스트), 백엔드 없이 브라우저 클라이언트사이드 모델 실행.
- **언제 쓰지 않는가**: Python 기반 추론/학습(transformers, huggingface-llm-trainer 등), 로컬 GGUF/llama.cpp(huggingface-local-models). 서버에 Python ML 백엔드를 두는 경우.

## 💻 사용 예시 / 명령어
```bash
npm install @huggingface/transformers
```

```javascript
// CDN (브라우저)
import { pipeline } from 'https://cdn.jsdelivr.net/npm/@huggingface/transformers';

// Pipeline API
import { pipeline } from '@huggingface/transformers';
const pipe = await pipeline('sentiment-analysis');
const result = await pipe('I love transformers!');
// [{ label: 'POSITIVE', score: 0.9998 }]
await pipe.dispose();   // 필수: 메모리 해제

// 커스텀 모델 + 디바이스 + 양자화
const gen = await pipeline('text-generation', 'onnx-community/gemma-3-270m-it-ONNX',
  { device: 'webgpu', dtype: 'q4' });   // dtype: fp32|fp16|q8|q4
const out = await gen('Once upon a time', { max_new_tokens: 100, temperature: 0.7 });

// 임베딩 (mean pooling + normalize)
const ext = await pipeline('feature-extraction', 'onnx-community/all-MiniLM-L6-v2-ONNX');
const emb = await ext('Text to embed', { pooling: 'mean', normalize: true });

// 진행률 콜백
const clf = await pipeline('sentiment-analysis', null, { progress_callback: onProgress });
```

```javascript
// env 전역 설정
import { env, LogLevel } from '@huggingface/transformers';
env.allowRemoteModels = true; env.useFSCache = true; env.cacheDir = './.cache';
env.fetch = (url, opt) => fetch(url, { ...opt, headers: { ...opt?.headers, Authorization: `Bearer ${HF_TOKEN}` }});
```

## 🛠️ 제공 도구 / 주요 파라미터
핵심 개념: ① pipeline API(가장 쉬움), ② 모델 선택(2번째 인자), ③ device(`webgpu` 또는 기본 WASM/CPU), ④ 양자화 dtype(`fp32`/`fp16`/`q8`/`q4`).

지원 태스크(태스크 ID): text-classification(sentiment-analysis), token-classification(ner), question-answering, fill-mask, summarization, translation, text-generation, zero-shot-classification, image-classification, image-segmentation, object-detection, depth-estimation, zero-shot-image/object-detection, automatic-speech-recognition, audio-classification, text-to-speech, image-to-text, document-question-answering, feature-extraction, sentence-similarity.

모델 찾기: `https://huggingface.co/models?library=transformers.js&sort=trending`, `pipeline_tag`로 필터. ONNX 파일(onnx 폴더) 확인 필수.

고급: `ModelRegistry`(파일 목록·캐시 상태·dtype 검사·캐시 정리), `@huggingface/tokenizers`(토크나이저 전용 경량 패키지), `AutoTokenizer`/`AutoModel`(저수준 제어), 배치 처리.

메모리 관리(중요): `pipe.dispose()` 필수 — 앱 종료/언마운트, 다른 모델 로드 전, 배치 후. 모델은 100MB~수GB 점유.

런타임: WebGPU(Chrome/Edge 113+, fp16 폴백), WASM(가장 호환). 모델 자동 캐싱(브라우저 Cache API/Node FS).

참조: `references/PIPELINE_OPTIONS.md`, `CONFIGURATION.md`, `MODEL_REGISTRY.md`, `CACHE.md`, `TEXT_GENERATION.md`(스트리밍·chat), `MODEL_ARCHITECTURES.md`, `EXAMPLES.md`.

베스트 프랙티스: 항상 dispose, pipeline 우선, 버전 핀(`revision`), try-catch, 파이프라인 재사용, 서버 SIGTERM/SIGINT 시 dispose.

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/transformers-js/SKILL.md`
