# 04. 도구 증강 LLM 트렌드 — LLM은 오케스트레이터

> 기준: 2026년 2월 / LLM이 단독이 아니라 "도구를 쓰는 오케스트레이터"가 되어가는 흐름

---

## 핵심 메시지

> LLM은 코드를 생성 → 터미널/런타임에서 실행 → 결과를 받아 다음 행동을 결정한다.
> 단독 챗봇이 아니라, **도구를 쓰는 오케스트레이터**로 진화 중이다.

---

## 1. 코드 생성 → 실행 → 결과 수신 → 다음 행동 결정

### CodeAct 패러다임
- JSON 함수 호출 대신 LLM이 **실행 가능한 Python 코드**를 생성
- 샌드박스 인터프리터에서 실행 → 출력/에러를 "관찰"로 수신 → 다음 단계 계획
- 코드 액션: 루프, 조건문, 변수, 제어 흐름 활용 → 정적 도구 호출보다 훨씬 풍부

### 런타임 동적 도구 생성
- LLM(o3-mini 등)이 사용자 프롬프트에 따라 **런타임에 도구 함수를 동적 생성** → 코드 인터프리터로 실행
- 사전에 모든 시나리오를 코드베이스에 정의할 필요 없음

### 에이전틱 코딩 에이전트
- Claude Code, Cursor, Copilot 등: 코드 생성 → 터미널 실행 → 결과 읽기 → 다음 행동 결정 루프
- Claude Code(2025.02 출시): "2025년 가장 영향력 있는 이벤트"로 평가

### 보안 고려
- LLM 생성 코드는 **샌드박스 Docker 컨테이너**에서 실행 (네트워크·호스트 파일시스템 접근 차단)
- 터미널 권한이 있는 에이전틱 코딩 에디터: 프롬프트 인젝션 공격 성공률 **최대 84%**

---

## 2. LLM을 오케스트레이터로 — 도구 사용 핵심 루프

```
컨텍스트 조립 (시스템 프롬프트 + 도구 정의 + 사용자 메시지)
       ↓
도구 사용 결정 (LLM이 도구 호출 여부 판단)
       ↓
도구 실행 (개발자 코드가 함수 실행)
       ↓
관찰 (결과가 모델에 반환)
       ↓
응답 생성 (최종 답변 또는 다음 도구 호출)
```

- **에이전틱 LLM**: 계획, 평가, 자기 교정, 도구 호출, 웹 탐색, 코드 작성, 다른 AI와 협업, 다단계 결정을 인간 개입 없이 수행
- "사용자가 **'무엇'**을 정의하면, 에이전트가 **'어떻게'**를 결정한다"
- MLAT 프레임워크(2026.02): 사전 학습된 ML 모델을 에이전트 워크플로우 내 호출 가능한 도구로 노출

---

## 3. 주요 모델의 함수 호출(Function Calling) / 도구 사용

- **모든 주요 제공사**가 구조화된 함수/도구 호출 지원: OpenAI(GPT-4o, o3), Anthropic(Claude 4), Google(Gemini 2.5+)
- 오픈소스: DeepSeek-V3.2, Xiaomi MiMo-V2-Flash — 에이전틱/도구 호출 워크플로우 전용 훈련
- DeepSeek-V3.2: **사고(thinking)를 도구 사용에 직접 통합**한 최초 모델
- 평가 벤치마크: Berkeley Function Calling Leaderboard(BFCL), tau-bench, API-Bank

---

## 4. 에이전트 프레임워크 (2025-2026)

| 프레임워크 | 특징 | 규모 |
|-----------|------|------|
| **LangChain/LangGraph** | 그래프 기반 상태 머신, 노드·엣지·조건 라우팅. v1.0(2025말). 프로덕션급 | PyPI 4,700만+ 다운로드 |
| **CrewAI** | 역할 기반 멀티 에이전트 (연구자, 작가, 분석가). Crews + Flows 모드 | GitHub 44,000+ 스타 |
| **AutoGen(AG2)** | MS Research. 이벤트 기반 비동기 아키텍처. 네이티브 Human-in-the-loop | AutoGen Studio GUI |
| **신규 진입** | OpenAI Agents SDK, Pydantic AI, Google ADK, Amazon Bedrock Agents | — |

- 에이전트 프레임워크 GitHub 레포(1,000+ 스타): 2024년 14개 → 2025년 **89개** (535% 증가)

---

## 5. MCP (Model Context Protocol) — 에이전틱 AI의 HTTP

### 개요
- Anthropic이 2024.11 도입한 **오픈 표준**: LLM이 외부 도구·데이터 소스에 연결하는 방법을 표준화
- 파편화된 통합을 **단일 프로토콜**로 대체

### 채택 타임라인
| 시기 | 이벤트 |
|------|--------|
| 2025.03 | OpenAI가 Agents SDK, Responses API, ChatGPT Desktop에 MCP 채택 |
| 2025.04 | Google DeepMind가 Gemini에 MCP 지원 확인 |
| 2025.05 | Microsoft/GitHub가 MCP 운영위 합류; Windows 11 MCP 프리뷰 |
| 2025.11 | 주요 스펙 업데이트 (비동기, 무상태, 서버 ID, 공식 레지스트리) |
| 2025.11 | MCP Apps Extension (대화 내 인터랙티브 UI) — OpenAI·Anthropic 공동 작성 |
| 2025.12 | Linux Foundation 산하 Agentic AI Foundation에 기부 (Anthropic, Block, OpenAI 공동 설립; Google, MS, AWS 등 지원) |

