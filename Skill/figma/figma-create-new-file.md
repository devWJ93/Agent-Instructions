# figma-create-new-file

> 새 빈 Figma 파일(design/figjam/slides)을 생성하는 스킬. `create_new_file` 호출의 필수 선행 스킬.

## 📌 용도
`create_new_file` MCP 도구로 사용자 드래프트 폴더에 새 빈 Figma 파일을 만든다. 작업할 새 파일이 필요할 때(특히 `use_figma` 이전에) 사용한다. 이 스킬은 plan 해석 결정 트리, editorType 계약, 생성 후 `use_figma`로의 핸드오프를 인코딩한다.

> **필수(MANDATORY)**: 모든 `create_new_file` 호출 전에 이 스킬을 먼저 로드해야 한다. 직접 호출 금지.

**3단계 워크플로우**
1. **planKey 해석** — 사용자가 제공했으면 그대로 사용. 없으면 `whoami` 호출 → `plans` 배열에서 단일 플랜은 자동 사용, 복수면 사용자에게 어느 팀/조직인지 질문.
2. **create_new_file 호출** — `planKey`, `fileName`, `editorType` 전달.
3. **결과 사용** — `file_key`, `file_url` 반환. `file_key`를 후속 `use_figma` 등에 사용.

## 🎯 트리거 조건
- **언제 호출되는가**: 새 빈 Figma 파일(design/figjam/slides)을 원할 때, 또는 `use_figma` 전에 새 파일이 필요할 때. `/figma-create-new-file [editorType] [fileName]`.
- **언제 쓰지 않는가**: 기존 파일에서 작업할 때, 다이어그램 생성(→ `generate_diagram`은 자체 파일 생성하므로 이 스킬 선행 불필요).

## 💻 사용 예시 / 명령어
```
/figma-create-new-file                          # design 파일 "Untitled"
/figma-create-new-file figjam My Whiteboard     # FigJam 파일
/figma-create-new-file design My New Design     # design 파일
/figma-create-new-file slides Q3 Review         # Slides 프레젠테이션
```
- **인자**: `editorType`(`design` 기본 / `figjam` / `slides`), `fileName`(기본 "Untitled").

호출 페이로드 예:
```json
{ "planKey": "team:123456", "fileName": "My New Design", "editorType": "design" }
```

## 🛠️ 제공 도구 / 주요 파라미터
- **사용 MCP 도구**: `create_new_file`, `whoami`(planKey 해석용).

| 파라미터 | 필수 | 설명 |
| --- | --- | --- |
| `planKey` | 예 | Step 1에서 얻은 플랜 키 |
| `fileName` | 예 | 새 파일 이름 |
| `editorType` | 예 | `"design"` / `"figjam"` / `"slides"` |

**주의**:
- 파일은 선택한 플랜의 **드래프트 폴더**에 생성.
- `use_figma`가 다음 단계면 `figma-use` 스킬을 먼저 로드.
- **Slides 특이사항**: 새로 만든 slides 파일은 행 0개·슬라이드 0개인 빈 그리드(`getSlideGrid()` → `[]`). 첫 `createSlide()`가 row 0과 슬라이드를 암묵 생성. 첫 슬라이드 존재를 가정하는 후속 스크립트는 빈 경우를 가드하거나 `createSlide()`를 먼저 호출(자세히는 `figma-use-slides → slide-grid`).

## 📦 출처 / 설치 상태
- 플러그인: figma@claude-plugins-official
- 활성화: ON (`disable-model-invocation: false`)
- 관련 MCP: Figma MCP 연동(`create_new_file`, `whoami`)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/figma/2.2.12/skills/figma-create-new-file/SKILL.md`
