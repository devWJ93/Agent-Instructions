# frontend-design

> 일반적인 "AI 슬롭" 미감을 피하고, 독창적이고 프로덕션급의 프론트엔드 인터페이스를 만드는 스킬

## 📌 용도
높은 디자인 품질의 독특하고 프로덕션급인 프론트엔드 인터페이스를 만든다. 일반적인 "AI가 만든 듯한" 미감을 피하고, 미적 디테일과 창의적 선택에 탁월한 주의를 기울인 실제 동작 코드를 구현한다. 컴포넌트, 페이지, 애플리케이션, 인터페이스를 빌드할 때 사용한다.

## 🎯 트리거 조건
- **언제 호출되는가**: 웹 컴포넌트·페이지·애플리케이션 빌드 요청 시. 프론트엔드 UI 구현 전반.
- **언제 쓰지 않는가**: 백엔드 로직, 데이터 처리, 비시각적 코드. 대화형 HTML 탐색 도구는 playground 스킬.

## 💻 사용 예시 / 명령어
- "랜딩 페이지를 만들어줘"
- "이 대시보드 UI를 디자인해줘"
- "제품 소개 컴포넌트를 멋지게 만들어줘"

## 🛠️ 제공 도구 / 주요 파라미터
**디자인 사고**(코딩 전): 맥락 이해 후 BOLD한 미적 방향에 전념.
- **Purpose**: 어떤 문제를 해결하나? 누가 쓰나?
- **Tone**: 극단을 선택 — brutally minimal, maximalist chaos, retro-futuristic, organic, luxury/refined, playful, editorial/magazine, brutalist, art deco, soft/pastel, industrial 등.
- **Constraints**: 프레임워크, 성능, 접근성.
- **Differentiation**: 무엇이 이걸 잊을 수 없게 만드나?

**핵심**: 명확한 개념적 방향을 정밀하게 실행. Bold 맥시멀리즘과 정제된 미니멀리즘 모두 가능 — 핵심은 강도가 아니라 의도성.

**프론트엔드 미감 가이드라인**:
- **Typography**: 아름답고 독특한 폰트. Arial·Inter 같은 일반 폰트 지양, 개성 있는 디스플레이 폰트 + 정제된 본문 폰트 페어링.
- **Color & Theme**: 응집된 미감, CSS 변수로 일관성. 지배적 색상 + 날카로운 액센트.
- **Motion**: 애니메이션·마이크로 인터랙션. HTML은 CSS 우선, React는 Motion 라이브러리. 잘 연출된 페이지 로드(staggered reveals, animation-delay) 한 번이 흩어진 마이크로 인터랙션보다 낫다. 스크롤 트리거·호버.
- **Spatial Composition**: 예상 밖 레이아웃, 비대칭, 오버랩, 대각선 흐름, 그리드 깨기, 넉넉한 여백 OR 통제된 밀도.
- **Backgrounds & Visual Details**: 분위기·깊이 창출 — gradient mesh, noise texture, geometric pattern, 레이어드 투명도, 극적 그림자, 장식 테두리, 커스텀 커서, grain overlay.

**금지(generic AI 미감)**: 과용 폰트(Inter, Roboto, Arial, 시스템 폰트), 진부한 색상(흰 배경 보라 그래디언트), 예측 가능한 레이아웃, 컨텍스트 없는 쿠키커터 디자인. 생성마다 다르게(라이트/다크, 폰트, 미감 변주), 흔한 선택(예: Space Grotesk)으로 수렴 금지.

**중요**: 구현 복잡도를 미적 비전에 맞춰라 — 맥시멀리즘은 정교한 코드·애니메이션, 미니멀리즘은 절제·정밀·간격/타이포 디테일.

## 📦 출처 / 설치 상태
- 플러그인: frontend-design@claude-plugins-official
- 활성화: ON
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/frontend-design/unknown/skills/frontend-design/SKILL.md`
