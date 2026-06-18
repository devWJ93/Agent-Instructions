# _conventions (커맨드 메타 문서)

> 이 플러그인의 모든 슬래시 커맨드가 따르는 일관된 구조를 정의하는 메타 문서. 사용자가 직접 호출하는 커맨드가 아님.

## 📌 용도
**플러그인 내부 메타 문서**다. 파일명이 `_`로 시작하므로 `plugin.json` 열거에서 제외되며, 사용자에게 호출 가능한 슬래시 커맨드로 노출되지 않는다. 커맨드 파일을 작성·수정할 때 모든 커맨드가 일관된 구조를 갖도록 강제하는 작성 규약이다.

이 문서 자체는 사용자가 호출할 수 없지만, vercel 플러그인을 켰을 때 다른 커맨드(/deploy, /env, /marketplace, /status, /bootstrap)들이 왜 동일한 섹션 구조를 갖는지 설명한다.

## 🎯 트리거 조건
- **언제 호출되는가**: 호출되지 않는다(slash command 아님). 플러그인 개발자가 새 커맨드를 작성/갱신할 때 참조하는 규약 문서.
- **언제 쓰지 않는가**: 일반 사용자 작업 전체.

## 💻 사용 예시 / 명령어
사용자 호출 명령 없음. 커맨드 파일 작성 시 아래 필수 섹션을 모두 포함해야 한다:
1. **Preflight** — 프로젝트 링크(`.vercel/project.json`)·CLI 존재·repo 상태·모노레포 스코프 점검
2. **Plan** — 실행할 명령/MCP 호출 명시, 파괴적/프로덕션 작업은 명시적 확인 요구
3. **Commands** — MCP-first·CLI-fallback, `--format=json` 선호, 시크릿 미출력, 파괴적 작업 확인
4. **Verification** — 실행 후 상태 재확인(`vercel ls`, `vercel env ls`), before/after 비교
5. **Summary** — 간결한 결과 블록(Action/Status/Details)
6. **Next Steps** — 논리적 후속 작업 제안

## 🛠️ 제공 도구 / 주요 파라미터
- **파일 네이밍**: 커맨드는 `commands/`에 `.md`로 위치. `_` 접두 파일(이 문서 등)은 메타 문서로 plugin.json 열거에서 제외.
- **frontmatter**: 모든 커맨드 파일은 최소 `description` 필드를 가진 YAML frontmatter 필수.
- **검증**: `scripts/validate.ts`가 비-언더스코어 커맨드 파일마다 6개 필수 섹션(Preflight·Plan·Commands·Verification·Summary·Next Steps)을 강제.

## 📦 출처 / 설치 상태
- 플러그인: vercel@claude-plugins-official
- 활성화: ⛔ **OFF** (enabledPlugins = false)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/vercel/0.43.0/commands/_conventions.md`