### 규모
- **10,000+** 활성 공개 MCP 서버
- 월 **9,700만+** SDK 다운로드 (Python + TypeScript)
- ChatGPT, Cursor, Gemini, MS Copilot, VS Code에 채택

### 관련 프로토콜
- **Google A2A** (Agent-to-Agent Protocol): 다른 벤더/플랫폼 에이전트 간 통신 표준
- MCP(에이전트↔도구) + A2A(에이전트↔에이전트) = 에이전틱 AI의 **"HTTP에 해당하는" 표준**

---

## 6. 컴퓨터 사용 / 브라우저 사용 에이전트

| 에이전트 | 설명 | 성과/규모 |
|---------|------|----------|
| **Anthropic Computer Use** | Claude가 화면 보기, 커서 이동, 클릭, 타이핑으로 직접 컴퓨터 조작 | Chrome 확장(2025.08) |
| **OpenAI Operator → ChatGPT Atlas** | 브라우저에서 자율적으로 다단계 작업 수행 (2025.10) | WebVoyager 87% |
| **Perplexity Comet** | AI 내장 Chromium 브라우저; 웹사이트 탐색, 양식 작성, 이메일/캘린더 관리 | 무료(2025.10~) |
| **Google Chrome Auto Browse** | Gemini 3 AI 사이드 패널로 자동 브라우징 (2026.01) | Premium 구독자 |
| **Dia Browser** | The Browser Company; AI 퍼스트 설계. Atlassian이 $6.1억에 인수(2025.08) | — |

- Browser Use 프레임워크: WebVoyager 벤치마크(586개 웹 작업) **89.1%** 성공률
- 에이전틱 브라우저 시장: 2024년 $45억 → 2034년 **$768억** 전망

---

## 교육 시사점

> 컴퓨팅 모듈 + LLM 모듈 + (검색/파일/실행/문서화) **도구 모듈**을 "레고 블록"처럼 이어 붙여 아키텍처를 설명하면, 비전공자도 AI 시스템 구조를 직관적으로 이해할 수 있다.

---

## 출처

- [Transforming LLM Agents with Executable Code Actions — Medium](https://medium.com/@stalin.t/transforming-llm-agents-with-executable-code-actions-boosting-efficiency-and-capability-6a1824496377)
- [OpenAI Cookbook — Secure Code Interpreter Tool for LLM Agents](https://developers.openai.com/cookbook/examples/object_oriented_agentic_approach/secure_code_interpreter_tool_for_llm_agents/)
- [2025: The Year in LLMs — Simon Willison](https://simonwillison.net/2025/Dec/31/the-year-in-llms/)
- [AI Agentic Programming Survey — arXiv](https://arxiv.org/html/2508.11126v1)
- [How Tools Are Called in AI Agents — Medium](https://medium.com/@sayalisureshkumbhar/how-tools-are-called-in-ai-agents-complete-2025-guide-with-examples-42dcdfe6ba38)
- [Agentic LLMs in 2025 — Data Science Dojo](https://datasciencedojo.com/blog/agentic-llm-in-2025/)
- [MLAT Paper — arXiv (2026.02)](https://arxiv.org/html/2602.14295)
- [Function Calling — Prompt Engineering Guide](https://www.promptingguide.ai/agents/function-calling)
- [Top 7 Agentic AI Frameworks 2026 — AlphaMatch](https://www.alphamatch.ai/blog/top-agentic-ai-frameworks-2026)
- [Open Source AI Agent Frameworks Compared (2026.02) — OpenAgents](https://openagents.org/blog/posts/2026-02-23-open-source-ai-agent-frameworks-compared)
- [Definitive Guide to Agentic Frameworks 2026 — SoftmaxData](https://blog.softmaxdata.com/definitive-guide-to-agentic-frameworks-in-2026-langgraph-crewai-ag2-openai-and-more/)
- [A Detailed Comparison of Top 6 AI Agent Frameworks 2026 — Turing](https://www.turing.com/resources/ai-agent-frameworks)
- [Introducing MCP — Anthropic](https://www.anthropic.com/news/model-context-protocol)
- [A Year of MCP — Pento](https://www.pento.ai/blog/a-year-of-mcp-2025-review)
- [Why the Model Context Protocol Won — The New Stack](https://thenewstack.io/why-the-model-context-protocol-won/)
- [Donating MCP — Anthropic](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation)
- [AI Computer-Use Benchmarks Guide 2025-2026 — O-Mega](https://o-mega.ai/articles/the-2025-2026-guide-to-ai-computer-use-benchmarks-and-top-ai-agents)
- [Best AI Browser Agents 2026 — Firecrawl](https://www.firecrawl.dev/blog/best-browser-agents)
- [Best Agentic AI Browsers 2026 — KDnuggets](https://www.kdnuggets.com/the-best-agentic-ai-browsers-to-look-for-in-2026)
