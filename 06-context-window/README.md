# 06. 컨텍스트 윈도우 한계 — 기억의 크기와 품질

> 기준: 2026년 2월 / 컨텍스트 크기 한계, Lost in the Middle, 상충 정보 문제와 실무 대응

---

## 핵심 메시지

> 컨텍스트가 커질수록 핵심이 희미해지거나, 모델이 "헷갈림"할 수 있다.
> 상충/불일치 정보를 계속 넣으면 일관성이 흔들린다.

---

## 1. 주요 모델 컨텍스트 윈도우 (2025-2026)

| 모델 | 컨텍스트 윈도우 | 제공사 |
|------|----------------|--------|
| Llama 4 Scout | 10M 토큰 (업계 최대, MoE 17B/109B) | Meta |
| Gemini 3 Pro | 1M-10M 토큰 | Google |
| Gemini 2.5 Pro/Flash | 1M 토큰 | Google |
| GPT-4.1 / GPT-4.1 Mini | 1M 토큰 | OpenAI |
| GPT-5 / GPT-5.2 | 256K-400K (출력 128K) | OpenAI |
| Claude 4 Sonnet | 200K (1M 베타, Tier 4+) | Anthropic |
| Claude Opus 4.5 | 200K 토큰 | Anthropic |
| Grok 4 | 2M 토큰 | xAI |
| DeepSeek R1/V3 | 128K-164K 토큰 | DeepSeek |

---

## 2. "Lost in the Middle" 문제

### 현상
- **U자형 성능 곡선**: 시작(primacy bias)과 끝(recency bias) 정보는 잘 파악, **중간 정보는 30% 이상 성능 저하**
- 시작/끝 정보 정확도: **85-95%**, 중간 섹션: **76-82%**

### 원인
- **RoPE(Rotary Position Embedding)**: 장기 감쇠 효과로 시작·끝 토큰 우선, 중간 토큰 경시
- 이 아키텍처적 편향은 문서 순서 랜덤화와 무관하게 지속
- 장문 컨텍스트 전용 모델에서도 발생

### 완화 전략
- 가장 중요한 문서를 컨텍스트 **시작과 끝**에 배치
- 2단계 검색: 넓은 벡터 유사도 검색(20-100건) → 정교한 재순위화
- 가장 관련 높은 부분을 사전 요약/하이라이트/인용
- 컨텍스트를 슬림하게 유지; 단일 거대 프롬프트 대신 **계층적/다단계 읽기**

---

## 3. 상충/불일치 정보로 인한 불안정

### "Context Rot" (Chroma Research)
- GPT-4.1, Claude 4, Gemini 2.5, Qwen3 등 18개 모델 평가
- 입력 길이가 커질수록 성능이 **점점 더 불안정해짐**
- 모델이 컨텍스트를 균일하게 사용하지 않음

### 광고된 윈도우 vs 실효 윈도우 (MECW)
- arXiv(2025.09): 일부 최상위 모델이 **100 토큰**에서도 실패
- 대부분 **1,000 토큰**에서 심각한 성능 저하
- 광고된 윈도우 대비 **최대 99% 미달**

### 성능 절벽 현상
- 특정 길이까지 95% 정확도 유지 → **갑자기 60%로 급락** (점진적이 아닌 급격한 저하)
- 대부분의 모델이 광고 용량보다 훨씬 이전에 신뢰성 상실 (200K 주장 → ~130K에서 불안정)

### 도구/정보 과부하
- DeepSeek-v3: 30개 이상 도구 정의 시 설명 겹침으로 혼동, 100개 이상 시 **거의 확실히 실패**
- "컨텍스트 윈도우 한계가 아니라 **컨텍스트 혼동(confusion)**"

### 위치 편향
- LLM 평가 판정의 **48.4%**가 응답 순서 뒤집기만으로 **뒤집어짐**
- 원래 순서에서는 판사 전원 100% 합의 → 체계적 공유 편향

---

## 4. 실무 활용 원칙

