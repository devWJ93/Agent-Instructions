# hf-cli

> Hugging Face Hub의 공식 CLI 도구 `hf`로 모델·데이터셋·스페이스·버킷·repo·논문·잡(Jobs) 등을 다운로드/업로드/관리하는 스킬.

## 📌 용도
Hugging Face Hub와 상호작용하는 통합 명령줄 도구 `hf`의 사용을 담당한다. 인증, 로컬 캐시 관리, 버킷(클라우드 스토리지) 관리, HF 인프라에서의 잡 실행/스케줄링, repo·discussion·PR 관리, 모델/데이터셋/스페이스 탐색, 학술 논문 읽기/검색, 컬렉션 관리, 데이터셋 SQL 쿼리, 스페이스 설정, 웹훅 설정, Inference Endpoint 배포/관리까지 폭넓게 커버한다. 이 도구는 deprecated된 `huggingface-cli`를 대체한다. 학습 체크포인트, 데이터 파이프라인, 에이전트 트레이스 같은 클라우드 스토리지 작업에도 사용된다. 사용자가 명시적으로 CLI 명령을 요구하지 않아도 HF 생태계나 AI/ML 관련 작업이면 사용된다.

## 🎯 트리거 조건
- **언제 호출되는가**: 사용자가 'hf', 'huggingface', 'Hugging Face', 'huggingface-cli', 'hugging face cli'를 언급할 때. 모델/데이터셋/스페이스 다운로드·업로드, 인증(로그인/whoami), 캐시 관리, 버킷 생성/동기화, HF Jobs 실행·스케줄, repo/discussion/PR 관리, 논문 검색·읽기, 컬렉션 관리, 데이터셋 쿼리, 스페이스 구성, 웹훅, Inference Endpoint 배포 등 HF 생태계 전반 또는 AI/ML 일반 작업.
- **언제 쓰지 않는가**: 명령어 자체보다 더 전문화된 다른 스킬(예: 학습은 huggingface-llm-trainer, 데이터셋 뷰어 API는 huggingface-datasets, 모델 추천은 huggingface-best)이 직접 다루는 작업은 그 스킬로 위임. 단 CLI 명령 실행 자체는 본 스킬.

## 💻 사용 예시 / 명령어
```bash
# 설치
curl -LsSf https://hf.co/cli/install.sh | bash -s

# 인증
hf auth login                 # 토큰으로 로그인
hf auth whoami                # 현재 로그인 계정 확인

# 다운로드 / 업로드
hf download org/model --type model --include "*.safetensors"
hf upload org/model ./local --commit-message "add weights"
hf upload-large-folder org/model ./big --type model   # 재개 가능 업로드

# 잡 실행
hf jobs run IMAGE COMMAND --flavor a10g-small --timeout 1h
hf jobs uv run script.py --flavor t4-small            # UV 스크립트 실행
hf jobs scheduled run "0 * * * *" IMAGE COMMAND       # cron 스케줄

# 데이터셋 SQL
hf datasets sql "SELECT * FROM 'https://...parquet' LIMIT 10"

# 리포 마운트 (별도 도구 hf-mount)
hf-mount start repo openai-community/gpt2 /tmp/gpt2
```

## 🛠️ 제공 도구 / 주요 파라미터
주요 서브커맨드 그룹:
- **최상위**: `cp`, `download`, `env`, `sync`, `update`, `upload`, `upload-large-folder`, `version`
- **`hf auth`**: list, login, logout, switch, token, whoami
- **`hf buckets`**: cp, create, delete, info, list, move, remove, sync (클라우드 스토리지, `--region [us|eu]`)
- **`hf cache`**: list, prune, rm, verify
- **`hf collections`**: create/add-item/update/delete 등
- **`hf datasets`**: card, info, leaderboard, list, parquet, sql
- **`hf discussions`**: close, comment, create, diff, info, list, merge, rename, reopen
- **`hf endpoints`**: catalog deploy/list, deploy, describe, list, pause, resume, scale-to-zero, update, delete
- **`hf extensions`**: exec, install, list, remove, search
- **`hf jobs`**: cancel, hardware, inspect, labels, logs, ps, run, stats, uv run, `scheduled` 서브그룹
- **`hf models`**: card, info, list
- **`hf papers`**: info, list, read, search
- **`hf repos`**: branch, cp, create, delete, delete-files, duplicate, list, move, settings, tag
- **`hf skills`**: add, list, preview, update
- **`hf spaces`**: card, dev-mode, hardware, hot-reload, info, list, logs, pause, restart, search, secrets, settings, ssh, variables, volumes
- **`hf webhooks`**: create, delete, disable, enable, info, list, update

공통 옵션: `--format [auto|human|agent|json|quiet]`(또는 `--json`), `-q/--quiet`, `--revision`, `--token`(가급적 `HF_TOKEN` 환경변수 권장), `--type [model|dataset|space]`.

Jobs/Spaces 하드웨어 플레이버: `cpu-basic`, `t4-small`, `a10g-small`, `a100-large`, `h200`, `rtx-pro-6000` 등.

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/hf-cli/SKILL.md`
