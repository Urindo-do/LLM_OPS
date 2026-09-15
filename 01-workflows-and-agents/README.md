# 01. Workflows & Agents

> 1회차 보충·검토자료 정리. 자료 원문은 [resources.md](resources.md) 링크 참고.

## 1. 한 줄 요약

LLM 시스템은 **"누가 흐름을 정하나"** 로 나뉜다.

| 구분 | 흐름을 정하는 주체 | 언제 쓰나 |
|---|---|---|
| **Workflow** | 사람이 짠 코드 (미리 정한 경로 위에서 LLM 호출) | 단계가 예측 가능한 작업 |
| **Agent** | LLM 자신 (도구를 고르고, 결과를 보고 다음 행동 결정) | 단계를 미리 그릴 수 없는 열린 문제 |

둘 다 출발점은 **Augmented LLM** — 검색·도구·메모리를 붙인 LLM 한 개다.

## 2. 대표 패턴 6가지

| 패턴 | 구조 | 쓰는 상황 | 예시 |
|---|---|---|---|
| Prompt Chaining | 앞 호출의 출력 → 다음 호출의 입력 (직렬) | 작업을 단계로 쪼개면 품질이 오를 때 | 주제 → 농담 생성 → 검사 → 두 번 개선 |
| Parallelization | 하위 작업을 동시에 실행 후 합침 | 독립 작업, 여러 관점이 필요할 때 (RAG 멀티쿼리) | 주제 하나로 농담·이야기·시를 동시에 |
| Routing | 입력을 분류해서 전문 경로로 보냄 | 질문 유형별로 다른 검색 시스템을 쓸 때 | 요청을 농담/이야기/시 중 하나로 라우팅 |
| Orchestrator-Worker | 오케스트레이터가 작업을 나누고 워커에 위임 | 하위 작업 **개수를 미리 모를 때** | 보고서 목차를 LLM이 정하고 섹션별로 워커가 작성 |
| Evaluator-Optimizer | 생성 LLM ↔ 평가 LLM 피드백 루프 | 평가 기준이 분명할 때 (RAG 환각 채점) | 답변 생성 → 채점 → 기준 통과까지 반복 |
| Agent | 계획 → 도구 호출 → 피드백 반응을 루프로 | 워크플로로 그릴 수 없는 열린 문제 | — |

💡 **Parallelization vs Orchestrator-Worker 구분 포인트**: 병렬화는 하위 작업이 코드에 고정돼 있고,
오케스트레이터-워커는 LLM이 실행 시점에 하위 작업을 만들어낸다.

## 3. 에이전트를 이루는 3요소 (Visual Guide to LLM Agents)

1. **Memory**
   - 단기: 컨텍스트 윈도우. 길어지면 요약해서 압축
   - 장기: 벡터 DB + 임베딩 검색(RAG). 의미·작업·일화·절차 기억으로 나누기도 함
2. **Tools**
   - LLM이 JSON 같은 구조화된 요청을 만들고, 코드가 실제로 실행
   - Toolformer: 모델이 언제 어떤 API를 부를지 학습
   - **MCP**(Model Context Protocol): Host–Client–Server 구조로 도구 연결을 표준화
3. **Planning**
   - Chain-of-Thought: 단계별로 생각하게 하기
   - **ReAct**: Thought → Action → Observation 반복
   - Reflexion / Self-Refine: 실패를 돌아보고 스스로 고치기 (→ Evaluator-Optimizer와 연결)

그 위에 **Multi-Agent**: 역할이 다른 에이전트 여럿이 협업 (AutoGen, MetaGPT, CAMEL, Generative Agents 등).

## 4. 왜 LangGraph 같은 프레임워크를 쓰나

위 패턴은 프레임워크 없이도 만들 수 있다. LangGraph는 구현 부담을 줄이고 **밑단 인프라**를 준다.

- Persistence / Memory — 상태 저장·대화 기억
- Human-in-the-loop — 중간에 사람 승인 받기 (interrupt)
- Streaming — 어느 단계에서든 출력 흘려보내기
- Deployment — 테스트·디버깅·배포

LangGraph 기본 부품: **State**(공유 데이터) · **Node**(처리 단위 함수) · **Edge**(연결) · **Conditional Edge**(분기)

## 5. 복습 질문

- [ ] Workflow와 Agent를 "흐름 제어 주체"로 설명할 수 있나?
- [ ] 이메일 분류 → 담당자 배정 시스템은 6가지 패턴 중 무엇인가? 왜?
- [ ] Parallelization과 Orchestrator-Worker의 차이는?
- [ ] ReAct의 3단계를 말하고, Evaluator-Optimizer와 어떤 점이 비슷한가?
- [ ] MCP의 Host / Client / Server는 각각 무엇인가?
- [ ] LangGraph에서 조건 분기는 어떤 부품으로 만드나?

## 6. 실습

- `code/` — (실습코드 받으면 추가)
- 추천 다음 단계: LangChain Academy *LangGraph Essentials* 강의 따라 이메일 워크플로 만들기
