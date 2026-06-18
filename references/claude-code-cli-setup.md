# 참조: Claude Code CLI 설정 설치 가이드

> 이 문서는 **필요할 때만 참조**하는 설치 절차다. 범용 규칙은 `../AGENTS.md`와 `../rules/`를 따른다. 사용자가 "세팅해줘"·"설정해줘"·"설치해줘"를 요청하면 이 절차를 **정확히** 따른다.

## 저장소 구조

```
dotfiles/                     # 설치할 설정 파일 원본
├── CLAUDE.md                 # → ~/.claude/CLAUDE.md (글로벌 규칙)
├── settings.json             # → ~/.claude/settings.json (머지)
├── keybindings.json          # → ~/.claude/keybindings.json
├── skills/                   # → ~/.claude/skills/ (폴더째 복사)
├── agents/                   # → ~/.claude/agents/ (파일 복사)
└── bash/                     # → ~/ (홈 디렉토리에 복사)
mcp-servers/                  # MCP 서버 소스 코드
├── computer-control/server.py
└── web-fetcher/index.mjs, package.json
```

## 세팅 절차

### Step 1: 환경 확인
```bash
python --version    # 3.8+ 필요
node --version      # 18+ 필요 (MCP web-fetcher 선택 시)
claude --version    # MCP 등록 시 필요
jq --version        # 상태줄에 필요 (없으면 설치)
```

### Step 2: 설치 항목 선택
전체 항목을 카테고리별로 보여주고 선택받는다. "기본/전부/추천대로" 요청이면 **추천(기본 ON)** 항목만 설치한다.

**코어 설정 (전부 추천 ON)**
| # | 항목 | 설명 |
|---|------|------|
| 1 | 글로벌 규칙 (CLAUDE.md) | 작업 프로세스 3단계, 품질·컨텍스트·Compact 규칙 |
| 2 | 안전 Hooks | rm -rf·force push·시크릿 커밋·API 키 삽입·하드코딩 절대경로 차단 |
| 3 | 한국어 상태줄 | 모델명·컨텍스트%·레이트 리밋 표시. **jq 필요**(미설치 시 자동 설치) |
| 4 | 키바인딩 | Enter=전송, Shift+Enter=줄바꿈 |
| 5 | 플러그인 활성화 | superpowers·feature-dev·firecrawl 등 30개(ON 24 + OFF 6) |
| 6 | 한국어 응답 | 응답 언어 한국어 |
| 7 | 기본 권한 규칙 | git status/diff/log 등 안전 명령 자동 허용 (19개) |

**Skills** — 8 claude-news(ON) / 9 compile-check / 10 ui-toolkit-guide / 11 profiler-analysis (9~11 Unity 전용, OFF)

**추가 기능** — 12 code-reviewer 에이전트 / 13 Bash 테마 / 14 알림 Hooks / 15 .cs 수정 알림 Hook (전부 OFF)

**도구 & 바로가기 (Windows)** — 16 Claude Code 바로가기(ON) / 17 bypass 바로가기 / 18 snap-path (OFF)

**MCP 서버 (claude CLI 필요)** — 19 computer-control(마우스/키/스크린샷) / 20 web-fetcher(HTTPS 페칭) (OFF)

### Step 3: 백업
설치 전 **반드시** 기존 `~/.claude/`를 백업한다.
```bash
mkdir -p ~/.claude/backup_$(date +%Y%m%d_%H%M%S)
cp ~/.claude/CLAUDE.md ~/.claude/settings.json ~/.claude/keybindings.json ~/.claude/backup_*/ 2>/dev/null
cp -r ~/.claude/skills ~/.claude/agents ~/.claude/backup_*/ 2>/dev/null
```
`~/.claude/` 자체가 없으면 백업 불필요. 디렉토리만 생성한다: `mkdir -p ~/.claude/skills ~/.claude/agents`

### Step 4: 설치 실행

**4-1. CLAUDE.md** — `dotfiles/CLAUDE.md → ~/.claude/CLAUDE.md` 단순 복사. 설치 후 사용자에게 자기 환경에 안 맞는 섹션 삭제를 안내한다(RAG 없으면 "RAG MCP" 삭제, graph-RAG-study 없으면 "글로벌 설정" 삭제, Unity 안 쓰면 "Unity" 삭제, Notion 안 쓰면 "Notion" 삭제, team-monitor 없으면 "팀 작업" 삭제).

**4-2. settings.json 머지 (항목 2~7, 14~15)** — **절대 전체 덮어쓰기 금지.** Python으로 선택된 섹션만 머지한다.

```python
import json
from pathlib import Path

source = json.load(open(Path("dotfiles/settings.json"), encoding="utf-8"))
target_path = Path.home() / ".claude" / "settings.json"
target = json.load(open(target_path, encoding="utf-8")) if target_path.exists() else {}
```

