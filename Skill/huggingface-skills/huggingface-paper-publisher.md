# huggingface-paper-publisher

> Hugging Face Hub에 연구 논문을 발행·관리하는 스킬 (페이지 생성, 모델/데이터셋 연결, 저자권 주장, 연구 아티클 생성).

## 📌 용도
AI 엔지니어와 연구자가 Hugging Face Hub에서 연구 논문을 발행·관리·연결하도록 돕는다. 논문 작성부터 발행까지의 워크플로우를 간소화하며, arXiv 연동, 모델/데이터셋 연결, 저자권 관리를 포함한다. 주요 기능은 (1) 논문 페이지 관리(arXiv에서 인덱싱, 저자권 주장, 가시성 제어, 논문 발견), (2) 모델/데이터셋 아티팩트에 논문 연결(Hub가 자동으로 `arxiv:<ID>` 태그 생성), (3) 마크다운 기반 전문 연구 아티클 생성(standard/modern/arxiv/ml-report 템플릿), (4) 메타데이터 관리(YAML frontmatter, BibTeX 인용). `scripts/paper_manager.py`를 `uv run`으로 실행한다.

## 🎯 트리거 조건
- **언제 호출되는가**: HF Hub에 연구 논문 발행/관리, 논문 페이지 생성, 논문을 모델/데이터셋에 연결, 저자권 주장, 마크다운 기반 연구 아티클 생성.
- **언제 쓰지 않는가**: 기존 논문을 읽기/요약/분석하는 작업(huggingface-papers). 논문 발행이 아니라 단순히 arXiv/HF paper URL을 읽어달라는 경우.

## 💻 사용 예시 / 명령어
전제: `uv run`으로 스크립트 실행, `HF_TOKEN`(Write 권한) 설정. 경로는 SKILL.md 디렉토리 기준.

```bash
# arXiv에서 논문 인덱싱 / 존재 확인
uv run scripts/paper_manager.py index --arxiv-id "2301.12345"
uv run scripts/paper_manager.py check --arxiv-id "2301.12345"

# 모델/데이터셋/스페이스에 논문 연결 (자동 arxiv 태그 생성)
uv run scripts/paper_manager.py link --repo-id "username/model-name" --repo-type "model" --arxiv-id "2301.12345"
uv run scripts/paper_manager.py link --repo-id "username/model-name" --repo-type "model" --arxiv-ids "2301.12345,2302.67890"
uv run scripts/paper_manager.py link --repo-id "username/model-name" --repo-type "model" --arxiv-id "2301.12345" --citation "$(cat citation.txt)" --create-pr

# 저자권 주장 / 상태 확인
uv run scripts/paper_manager.py claim --arxiv-id "2301.12345" --email "your.email@institution.edu"
uv run scripts/paper_manager.py check-authorship --arxiv-id "2301.12345"

# 가시성 관리
uv run scripts/paper_manager.py list-my-papers
uv run scripts/paper_manager.py toggle-visibility --arxiv-id "2301.12345" --show true

# 연구 아티클 생성 / HTML 변환
uv run scripts/paper_manager.py create --template "modern" --title "..." --authors "Jane Doe, John Smith" --abstract "$(cat abstract.txt)" --output "paper.md"
uv run scripts/paper_manager.py convert --input "paper.md" --output "paper.html" --style "modern"

# 검색 / 정보 / 인용
uv run scripts/paper_manager.py search --query "transformer attention"
uv run scripts/paper_manager.py info --arxiv-id "2301.12345" --format "json"
uv run scripts/paper_manager.py citation --arxiv-id "2301.12345" --format "bibtex"
uv run scripts/paper_manager.py validate --repo-id "username/model-name" --repo-type "model"
```

## 🛠️ 제공 도구 / 주요 파라미터
스크립트: `scripts/paper_manager.py`. PEP 723 인라인 의존성(huggingface_hub>=0.26.0, pyyaml, requests, markdown, python-dotenv).

서브커맨드: `index`, `check`, `link`, `claim`, `check-authorship`, `list-my-papers`, `toggle-visibility`, `create`, `convert`, `search`, `info`, `citation`, `validate`.

연구 아티클 템플릿: `standard`(전통적), `modern`(Distill풍 웹 친화), `arxiv`, `ml-report`. modern은 동적 목차·반응형·코드 하이라이트·LaTeX 수식 지원.

연결 원리: README에 arXiv 링크 추가 → Hub가 ID 추출 → `arxiv:<ID>` 태그 자동 생성 → 클릭 시 Paper Page → 해당 논문 인용 모델/데이터셋 표시.

Python API: `from scripts.paper_manager import PaperManager; pm = PaperManager(hf_token=...); pm.index_paper(...); pm.link_paper(...)`.

에러: Paper Not Found(미인덱싱), Permission Denied(토큰 권한), Invalid YAML, Authorship Failed(이메일 불일치), Already Claimed, Rate Limiting. tfrere의 research-article-template Space와 보완 관계.

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-paper-publisher/SKILL.md`
