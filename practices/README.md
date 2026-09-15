# 실습 트랙 (Practice 01–10)

강의 보충자료 폴더 **02_보충자료(Agentic Workflow)** 의 실습 노트북 원본.
모두 Colab 기준이며, API 키는 Colab **Secrets**(`userdata.get`)에서 읽는다 — 노트북에 키를 적지 않는다.

> ⚠️ 강사 제공 자료. 이 저장소는 private 전제로 원본을 보관한다. public 전환 금지.

## 흐름

| 단계 | # | 폴더 | 다루는 것 | 주요 라이브러리 |
|---|---|---|---|---|
| 만들기 | P01 | [ai-service-agent](P01-ai-service-agent/) | LangGraph 기초 — State·Reducer·Node·병렬·조건부 엣지·Checkpointer·HITL(interrupt) | langgraph |
| 만들기 | P02 | [agentic-workflow](P02-agentic-workflow/) | Workflow vs Agent, Prompt Chaining·Routing·Parallelization·Orchestrator-Worker·ReAct·Evaluator-Optimizer·Planning, 패턴 선택 가이드 | langgraph, langchain_upstage |
| 만들기 | P03 | [tool-calling](P03-tool-calling/) | 수동 Tool Calling → `@tool` → Pydantic 스키마, 자기 수정 루프 | langchain, pydantic |
| 만들기 | P04 | [mcp](P04-mcp/) | MCP 보안 원칙, LangChain MCP Adapters (STDIO / Streamable HTTP / Local) | mcp, langchain_mcp_adapters |
| 똑똑하게 | P05 | [rag-agentic-rag](P05-rag-agentic-rag/) | Basic RAG(Loader→Splitter→Embedding→VectorStore→Retriever) → Agentic RAG | langchain_qdrant, langchain_upstage, tavily |
| 똑똑하게 | P06 | [memory-management](P06-memory-management/) | Checkpointer, 도구로 기억 갱신·조회, 메시지 요약·트리밍 | langgraph |
| 똑똑하게 | P07 | [context-engineering](P07-context-engineering/) | DeepAgent의 TODO 계획·가상 파일시스템(오프로딩)·서브에이전트(격리) | deepagents |
| 운영하기 | P08 | [safety-guardrails](P08-safety-guardrails/) | Middleware 가드레일 — PII 마스킹·HITL·모델 기반 커스텀 | langchain, langchain_openai |
| 운영하기 | P09 | [evaluation](P09-evaluation/) | LangSmith 데이터셋·메트릭·Evaluator, Chat/RAG 평가 | langsmith |
| 운영하기 | P10 | [agentops](P10-agentops/) | pytest로 에이전트 행동 테스트(off-topic·검색 쿼리·멀티툴), LangSmith 대시보드 | pytest, langsmith |

## 이론과의 연결

- P01·P02 ↔ [01-workflows-and-agents](../01-workflows-and-agents/)
- P03·P04 ↔ [02-tool-calling-and-mcp](../02-tool-calling-and-mcp/)

## 메모

- P08의 `sk-4Fb2...` 문자열은 PII 가드레일 테스트용 **가짜 샘플 키**다 (노트북 주석에 명시).
- 필요한 키(추정, 노트북별 상이): Upstage, OpenAI, Tavily, LangSmith — 각 노트북 "환경 설정" 칸 참고.
- 각 폴더의 개인 정리 `README.md`는 실습을 돌려보며 하나씩 추가 예정.
