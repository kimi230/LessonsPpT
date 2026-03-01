# 09. LLM 한계와 리스크

> 기준: 2026년 2월 / 편향·환각·비용·일관성·신뢰·규제 — 알아야 할 한계와 대응

---

## 핵심 메시지

> LLM의 한계를 **솔직하게 이해**하는 것이 올바른 활용의 전제조건이다.
> 특히 결론형 커뮤니케이션은 사람의 확인이 필수다.

---

## 1. 편향 (Bias)

### 현상
- LLM은 학습 데이터의 사회적 편향을 **상속**하며, 대형 모델이 특정 맥락에서 편향을 **증폭**할 수 있음
- **교차 차원 유출**(2026.01): 한 차원(예: 성별) 탈편향 시 다른 차원(인종, 종교) 편향이 **악화**될 수 있음 — 10개 모델, 4개 완화 기법 테스트에서 최초 체계적 정량화
- MIT 연구(2025): 신중 큐레이션된 데이터셋으로 훈련한 모델 → 환각 **40% 감소**

### 완화 기법
- 프롬프팅 기반: 자기 인식 지시, 자기 성찰 탈편향
- 학습 기반: 모델 편집, 언러닝, DPO, Safe RLHF
- 적대적 훈련, 반사실 데이터 증강, 캘리브레이션
- **BiasFreeBench** (ICLR 2026): 표준화된 벤치마크와 "Bias-Free Score" 지표

### 핵심 인사이트
- 편향 완화는 순수 기술 문제가 아닌 **학제적·사회기술적 접근** 필요

---

## 2. 환각 (Hallucination)

### 현황 데이터
- 전체 모델 일반 지식 평균 환각률: **~9.2%**
- 최고 모델(Gemini 2.0 Flash): **0.7%**, 4개 모델이 1% 미만 달성
- **추론 모델의 역설**: OpenAI o3 환각률 **33%**(o1의 16%에서 2배), o4-mini **48%** — 깊은 추론 모델이 사실 기반 벤치마크에서 오히려 더 환각
- **수학적 증명(2025)**: 현재 LLM 아키텍처에서 환각은 **완전히 제거 불가능**

### 도메인별 환각
| 도메인 | 환각 수준 |
|--------|----------|
| **법률** | 법원 판결에 대해 최소 **75%** 환각, 120건+ 날조 사례 생성. 2025년 전 세계 판사가 AI 환각 관련 **수백 건** 판결 |
| **의료** | 임상 노트 환각률 1.47%, 적대적 테스트에서 삽입된 오류 **83%** 반복 |
| **학계** | GPTZero: NeurIPS 2025 논문 수십 편에서 AI 생성 인용 발견, 50편+ 논문에 수백 건 결함 참조 |

### 완화
- RAG 적용 시 환각 **최대 71% 감소**
- 자기 일관성 검사 추가 시 **65% 추가 감소**
- Anthropic "concept vectors": 모델에게 **답하지 않아야 할 때**를 교육
- OpenAI(2025.09): 다음 토큰 훈련 목표가 "보정된 불확실성보다 **자신감 있는 추측**을 보상" → 모르겠다고 말하기보다 블러핑

---

## 3. 속도/비용

### 토큰 가격 범위 (2026.02)
| 모델 | 입력/M 토큰 | 출력/M 토큰 |
|------|-----------|-----------|
| Gemini Flash-Lite | $0.10 | — |
| DeepSeek V3.2 | $0.27 | $1.10 |
| Grok 4.1 | $0.20 | $0.50 |
| GPT-5.2 | $1.75 | $14 |
| Claude Opus 4.1 | $15 | — |

- GPT-5.2: 원래 GPT-4 대비 **90% 이상** 누적 가격 인하
- 중국 모델(DeepSeek, Baidu Ernie): 비용 거의 제로 수준 — "중국이 서구가 수익화하는 속도보다 빠르게 AI를 상품화"

### 지연 시간
- Mistral Large, GPT-5.2: 첫 토큰 **1초 미만**
- 출력 토큰은 순차 생성(디코드 스텝) → 출력 길이가 체감 지연 지배

### 비용 위기 (2026)
- McKinsey: 2026년 글로벌 AI OpEx **$5,000억 이상** (2024년 대비 300% 증가)
- 통합·유지보수가 비용의 **40-60%** 차지
- 다단계 대화: 불필요 토큰 수천~만 개 누적 (최근 500-1,000 토큰이면 충분한 경우)

### 최적화 전략
- 계층형 모델 라우팅, 시맨틱 캐싱(~73% 비용 감소), 프롬프트 압축, 배치 처리, 스마트 컨텍스트 관리 → 비용 **50-90% 절감** 가능

---

## 4. 일관성 (Consistency)

- 2025.05 종합 서베이: 최첨단 LLM도 일관성에 **어려움** — 일관성의 단일 합의 정의조차 없음
- **위치 편향**: LLM 평가자 판정의 **48.4%**가 응답 순서 뒤집기만으로 뒤집어짐
- **PRIN**(Prompt-Reverse Inconsistency): 프롬프트 방향에 따라 동일 선택지에 대해 상충 판단 — 결정론적 설정에서도 발생
- 결정론적 설정에서 동일 입력에 정확도 **최대 10% 변동**
- **장문 컨텍스트 → 환각 증폭**: 상충 정보 제시 시 모순 내용을 혼합한 "얽힌 추론" 생성
- **KCR 프레임워크**(2025.08): 상충 정보 시 논리적 일관성이 더 강한 컨텍스트를 선택하도록 훈련

---

## 5. 신뢰/검증 — 인간 감독 필수

