# create-plugin

> 컴포넌트 설계·구현·검증을 아우르는, 가이드형 엔드투엔드 Claude Code 플러그인 생성 워크플로 (슬래시 명령)

## 📌 용도
초기 개념부터 테스트된 구현까지 완전하고 고품질의 Claude Code 플러그인을 단계별로 만든다. 요구사항 이해 → 컴포넌트 설계 → 세부 명확화 → 베스트 프랙티스 기반 구현 → 검증 → 테스트의 체계적 접근을 따른다.

**핵심 원칙**:
- **명확화 질문**: 목적·트리거·범위·컴포넌트의 모든 모호함을 구체적 질문으로 확인. 추측 금지, 답변 후 진행.
- **관련 스킬 로드**: 필요 시 Skill 도구로 plugin-dev 스킬(plugin-structure, hook-development 등) 로드.
- **전문 에이전트 활용**: agent-creator, plugin-validator, skill-reviewer.
- **점진적 공개(Progressive disclosure)**: references/examples를 갖춘 lean 스킬 생성.
- **TodoWrite로 진행 추적**.

이 명령은 `commands/` 디렉터리 형식(legacy)으로 작성된 슬래시 명령이다. 참고로 새 플러그인에서는 사용자 호출 명령도 `skills/<name>/SKILL.md`로 만드는 것이 권장된다.

## 🎯 트리거 조건
- **언제 호출되는가**: `/plugin-dev:create-plugin` 슬래시 명령 실행 시. 플러그인을 처음부터 만들고 싶을 때.
- **언제 쓰지 않는가**: 개별 컴포넌트(스킬/에이전트/훅/MCP)만 만들 때는 해당 전용 스킬 직접 사용. 기존 플러그인 일부 수정.

## 💻 사용 예시 / 명령어
```
/plugin-dev:create-plugin
/plugin-dev:create-plugin 데이터베이스 마이그레이션 관리 플러그인
```
- argument-hint: `Optional plugin description` (선택적 플러그인 설명)

## 🛠️ 제공 도구 / 주요 파라미터
**8단계 워크플로** (각 결정 지점에서 사용자 승인 대기):
1. **Discovery**: 플러그인 목적·대상 사용자 파악(7단계 todo 생성).
2. **Component Planning**(plugin-structure 스킬 로드 필수): 필요 컴포넌트(Skills/Agents/Hooks/MCP/Settings) 판단, 표로 제시.
3. **Detailed Design & 명확화 질문**(절대 건너뛰지 말 것): 각 컴포넌트의 미정 사항 질문, 답변 대기.
4. **Plugin Structure Creation**: kebab-case 이름, 위치 선택, 디렉터리 구조 + `plugin.json` 매니페스트 + README 생성, git 초기화.
5. **Component Implementation**: 타입별 스킬 로드 후 구현 (skill-development, agent-development, hook-development, mcp-integration, plugin-settings; legacy `commands/`만 command-development).
6. **Validation & Quality Check**: plugin-validator 에이전트, skill-reviewer, validate-agent.sh, validate-hook-schema.sh로 검증.
7. **Testing & Verification**: `cc --plugin-dir /path`로 로컬 테스트, 체크리스트 검증.
8. **Documentation & Next Steps**: README 완성, marketplace.json 엔트리, 요약.

**allowed-tools**: Read, Write, Grep, Glob, Bash, TodoWrite, AskUserQuestion, Skill, Task
**전문 에이전트**: agent-creator, plugin-validator, skill-reviewer

**품질 기준**: plugin-dev 자체 패턴 준수, 올바른 네이밍, 강한 트리거 조건, 동작 예시, ${CLAUDE_PLUGIN_ROOT} 사용, 보안 우선(HTTPS, 자격증명 하드코딩 금지).

## 📦 출처 / 설치 상태
- 플러그인: plugin-dev@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/plugin-dev/unknown/commands/create-plugin.md`
