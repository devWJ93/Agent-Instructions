# revise-claude-md

> 현재 세션에서 얻은 학습 내용을 CLAUDE.md에 반영해 업데이트하는 슬래시 명령

## 📌 용도
현재 세션에서 이 코드베이스로 작업하며 얻은 학습 내용을 검토하고, 향후 Claude 세션이 더 효과적으로 작업할 수 있도록 CLAUDE.md에 컨텍스트를 추가한다. 이번 세션에서 발견한 명령어, 코드 스타일 패턴, 테스트 접근법, 환경/설정 특이사항, 주의사항(gotcha)을 다음 세션을 위해 기록한다.

## 🎯 트리거 조건
- **언제 호출되는가**: `/claude-md-management:revise-claude-md` 실행 시. 세션에서 배운 것을 CLAUDE.md에 반영하고 싶을 때.
- **언제 쓰지 않는가**: 저장소 전체의 CLAUDE.md 품질 감사·평가는 claude-md-improver 스킬 사용. 이 명령은 "현재 세션 학습 반영"에 특화.

## 💻 사용 예시 / 명령어
```
/claude-md-management:revise-claude-md
```
- allowed-tools: Read, Edit, Glob

## 🛠️ 제공 도구 / 주요 파라미터
**5단계 워크플로**:

1. **Reflect(반성)**: 무엇이 있었으면 Claude가 더 효과적으로 작업했을지 파악 — 사용/발견한 bash 명령, 따른 코드 스타일 패턴, 통한 테스트 접근법, 환경/설정 특이사항, 마주친 경고/gotcha.

2. **CLAUDE.md 파일 찾기**:
   ```bash
   find . -name "CLAUDE.md" -o -name ".claude.local.md" 2>/dev/null | head -20
   ```
   추가 위치 결정: `CLAUDE.md`(팀 공유, git 체크인) vs `.claude.local.md`(개인/로컬, gitignore).

3. **추가 내용 초안**: **간결하게** — 개념당 한 줄. CLAUDE.md는 프롬프트의 일부이므로 간결함이 중요. 형식 `<명령 또는 패턴>` - `<짧은 설명>`. 장황한 설명·자명한 정보·재발 가능성 낮은 일회성 수정은 피할 것.

4. **변경 제안 표시**: 각 추가에 대해 대상 파일, 이유(한 줄), diff 형식으로 제시.
   ```
   ### Update: ./CLAUDE.md
   **Why:** [한 줄 이유]
   ```diff
   + [간결한 추가]
   ```
   ```

5. **승인 후 적용**: 사용자에게 적용 여부 질문, 승인한 파일만 편집.

## 📦 출처 / 설치 상태
- 플러그인: claude-md-management@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/claude-md-management/1.0.0/commands/revise-claude-md.md`
