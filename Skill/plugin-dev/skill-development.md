# skill-development

> Claude Code 플러그인용 스킬의 구조·점진적 공개·작성 스타일 베스트 프랙티스를 안내하는 스킬

## 📌 용도
효과적인 플러그인 스킬을 만든다. 스킬은 특정 도메인/작업을 위한 "온보딩 가이드"로, Claude를 범용 에이전트에서 절차적 지식을 갖춘 특화 에이전트로 변환한다. 특화 워크플로, 도구 통합, 도메인 전문성, 번들 리소스(스크립트·참조·에셋)를 제공한다.

## 🎯 트리거 조건
- **언제 호출되는가**: "스킬 생성", "플러그인에 스킬 추가", "새 스킬 작성", "스킬 description 개선", "스킬 콘텐츠 구성" 요청 시. 스킬 구조·점진적 공개·베스트 프랙티스 가이드 필요 시.
- **언제 쓰지 않는가**: 명령(→ command-development), 에이전트(→ agent-development), 훅(→ hook-development). 독립 스킬 생성·평가는 별도의 skill-creator 스킬도 존재.

## 💻 사용 예시 / 명령어
- "API 문서화 스킬을 만들어줘"
- "이 스킬의 description이 트리거가 잘 되도록 개선해줘"
- "SKILL.md가 너무 길어졌는데 정리해줘"

## 🛠️ 제공 도구 / 주요 파라미터
**스킬 구조**:
```
skill-name/
├── SKILL.md (필수)
│   ├── YAML frontmatter (name, description 필수)
│   └── 마크다운 지시
└── 번들 리소스 (선택)
    ├── scripts/     - 실행 코드(결정론적/반복 작업)
    ├── references/  - 필요 시 컨텍스트로 로드되는 문서
    └── assets/      - 출력에 사용되는 파일(템플릿, 아이콘, 폰트)
```

**점진적 공개(3단계 로딩)**:
1. 메타데이터(name + description) — 항상 컨텍스트(~100단어)
2. SKILL.md 본문 — 스킬 트리거 시(<5k 단어)
3. 번들 리소스 — 필요 시(무제한, 스크립트는 로드 없이 실행)

**생성 프로세스**: ① 구체적 예시로 사용 패턴 이해 → ② 재사용 콘텐츠 계획(scripts/references/assets) → ③ 구조 생성(`mkdir -p skills/skill-name/{references,examples,scripts}`) → ④ SKILL.md 편집 → ⑤ 검증·테스트 → ⑥ 반복.

**작성 스타일(필수)**:
- 본문: **명령형/부정사형**(verb-first). "To do X, do Y" — "You should" 금지.
- description: **3인칭** + 구체적 트리거 구문. "This skill should be used when the user asks to '...'". 모호/2인칭 금지.

**lean 유지**: 본문 1,500~2,000단어 목표(<5k 최대). 상세 패턴 → `references/patterns.md`, 고급 → `references/advanced.md`, 마이그레이션 → `references/migration.md`, API → `references/api-reference.md`. 정보 중복 금지(SKILL.md 또는 references 중 한 곳).

**검증 체크리스트**: 구조(frontmatter name/description, 참조 파일 존재), description 품질(3인칭·구체적 트리거), 콘텐츠 품질(명령형·lean·references 분리), 점진적 공개, 테스트(트리거 동작).

**흔한 실수**: ① 약한 트리거 description, ② SKILL.md에 과다 콘텐츠, ③ 2인칭 작성, ④ 리소스 참조 누락.

**참조**: plugin-dev 자체 스킬(hook-development, agent-development, plugin-settings 등)을 모범 예시로 연구. skill-reviewer 에이전트로 검토.

## 📦 출처 / 설치 상태
- 플러그인: plugin-dev@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/plugin-dev/unknown/skills/skill-development/SKILL.md`