| 항목 | 머지 방법 |
|------|-----------|
| 안전 Hooks (2) | `source.hooks.PreToolUse` → target에 추가. 같은 `matcher` 있으면 스킵 |
| 상태줄 (3) | `target["statusLine"] = source["statusLine"]` (교체) |
| 플러그인 (5) | `target["enabledPlugins"].update(...)` (기존 + 신규) |
| 한국어 (6) | `target["language"] = "korean"` |
| 권한 (7) | `allow` 배열 합집합 (순서 유지, 중복 제거) |
| 알림 Hooks (14) | `source.hooks.Notification` → target에 추가. matcher 중복 스킵 |
| .cs Hook (15) | `source.hooks.PostToolUse` → target에 추가. matcher 중복 스킵 |
| SessionStart | 항상 머지 (무해한 verify 훅) |

```python
def merge_hooks(target, source, hook_type):
    """matcher 기준 중복 체크 후 추가"""
    target.setdefault("hooks", {})
    src = source.get("hooks", {}).get(hook_type, [])
    tgt = target["hooks"].get(hook_type, [])
    existing = {h["matcher"] for h in tgt}
    for h in src:
        if h["matcher"] not in existing:
            tgt.append(h)
    target["hooks"][hook_type] = tgt

# permissions 머지
existing = target.get("permissions", {}).get("allow", [])
new = source.get("permissions", {}).get("allow", [])
target.setdefault("permissions", {})["allow"] = list(dict.fromkeys(existing + new))

# 저장
target_path.parent.mkdir(parents=True, exist_ok=True)
json.dump(target, open(target_path, "w", encoding="utf-8"), indent=2, ensure_ascii=False)
```

**4-3. 키바인딩** — `dotfiles/keybindings.json → ~/.claude/keybindings.json` 단순 복사.

**4-4. Skills** — 선택된 skill 폴더를 통째로 복사. 이미 있으면 덮어쓰기(최신 교체).

**4-5. Agent** — `dotfiles/agents/code-reviewer.md → ~/.claude/agents/code-reviewer.md`.

**4-6. Bash 테마** — `.bashrc`·`.minttyrc`·`.elephant_walk.py`를 `~/`에 복사. 기존 `.bashrc`가 있으면 덮어쓰기 전 **반드시 확인**. Roboto Mono 폰트는 `%LOCALAPPDATA%\Microsoft\Windows\Fonts\`에 설치.

**4-7. 바탕화면 바로가기 (Windows)** — `Claude Code.bat`:
```bat
@echo off
start "" "C:\Program Files\Git\git-bash.exe" -c "claude"
```
bypass 버전은 `claude --dangerously-skip-permissions`. Git Bash 경로는 `%PROGRAMFILES%\Git\git-bash.exe` 확인.

**4-8. snap-path (Windows)** — 부모 디렉토리에 클론 후 시작 프로그램 등록.
```bash
cd .. && git clone https://github.com/glowElephant/snap-path.git
```

**4-9. MCP 서버 (claude CLI 필요)**
```bash
# computer-control
pip install pyautogui pygetwindow keyboard Pillow pyperclip
claude mcp add --scope user --transport stdio computer-control -- python <저장소경로>/mcp-servers/computer-control/server.py

# web-fetcher
cd <저장소경로>/mcp-servers/web-fetcher && npm install
claude mcp add --scope user --transport stdio web-fetcher -- node <저장소경로>/mcp-servers/web-fetcher/index.mjs
```
`claude mcp list`에 이름이 있으면 스킵.

**4-10. jq 설치 (상태줄 의존성)**
```bash
# Windows
mkdir -p ~/bin && curl -L -o ~/bin/jq.exe https://github.com/jqlang/jq/releases/download/jq-1.7.1/jq-windows-amd64.exe
# Linux
sudo apt install jq   # 또는 brew install jq
```

### Step 5: 결과 보고
설치 후 (1) 설치된 항목 (2) 스킵된 항목 (3) 실패 항목+원인 (4) 백업 위치 (5) 다음 할 일(CLAUDE.md 정리·재시작·MCP 재시도)을 보고한다.

## 주의사항

- settings.json은 절대 전체 복사하지 말 것 — 반드시 머지한다.
- `~/.claude/` 경로는 하드코딩하지 말 것 — `Path.home() / ".claude"` 또는 `~/`를 쓴다.
- MCP 서버 등록 시 `--scope user` — project scope면 다른 프로젝트에서 안 된다.
- MCP 등록은 `claude mcp add` 명령어만 — settings.json·.mcp.json 직접 쓰면 인식 안 된다.
- Windows Git Bash(mintty)에서는 `$` 변수 확장이 안 되니 경로를 직접 지정한다.
- 이미 존재하는 hook은 matcher로 판별 — 같은 matcher면 추가하지 않는다.
