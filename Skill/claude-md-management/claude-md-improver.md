# claude-md-improver

> 저장소의 CLAUDE.md 파일들을 감사·평가하고 타겟 개선을 수행하는 스킬

## 📌 용도
코드베이스 전반의 CLAUDE.md 파일을 감사·평가·개선해 Claude Code가 최적의 프로젝트 컨텍스트를 갖도록 한다. 모든 CLAUDE.md를 스캔하고 템플릿 대비 품질을 평가하여 품질 리포트를 출력한 뒤, 사용자 승인을 받아 타겟 개선을 적용한다. **이 스킬은 CLAUDE.md 파일에 쓰기 작업을 수행한다.**

## 🎯 트리거 조건
- **언제 호출되는가**: CLAUDE.md 파일을 점검·감사·업데이트·개선·수정 요청 시. "CLAUDE.md maintenance" 또는 "project memory optimization" 언급 시.
- **언제 쓰지 않는가**: 현재 세션 학습만 반영할 때는 revise-claude-md 명령 사용. 이 스킬은 저장소 전체 감사·품질 평가에 특화.

## 💻 사용 예시 / 명령어
- "우리 프로젝트 CLAUDE.md 파일들을 감사해줘"
- "CLAUDE.md를 최신 코드베이스 상태에 맞게 업데이트해줘"
- "project memory를 최적화하고 싶어"

## 🛠️ 제공 도구 / 주요 파라미터
**5단계 워크플로**:

1. **Discovery**: `find . -name "CLAUDE.md" -o -name ".claude.md" -o -name ".claude.local.md"`로 모든 파일 탐색. 파일 유형: 프로젝트 루트(`./CLAUDE.md`), 로컬 오버라이드(`.claude.local.md`), 글로벌 기본(`~/.claude/CLAUDE.md`), 패키지별(monorepo), 서브디렉터리.

2. **Quality Assessment**: 각 파일을 품질 기준으로 평가 — 명령/워크플로 문서화(High), 아키텍처 명확성(High), 비자명 패턴(Medium), 간결성(Medium), 최신성(High), 실행 가능성(High). 점수: A(90~100) ~ F(0~29). 상세 루브릭은 `references/quality-criteria.md`.

3. **Quality Report 출력**(업데이트 전 항상 먼저): 요약(파일 수, 평균 점수, 업데이트 필요 수) + 파일별 평가(기준별 점수표, 이슈, 권장 추가).

4. **Targeted Updates**: 리포트 출력 후 사용자 확인. 진짜 유용한 정보만 타겟 추가(발견한 명령/워크플로, gotcha, 패키지 관계, 테스트 접근법, 설정 특이사항). 자명한 것·일반 베스트 프랙티스·일회성 수정·장황한 설명은 피할 것. diff로 변경 표시.

5. **Apply Updates**: 승인 후 Edit 도구로 적용, 기존 구조 보존.

**흔히 지적할 이슈**: stale 명령, 누락 의존성, 구식 아키텍처, 환경 설정 누락, 깨진 테스트 명령, 미문서화 gotcha.

**사용자 팁**: `#` 키 단축키(세션 중 학습 자동 반영), 간결 유지, 실행 가능한 명령, `.claude.local.md`(개인 설정), `~/.claude/CLAUDE.md`(전역 기본).

**참조 파일**: `references/quality-criteria.md`, `references/templates.md`
**tools**: Read, Glob, Grep, Bash, Edit

## 📦 출처 / 설치 상태
- 플러그인: claude-md-management@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/claude-md-management/1.0.0/skills/claude-md-improver/SKILL.md`