### 정확성이 중요한 업무
- 핵심 근거/데이터/제약을 명확히, **컨텍스트를 슬림하게**
- 그럴듯한 방해 정보(plausible distractors) 제거
- Chain-of-Thought, few-shot 프롬프팅, 프롬프트 스캐폴딩 사용

### 학습/탐색 업무
- 거칠게 질문 → 후속 질문으로 맥락을 **점진적으로 확장**
- 요약·압축으로 컨텍스트 부패 방지
- **틀릴 가능성은 전제**로 진행

### 컨텍스트 엔지니어링 > 프롬프트 엔지니어링
- 프로덕션급 LLM 앱은 3개 레이어 관리: **지시(instruction), 지식(knowledge), 도구(tool)** 데이터
- 4가지 전략: 쓰기(외부 메모리) → 선택(관련 검색) → 압축(요약/트리밍) → 격리(구획화된 워크플로우)

### 구체적 기법

| 기법 | 설명 |
|------|------|
| 컨텍스트 격리 | 다른 컨텍스트를 전용 스레드에 분리 → 교차 오염 방지 |
| 컨텍스트 가지치기 | 작업 진행에 따라 불필요 정보 정기 제거 |
| Temperature 조정 | 정확도 중요 → 낮은 값, 창의/탐색 → 높은 값 |
| 프롬프트 테스트 | 알려진 입출력 데이터셋으로 정확도·일관성·재현성·환각률 평가 |

---

## 5. 장문 컨텍스트 벤치마크

| 벤치마크 | 특징 |
|---------|------|
| **LV-Eval** (ICLR 2025) | 5단계 길이(16K-256K), 혼동 사실 삽입, 키워드 리콜 |
| **LongBench v2** | 8K-2M 단어, 실세계 다중과제 심층 이해·추론 |
| **100-LongBench** (ACL 2025) | 길이 조절 가능, 기존 벤치마크가 장문 능력을 제대로 측정 못한 문제 해결 |
| **LongGenBench** | 장문 텍스트 생성(16K-32K) 평가 — 대부분 벤치마크가 이해만 측정하는 간극 해소 |

- 소형 모델이 더 빨리 저하: Qwen2.5-14B는 1K→32K에서 43.87→20.53으로 급락
- 인간 vs LLM 격차: 인간 참조 귀속 정확도 >90%, 최첨단 LLM은 **30% 미만**

---

## 출처

- [Best LLMs for Extended Context Windows 2026 — AIMultiple](https://aimultiple.com/ai-context-window)
- [Context Window Size Comparison — JuheAPI](https://www.juheapi.com/blog/context-window-size-comparison-gpt5-claude4-gemini25-glm46)
- [Context Length Comparison 2026 — Elvex](https://www.elvex.com/blog/context-length-comparison-ai-models-2026)
- [Lost in the Middle — Stanford/UW (arXiv)](https://arxiv.org/abs/2307.03172)
- [Lost in the Middle — ACL/MIT Press](https://direct.mit.edu/tacl/article/doi/10.1162/tacl_a_00638/119630/)
- [Solving Lost in the Middle — Maxim AI](https://www.getmaxim.ai/articles/solving-the-lost-in-the-middle-problem-advanced-rag-techniques-for-long-context-llms/)
- [Context Rot — Chroma Research](https://research.trychroma.com/context-rot)
- [Maximum Effective Context Window — arXiv](https://arxiv.org/abs/2509.21361)
- [Context Length Alone Hurts — arXiv](https://arxiv.org/html/2510.05381v1)
- [The LLM Context Window Paradox — Data Science Dojo](https://datasciencedojo.com/blog/the-llm-context-window-paradox/)
- [Context Engineering Best Practices — Kubiya](https://www.kubiya.ai/blog/context-engineering-best-practices)
- [Context Engineering Guide — FlowHunt](https://www.flowhunt.io/blog/context-engineering/)
- [LV-Eval — OpenReview](https://openreview.net/forum?id=WQwy1rW60F)
- [LongBench v2](https://longbench2.github.io/)
- [100-LongBench — ACL 2025](https://aclanthology.org/2025.findings-acl.903/)
- [Long Context, Less Focus — arXiv (2026.02)](https://www.arxiv.org/pdf/2602.15028)
