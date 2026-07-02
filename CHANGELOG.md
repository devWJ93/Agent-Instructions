<!-- 이 문서는 Agent-Instructions 저장소의 공개 변경 이력을 기록한다. -->
# Changelog

## 2026-07-02

### Added

- `hexagonal-code-review` 개인 글로벌 스킬 카탈로그를 추가해 헥사고날 아키텍처 / Ports and Adapters 기준의 코드 리뷰와 리팩토링 적용 기준을 문서화했다.

### Changed

- 스킬 인덱스와 README의 노출 스킬 수를 98개, 개인 글로벌 스킬 수를 5개로 갱신했다.
- `hexagonal-code-review`의 구현 모드는 계획 보고와 사용자 승인 후 수정하도록 보강하고, 결과 보고 시 출력 포맷 참조를 적용하도록 문서화했다.

## 2026-06-18

### Added

- `GETTING_STARTED.md`를 추가해 다른 AI에게 저장소를 적용시키는 프롬프트와 AI별 사용법을 분리했다.
- `SECURITY.md`를 추가해 토큰, 인증 헤더, 개인 설정 파일, 내부 프로젝트 지식의 공개 금지 기준을 명시했다.
- `LICENSE`를 추가해 직접 작성한 문서의 재사용 범위와 타사 자료의 별도 라이선스 적용 범위를 구분했다.
- Codex CLI의 `AGENTS.md` 로딩 방식과 `rules/*.md` 문서, `~/.codex/rules/*.rules` 승인 정책의 차이를 `GETTING_STARTED.md`에 추가했다.
- 핵심 검증·보안·파일 읽기·중단 조건을 `AGENTS.md` 본문에 직접 추가해 링크 문서를 읽지 않아도 최소 규칙이 적용되도록 했다.
- Codex 전역 `~/.codex/AGENTS.md`에 복사하기 위한 `codex-global-AGENTS.md`를 추가했다.

### Changed

- `README.md` 상단을 공개 저장소 첫 화면에 맞게 정리하고 Quick Start 프롬프트를 추가했다.
- `README.md`에서 `GETTING_STARTED.md`, `SECURITY.md`, `CHANGELOG.md`로 이어지는 온보딩 흐름을 명확히 했다.
- Notion 토큰 저장 언급을 일반 보안 주의 문구로 바꿨다.

### Removed

- 개인 프로젝트 지식인 `references/data2avatar.md`를 공개 대상에서 제외했다.
- 프로젝트 전용 흔적이던 `MCP/caveman-shrink.md`를 제거했다.