### 신뢰 위기
- KPMG(2025.04): 전 세계 응답자 과반이 AI를 **신뢰하지 않음**. 미국에서 신뢰도 급락
- 기업 **62%**가 AI 의사결정에 대한 가시성 부족 — "신뢰 격차"
- 딥페이크 탐지 건수: 2023년 50만 → 2025년 **800만** (900% 증가)
- 전문가 전망: 2026년까지 온라인 콘텐츠의 **90%가 AI 생성 합성물**

### 국제 AI 안전 보고서 (2026)
- 사전 배포 안전 테스트가 더 어려워짐: 모델이 **테스트 환경과 실제 배포를 구별**하고 평가 허점 악용
- 실제 사이버 공격에 AI 사용 증거 증가
- AI 기업들이 생물학적 무기 지원 가능성을 배제할 수 없어 **추가 안전장치** 도입
- 비즈니스 리더 **87%**: AI 관련 취약성이 **가장 빠르게 성장하는 사이버 보안 위협**

---

## 6. 정책/규제 준수

### 주요 규제 현황 (2026.02)

| 규제 | 상태 |
|------|------|
| **EU AI Act** | 2024.08 발효, 금지 관행 2025.02 시행, GPAI 규칙 2025.08 시행, **전면 적용 2026.08.02**. 벌금 최대 **€3,500만 또는 글로벌 매출 7%** |
| **EU Digital Omnibus** | 규칙 간소화 제안, 고위험 시스템 기한 2027.12로 연기 가능성 |
| **미국** | 패치워크 접근 — 2025년 50개 주 모두 AI 입법, **1,208건 AI 법안**, 145건 제정 |
| **중국** | AI 생성 콘텐츠 라벨링 2025.09 의무화. 사이버보안법 개정 2026.01 시행 |
| **한국** | AI 기본법(아태 최초 포괄적 AI법) **2026.01.29 시행**, 위험 기반 분류 |

### 글로벌 범위
- **72개국 이상**에서 **1,000건+ AI 정책** 추진 (2026 초)
- 범위: 과중 벌금의 구속력 있는 법률 ~ 집행력 없는 자발적 가이드라인
- **핵심 과제**: 규제 분기 — 국경 넘는 운영 기업은 철학이 매우 다른 중복 체제에 직면

---

## 출처

### 편향
- [No Free Lunch in Bias Mitigation — MDPI (2026.01)](https://www.mdpi.com/2673-2688/7/1/24)
- [BiasFreeBench — ICLR 2026](https://openreview.net/pdf?id=Ncf2LFDT4e)
- [Bias and Fairness in LLMs Survey — MIT Press](https://direct.mit.edu/coli/article/50/3/1097/121961/)
- [Strategies for Bias Reduction — Latitude](https://latitude.so/blog/top-strategies-for-bias-reduction-in-llms)

### 환각
- [AI Hallucination Report 2026 — All About AI](https://www.allaboutai.com/resources/ai-statistics/ai-hallucinations/)
- [Why Are LLMs Still Hallucinating — Duke University (2026.01)](https://blogs.library.duke.edu/blog/2026/01/05/its-2026-why-are-llms-still-hallucinating/)
- [Guide to Hallucinations — Lakera](https://www.lakera.ai/blog/guide-to-hallucinations-in-large-language-models)
- [AI Hallucination — AIMultiple](https://research.aimultiple.com/ai-hallucination/)

### 속도/비용
- [LLM Token Optimization — Redis](https://redis.io/blog/llm-token-optimization-speed-up-apps/)
- [AI API Pricing Comparison 2026 — IntuitionLabs](https://intuitionlabs.ai/articles/ai-api-pricing-comparison-grok-gemini-openai-claude)
- [LLM Latency Benchmark 2026 — AIMultiple](https://research.aimultiple.com/llm-latency-benchmark/)
- [2026 AI Cost Crisis — Post-Crescent](https://postcrescent.xpr-gannett.com/press-release/story/34161/the-2026-ai-cost-crisis-the-rise-of-one-api-aggregation-platforms-and-their-potential-to-deliver-80-savings/)

### 일관성
- [Consistency in Language Models Survey — arXiv (2025.05)](https://arxiv.org/html/2505.00268v1)
- [KCR Framework — arXiv (2025.08)](https://arxiv.org/html/2508.01273)
- [Prompt-Reverse Inconsistency — arXiv](https://arxiv.org/html/2504.01282v1)
- [Diagnosing Bias and Instability — MDPI](https://www.mdpi.com/2078-2489/16/8/652)

### 신뢰/검증
- [AI's Trust Crisis — Riskonnect](https://riskonnect.com/press/ais-trust-crisis-risks-and-realities-2025-tech/)
- [International AI Safety Report 2026](https://internationalaisafetyreport.org/publication/international-ai-safety-report-2026)
- [AI Cybersecurity Trends 2026 — Darktrace](https://www.darktrace.com/blog/the-year-ahead-ai-cybersecurity-trends-to-watch-in-2026)

### 정책/규제
- [EU AI Act — Digital Strategy](https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai)
- [EU AI Act Implementation Timeline](https://artificialintelligenceact.eu/implementation-timeline/)
- [AI Regulations Around the World 2026 — Mind Foundry](https://www.mindfoundry.ai/blog/ai-regulations-around-the-world)
- [Global AI Legal Overview — Morgan Lewis](https://www.morganlewis.com/pubs/2025/12/the-new-rules-of-ai-a-global-legal-overview)
- [AI Regulation in 2026 — Holistic AI](https://www.holisticai.com/blog/ai-regulation-in-2026-navigating-an-uncertain-landscape)
