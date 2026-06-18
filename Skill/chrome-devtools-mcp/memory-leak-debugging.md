# memory-leak-debugging

> JavaScript/Node.js 애플리케이션의 메모리 누수를 진단·해결하는 스킬

## 📌 용도
JavaScript와 Node.js 애플리케이션에서 메모리 누수를 찾고 진단하고 수정한다. 높은 메모리 사용량, OOM(Out of Memory) 에러, 힙 스냅샷 분석, memlab 같은 메모리 누수 탐지 도구 실행을 다룬다.

**핵심 원칙**:
- **memlab 우선**: raw `.heapsnapshot` 파일을 직접 읽지 말 것 — 매우 크고 토큰을 과도하게 소비한다. 항상 `memlab`으로 스냅샷을 처리하고 누수 트레이스를 식별한다.
- **누수 격리**: 브라우저(클라이언트) 누수인지 Node.js(서버) 누수인지 판별.
- **흔한 원인**: detached DOM 노드, 처리되지 않은 클로저, 전역 변수, 제거되지 않은 이벤트 리스너, 무한 증가 캐시. (단, detached DOM 노드는 의도적 캐시일 수 있으니 null로 만들기 전 사용자에게 확인할 것.)

## 🎯 트리거 조건
- **언제 호출되는가**: 높은 메모리 사용량 보고, OOM 에러, 힙 스냅샷 분석, memlab 등 메모리 누수 탐지 도구 실행 요청 시.
- **언제 쓰지 않는가**: 일반 성능/LCP(→ debug-optimize-lcp), 일반 디버깅(→ chrome-devtools)은 별도 스킬.

## 💻 사용 예시 / 명령어
- "우리 앱 메모리가 계속 늘어나는데 누수 찾아줘"
- "이 heapsnapshot 분석해줘"
- "OOM 에러가 나는데 원인 진단해줘"
- "memlab으로 누수 트레이스 뽑아줘"

## 🛠️ 제공 도구 / 주요 파라미터
**워크플로**:

1. **스냅샷 캡처**: 프론트엔드 누수 조사 시 chrome-devtools-mcp 도구(`click`, `navigate_page`, `fill` 등)로 페이지를 조작한다.
   - 인터랙션 후 원래 상태로 되돌려 메모리 해제 여부 확인.
   - 동일 인터랙션을 10회 반복해 누수를 증폭.
   - `take_heapsnapshot`으로 baseline(기준), target(액션 후), final(되돌린 후) 상태의 `.heapsnapshot`을 디스크에 저장.

2. **memlab으로 누수 탐지(권장)**: 생성된 `.heapsnapshot`을 `memlab`으로 자동 분석. 사용법은 `references/memlab.md` 참조. raw `.heapsnapshot`을 `read_file`/`cat`으로 읽지 말 것.

3. **흔한 누수 식별**: 누수 트레이스를 찾으면 `references/common-leaks.md`로 코드 내 근본 원인과 수정법 파악.

4. **폴백: 스냅샷 수동 비교**: `memlab`이 없으면 references 디렉터리의 폴백 스크립트로 두 스냅샷을 비교:
   ```bash
   node skills/memory-leak-debugging/references/compare_snapshots.js <baseline.heapsnapshot> <target.heapsnapshot>
   ```
   크기순 상위 증가 객체와 가장 흔한 3가지 누수 유형(detached DOM, 클로저, Context)을 출력한다.

**주요 도구**: `take_heapsnapshot`, chrome-devtools-mcp 인터랙션 도구, `memlab`(외부 CLI)
**참조 파일**: `references/memlab.md`, `references/common-leaks.md`, `references/compare_snapshots.js`

## 📦 출처 / 설치 상태
- 플러그인: chrome-devtools-mcp@claude-plugins-official
- 활성화: ON (Chrome DevTools MCP 서버와 연동, `--memoryDebugging` 플래그로 메모리 도구 활성화)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/chrome-devtools-mcp/1.2.0/skills/memory-leak-debugging/SKILL.md`
