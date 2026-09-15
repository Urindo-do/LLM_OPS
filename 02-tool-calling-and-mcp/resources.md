# 02회차 보충·검토자료

| 자료 | 종류 | 무엇을 보면 되나 |
|---|---|---|
| [OpenAI – Function calling (Responses API)](https://developers.openai.com/api/docs/guides/function-calling?api-mode=responses) | 공식 문서 | 5단계 호출 흐름, `function_call`/`function_call_output`, 함수 스키마·`strict`, 정의 best practice |
| [Why MCP Breaks Old Enterprise AI Architectures](https://www.decodingai.com/p/why-mcp-breaks-old-enterprise-ai) | 강의형 글 (Anca Ioana Muscalagiu, Decoding AI, 2025-08) | 기존 방식 vs MCP, Host/Client/Server, JSON-RPC·transport·OAuth, PR 리뷰 봇 설계. 코드: [decodingml/enterprise-mcp-series](https://github.com/decodingml/enterprise-mcp-series) |
| [MCP vs API – Model Context Protocol Explained](https://norahsakal.com/blog/mcp-vs-api-model-context-protocol-explained/) | 입문 글 (Norah Sakal, 2025-03) | USB-C 비유, MCP vs API 비교표, 언제 무엇을 쓸지 |
| [MCP Security Notification: Tool Poisoning Attacks](https://invariantlabs.ai/blog/mcp-security-notification-tool-poisoning-attacks) | 보안 리포트 (Invariant Labs, 2025-04) | Tool Poisoning·Rug Pull·Shadowing 공격과 대응 |

## 읽는 순서 추천

1. OpenAI 문서 → 도구 호출의 바닥 원리
2. Norah Sakal → MCP가 뭔지 가볍게
3. Decoding AI → MCP로 시스템을 설계하는 이유
4. Invariant Labs → 그 설계의 위험
5. 실습 P03 → P04
