# skill-creator

> 새 스킬을 만들고, 기존 스킬을 수정·개선하며, eval로 성능을 측정·최적화하는 메타 스킬

## 📌 용도
새 스킬을 처음부터 만들거나, 기존 스킬을 편집·최적화하고, eval(평가)로 스킬을 테스트하며, 분산 분석으로 성능을 벤치마크하고, 더 나은 트리거 정확도를 위해 description을 최적화한다.

**고수준 프로세스**: 스킬 목적 결정 → 초안 작성 → 테스트 프롬프트 작성 후 스킬-접근-Claude로 실행 → 정성·정량 평가 → 피드백 기반 재작성 → 만족할 때까지 반복 → 테스트 세트 확장 후 대규모 재시도.

사용자가 프로세스의 어느 단계에 있는지 파악해 진행을 돕는 것이 역할이다. 사용자의 코딩 용어 친숙도(배관공부터 컴퓨터 능숙자까지 광범위)에 맞춰 소통 톤을 조절한다.

## 🎯 트리거 조건
- **언제 호출되는가**: 스킬을 처음부터 생성, 기존 스킬 편집·최적화, eval 실행, 분산 분석 벤치마크, description 트리거 최적화 요청 시.
- **언제 쓰지 않는가**: Claude Code 플러그인 전용 스킬 작성 가이드는 plugin-dev:skill-development(이 스킬은 범용 독립 스킬 + eval/벤치마크 중심).

## 💻 사용 예시 / 명령어
- "X를 하는 스킬을 만들고 싶어"
- "이 스킬을 평가하고 개선해줘"
- "스킬 description을 트리거가 잘 되게 최적화해줘"
- "두 버전 중 어느 게 더 나은지 블라인드 비교해줘"

## 🛠️ 제공 도구 / 주요 파라미터
**스킬 생성**: 의도 포착(무엇을 가능케·언제 트리거·출력 형식·테스트 필요 여부) → 인터뷰/리서치 → SKILL.md 작성(name, description은 트리거 메커니즘이므로 "pushy"하게, compatibility) → 스킬 작성 가이드(점진적 공개, 명령형, examples 패턴).

**테스트 케이스 실행/평가**(연속 시퀀스, `/skill-test` 사용 금지):
- **Step 1**: 각 테스트당 with-skill + baseline 서브에이전트를 같은 턴에 spawn. 결과는 `<skill-name>-workspace/iteration-N/eval-ID/`에.
- **Step 2**: 실행 중 정량 assertion 초안 작성·설명.
- **Step 3**: 완료 시 `timing.json`(total_tokens, duration_ms) 즉시 저장.
- **Step 4**: 채점(`agents/grader.md`, grading.json 필드 `text`/`passed`/`evidence`) → 집계(`python -m scripts.aggregate_benchmark`) → 분석가 패스(`agents/analyzer.md`) → 뷰어 실행(`eval-viewer/generate_review.py`, 커스텀 HTML 금지).
- **Step 5**: `feedback.json` 읽기, 뷰어 종료.

**스킬 개선**: 피드백 일반화(오버핏 금지), 프롬프트 lean 유지, "왜"를 설명(ALL CAPS MUST 지양), 테스트 케이스 간 반복 작업을 scripts/로 번들.

**description 최적화**: 20개 트리거 eval 쿼리(should/should-not, 현실적·near-miss) → 사용자 검토(`assets/eval_review.html`) → `python -m scripts.run_loop`(60% train/40% test, 5회 반복) → `best_description` 적용.

**환경별**: Claude.ai(서브에이전트 없음·순차), Cowork(`--static` 뷰어), 패키징(`python -m scripts.package_skill`).

**참조 파일**: `agents/grader.md`, `comparator.md`, `analyzer.md`; `references/schemas.md`; `eval-viewer/generate_review.py`; `assets/eval_review.html`

## 📦 출처 / 설치 상태
- 플러그인: skill-creator@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/skill-creator/unknown/skills/skill-creator/SKILL.md`
