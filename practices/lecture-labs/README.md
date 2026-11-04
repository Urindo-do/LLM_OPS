# 강의별 실습 (1강–9강)

강의 회차(강)에 맞춰 나온 실습 노트북 원본. [P01–P10](../)과는 다른 묶음이다.
모두 Colab 기준이며, 키는 Colab **Secrets**(`userdata.get`)에서 읽는다 — 노트북에 키를 적지 않는다.
LLM은 대부분 Upstage Solar(OpenAI 호환 클라이언트)를 쓴다.

> ⚠️ 업스테이지 콘텐츠 자산(노트북 첫 칸에 라이선스 경고). 이 저장소는 private 전제로 원본을 보관한다. public 전환 금지.

## 목록

| 강 | 폴더 | 노트북 | 다루는 것 |
|---|---|---|---|
| 1 | [L01-failure-cases](L01-failure-cases/) | 실패케이스_Demo | 데모에선 되는데 운영에선 깨지는 에이전트 — 뉴스 요약 에이전트 시나리오 |
| 2 | [L02-mcp-server](L02-mcp-server/) | MCP_Server | MCP 서버를 직접 만들어 보기 |
| 3 | [L03-multi-agent-a2a](L03-multi-agent-a2a/) | MultiAgent_A2A / 심화_실패모드 | A2A 협업 토폴로지 5가지 / 멀티 에이전트 실패 모드 4가지(무한 루프 등)와 방어 |
| 4 | [L04-trajectory-eval](L04-trajectory-eval/) | Trajectory_채점 | 최종 출력만이 아니라 ReAct 실행 경로(trajectory)를 채점 |
| 5 | [L05-langsmith-otel](L05-langsmith-otel/) | OTel_Span / LangSmith_심화 | OpenTelemetry span 단위 평가(Phoenix) / LangSmith 자동 트레이싱·평가 |
| 6 | [L06-eval-dataset](L06-eval-dataset/) | EvalDataset | Golden·수집·적대적 셋을 합친 평가 데이터셋 제작, Judge 정렬 |
| 7 | [L07-autonomy-hitl](L07-autonomy-hitl/) | Autonomy_HITL / 심화_레벨비교 | 자율성 레벨 설계와 HITL 에스컬레이션 / 레벨 비교·신뢰도 추출·Annotation Queue |
| 8 | [L08-reliability](L08-reliability/) | Reliability / 심화_Sandbox_Budget | Circuit Breaker 등 운영 신뢰성 계층 / 시간·토큰 예산, Sandbox 실행 |
| 9 | [L09-trace-failure-taxonomy](L09-trace-failure-taxonomy/) | Trace분석_FailureTaxonomy | 트레이스로 실패 유형 분류 → 회귀셋으로 되돌리기 |

## 데이터셋

- `L06-eval-dataset/eval_dataset.jsonl` — 6강 노트북이 만들어 저장하는 결과물 (10건)
- `L09-trace-failure-taxonomy/regression_set.jsonl` — 9강 노트북이 만들어 저장하는 회귀셋 (4건)

노트북을 다시 돌리면 같은 이름으로 덮어쓴다. 7강 심화는 실행 중에 `annotations.jsonl`을 만든다(포함 안 됨).

## 메모

- 필요한 키: `UPSTAGE_API_KEY`(전부), `LANGSMITH_API_KEY`(5강 LangSmith 심화).
- 5강 OTel은 `arize-phoenix`, `opentelemetry-exporter-otlp`를 추가 설치한다.
- 원본 파일 이름(한글)은 그대로 두었다. 실행 확인 여부는 아직 전부 미확인.
