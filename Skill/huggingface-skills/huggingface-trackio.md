# huggingface-trackio

> Trackio로 ML 학습 실험의 메트릭을 로깅·시각화하고, 알림 발화·메트릭 조회까지 다루는 스킬.

## 📌 용도
Trackio는 ML 학습 메트릭을 로깅하고 시각화하는 실험 추적 라이브러리다. Hugging Face Spaces로 동기화되어 실시간 모니터링 대시보드를 제공한다. 세 가지 인터페이스를 다룬다: (1) 학습 중 메트릭 로깅(Python API), (2) 학습 진단을 위한 알림 발화(Python API), (3) 학습 중/후 메트릭·알림 조회(CLI). 원격/클라우드 학습 시 `space_id`를 넘기면 인스턴스 종료 후에도 메트릭이 Space 대시보드에 영속된다. 알림은 LLM 에이전트의 자율 실험 반복을 위한 핵심 메커니즘으로, 진단 조건(loss spike, NaN gradient, 낮은 정확도, stall)을 코드에 삽입하고 터미널 출력/DB/대시보드/웹훅(Slack·Discord)으로 전달된다.

## 🎯 트리거 조건
- **언제 호출되는가**: 학습 중 메트릭 로깅(Python API), 학습 진단 알림 발화, 로깅된 메트릭/알림 조회·분석(CLI). 실시간 대시보드 시각화, 웹훅 알림, HF Space 동기화, 자동화용 JSON 출력.
- **언제 쓰지 않는가**: 학습 자체 오케스트레이션(huggingface-llm-trainer/vision-trainer가 Trackio를 내부적으로 호출). 단순 모델 추론/배포.

## 💻 사용 예시 / 명령어
```python
# 최소 로깅 설정
import trackio
trackio.init(project="my-project", space_id="username/trackio")
trackio.log({"loss": 0.1, "accuracy": 0.9})
trackio.log({"loss": 0.09, "accuracy": 0.91})
trackio.finish()

# 자율 실험 워크플로우 (알림 삽입)
trackio.init(project="my-project", config={"lr": 1e-4})
for step in range(num_steps):
    loss = train_step()
    trackio.log({"loss": loss, "step": step})
    if step > 100 and loss > 5.0:
        trackio.alert(title="Loss divergence", text=f"Loss {loss:.4f} high after {step} steps",
                      level=trackio.AlertLevel.ERROR)
    if step > 0 and abs(loss) < 1e-8:
        trackio.alert(title="Vanishing loss", text="Loss near zero — gradient collapse?",
                      level=trackio.AlertLevel.WARN)
trackio.finish()
```

```bash
# CLI 조회 (--json으로 자동화용 출력)
trackio list projects --json
trackio list runs --project my-project
trackio list metrics
trackio get metric --project my-project --run my-run --metric loss --json
trackio list alerts --project my-project --json --since "2025-01-01T00:00:00"
trackio show              # 대시보드 실행
trackio sync              # HF Space로 동기화
```

## 🛠️ 제공 도구 / 주요 파라미터
Python API(로깅): `trackio.init(project, space_id, config)`, `trackio.log({...})`, `trackio.finish()`. TRL 통합은 `report_to="trackio"`. 원격 학습은 `space_id`로 Space 동기화·영속.

Python API(알림): `trackio.alert(title, text, level)`. 레벨 3종 `AlertLevel.INFO`/`WARN`/`ERROR`. 터미널 출력+DB 저장+대시보드 표시+선택적 웹훅.

CLI(조회): `trackio list projects|runs|metrics|alerts`, `trackio get project|run|metric`, `trackio show`(대시보드), `trackio sync`(Space 동기화). `--json` 플래그로 프로그래매틱 출력, `--since <timestamp>`로 신규 알림 폴링.

자율 ML 실험 워크플로우: ① 진단 알림 삽입 → ② 백그라운드 학습 실행 → ③ `trackio list alerts --json --since`로 폴링 → ④ `trackio get metric`으로 값 검사 → ⑤ 알림·메트릭 기반으로 중단·하이퍼파라미터 조정·재실행.

참조: `references/logging_metrics.md`(설정·TRL 통합·옵션), `references/alerts.md`(알림 API·웹훅·자율 워크플로우), `references/retrieving_metrics.md`(CLI 명령·JSON 포맷).

## 📦 출처 / 설치 상태
- 플러그인: huggingface-skills@claude-plugins-official
- 활성화: ON
- 관련 MCP: huggingface-skills MCP (authenticate 도구)
- 캐시 경로: `~/.claude/plugins/cache/claude-plugins-official/huggingface-skills/1.0.8/skills/huggingface-trackio/SKILL.md`
