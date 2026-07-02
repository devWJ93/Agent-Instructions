# hexagonal-code-review

> 헥사고날 아키텍처 / Ports and Adapters 기준으로 코드·diff·모듈·저장소를 리뷰하고, 요청 시 레이어 책임에 맞게 작성 또는 리팩토링하는 개인 글로벌 스킬.

## 📌 용도
코드가 헥사고날 아키텍처를 따르는지 검토한다. 핵심 검토 대상은 다음과 같다.

1. **Domain 순수성** — UI, framework, engine, DB, file system, network, external SDK 의존이 없는지 확인한다.
2. **Application / Use Case 책임** — domain과 port를 조율하고 concrete adapter에 직접 의존하지 않는지 확인한다.
3. **Port 계약** — inbound/outbound port가 구현체에 오염되지 않았는지 확인한다.
4. **Adapter 경계** — inbound adapter는 외부 요청 변환과 위임만, outbound adapter는 외부 기술 실행만 담당하는지 확인한다.
5. **Entry / Composition Root** — 객체 생성과 의존성 주입만 담당하는지 확인한다.
6. **언어별 레이아웃 프로파일** — TypeScript/WebGL과 Unity/C# 프로젝트의 파일 확장자와 폴더 관례를 각각 따르는지 확인한다.

## 🎯 트리거 조건
- **언제 호출되는가**
  - "헥사고날 리뷰해줘", "Ports and Adapters 맞는지 봐줘".
  - "domain/app/adapter 분리 상태 확인해줘".
  - "이 모듈 구조 위반 찾아줘".
  - "헥사고날 형태로 작성해줘", "헥사고날 구조로 리팩토링해줘".
  - Unity/C#, TypeScript/WebGL 프로젝트에서 레이어 경계 검토가 필요할 때.
- **언제 쓰지 않는가**
  - 일반 correctness 버그 리뷰만 원하면 `code-review`.
  - 보안 취약점 전용 리뷰는 `security-review`.
  - 동작 실행 검증은 `verify` 또는 `run`.

## 💻 사용 예시 / 명령어
- "이 diff가 헥사고날 구조를 지키는지 리뷰해줘."
- "이 Unity 모듈에서 Domain이 UnityEngine에 의존하는지 봐줘."
- "이 WebGL 모듈을 우리 프로젝트 모듈 모양으로 작성해줘."
- "Application이 outbound port 대신 adapter를 직접 쓰는 부분 찾아줘."
- "헥사고날 형태로 리팩토링 계획을 세우고 적용해줘."

## 🛠️ 제공 도구 / 주요 파라미터
- **Review Mode**: 파일을 수정하지 않고 PASS/PARTIAL/FAIL verdict, findings, layer classification, remediation plan을 보고한다.
- **Implementation Mode**: 사용자가 명시적으로 요청한 경우에도 먼저 수정 대상, 건드리지 않을 파일, 예상 리스크, 검증 방법을 포함한 구현 계획을 보고하고, 사용자 승인 후 최소 변경으로 작성 또는 리팩토링한다.
- **공통 참조**: `hexagonal-rules.md`, `review-checklist.md`, `remediation-patterns.md`, `output-formats.md`.
- **프로파일 참조**: TypeScript/WebGL은 `project-layout-typescript.md`, Unity/C#은 `project-layout-unity-csharp.md`.
- **출력 포맷**: Review Mode 또는 Implementation Mode 결과를 보고할 때 `output-formats.md` 기준을 적용한다.
- **핵심 제약**: `.ts` 확장자를 Unity/C#에 강제하지 않고, 파일명보다 역할과 의존 방향을 먼저 판단한다.

## 📦 출처 / 설치 상태
- 분류: **개인 글로벌 스킬** (`~/.agents/skills/hexagonal-code-review`).
- 작성 근거: 초기 임시 자료의 헥사고날 아키텍처 규칙, WebGL 모듈 레이아웃 설명, 기존 준수 리뷰 스킬 초안을 범용화했다.
- UI 메타데이터는 `openai.yaml` 기준을 맞춰 `short_description` 25~64자 범위를 준수한다.
- 공개 문서에는 실제 개인 경로와 내부 프로젝트 비밀정보를 포함하지 않는다.
