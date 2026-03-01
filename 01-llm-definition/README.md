# 01. LLM 정의와 핵심 속성

> 기준: 2026년 2월 / LLM이 무엇이고 왜 인간 수준처럼 보이는지, 핵심 속성 4가지 정리

---

## 핵심 메시지

> **LLM = 토큰 시퀀스에서 다음 토큰을 예측하는 모델**
> (예측을 너무 잘해서 인간 수준의 대화·추론처럼 보이게 됨)

---

## 1. 다음 토큰 예측 — LLM의 본질

### 작동 원리
- 입력 토큰 시퀀스(컨텍스트)를 받아 전체 어휘에 대한 **확률 분포를 계산**
- 하나의 토큰을 선택 → 입력에 추가 → 반복 = **자기회귀(autoregressive) 생성**
- 학습 시 "I do not like green eggs and ham"에서 `(I, do)`, `(I do, not)`, `(I do not, like)` … 형태의 훈련 예시 사용
- 모델 내부 마지막 단계: **logits**(원시 수치 점수) → softmax → 확률 → 토큰 샘플링/선택

### 왜 인간 수준으로 보이는가
- 다음 단어를 정확히 예측하려면 세상에 대한 상세한 정보를 파라미터에 **저장**해야 함
- "다음 토큰 예측이 손실 함수이지만, 그 과정에서 **풍부한 내부 세계 모델**을 발달시킨 것"
- 단순 텍스트 완성 → **지시 미세조정(Instruction Fine-tuning)** + **RLHF** → 지시를 따르는 어시스턴트로 전환
- Physical Review E(2025.09): 각 트랜스포머 레이어가 다음 토큰 예측 정확도 향상에 **균등하게 기여**하는 보편 법칙 발견

---

## 2. 비결정론적 특성 — 같은 질문, 다른 결과

### 의도적 비결정성 (샘플링 파라미터)

| 파라미터 | 설명 |
|----------|------|
| **Temperature** | logits를 스케일링하여 랜덤성 제어. 낮을수록 예측 가능, 높을수록 창의적 |
| **Top-K** | 상위 K개 토큰만 후보로 제한. 단순하지만 보정이 어려움 |
| **Top-P (Nucleus)** | 누적 확률 P를 초과하는 최소 토큰 집합에서 샘플링. 모델 확신도에 자연 적응 |
| **Min-P** | 최상위 토큰 확률 대비 최소 비율 설정. ICLR 2025 18위, 오픈소스 기본 방식으로 채택 |

- 2026년 오픈소스 합의: **Temperature + Min-P** 조합

### 비의도적 비결정성 (Temperature=0에서도 발생)
- **배치 크기 변동**: 동일 연산이 다른 순서로 실행 → 다른 부동소수점 결과 (Thinking Machines Lab, 2025.09)
- **부동소수점 비연관성**: 대수학의 결합 법칙이 부동소수점에서는 성립하지 않음
- **GPU 비결정성**: 최적화된 GPU 연산이 속도를 위해 결정성을 교환
- **MoE 라우팅**: GPT-4 등에서 다른 사용자의 토큰이 같은 전문가 슬롯을 경합 → 경쟁 조건
- **멀티-GPU 스케일링**: 노드 간 통신에서 추가 변동 누적

### 비결정성 완화 연구
- **배치 불변 커널** (Thinking Machines Lab, 2025): 1,000회 동일 실행 → 1,000회 동일 출력, 성능 오버헤드 10-40%
- **LLM-42** (Microsoft Research, 2026.01): 비결정론적 고속 경로 + 경량 검증-롤백 루프
- **Seed 파라미터**: OpenAI 등이 "대부분 결정론적" 동작을 위한 seed 파라미터 제공

---

## 3. 컨텍스트 의존성 — 입력이 출력 품질을 좌우

### 주요 모델 컨텍스트 윈도우 (2025-2026)

| 모델 | 컨텍스트 윈도우 | 제공사 |
|------|----------------|--------|
| Magic LTM-2-Mini | 100M 토큰 (주장) | Magic |
| Llama 4 Maverick | 10M 토큰 | Meta |
| Gemini 2.5 Pro/Flash | 1M 토큰 | Google |
| GPT-5 / GPT-5.2 | 400K 입력 / 128K 출력 | OpenAI |
| Claude 4 Sonnet | 200K (1M 베타) | Anthropic |
| Claude Opus 4 | 200K 토큰 | Anthropic |
| DeepSeek R1 / V3 | 128K-164K 토큰 | DeepSeek |

- 2023년 중반 이후 최대 컨텍스트 윈도우: 연간 ~30배 성장
- 장문 벤치마크에서 80% 정확도 달성 입력 길이: 9개월간 250배 이상 증가

### 핵심 특성
- **인컨텍스트 러닝**: 가중치 업데이트 없이 프롬프트 내 예시만으로 복잡한 과제 수행 가능
- **"Lost in the Middle" 문제**: 긴 프롬프트의 **시작과 끝** 정보는 잘 파악하지만 **중간**에 묻힌 정보는 놓치기 쉬움
- **정보 과부하**: 과도한 컨텍스트 → 핵심 정보 누락 가능
- **RAG vs 장문 컨텍스트**: RAG는 문서 크기가 커져도 안정적 정확도 유지, 장문 모델은 수만 토큰 이후 성능 저하 경향
- Claude 4 Sonnet: 200K 전체 컨텍스트 윈도우에서 **5% 미만 정확도 저하** 달성

---

## 4. 형식 유도 — 구조화된 출력 생성

### 기법 (신뢰도순 정렬)

| 기법 | 설명 | 적합 환경 |
|------|------|-----------|
| **프롬프트 엔지니어링** | 출력 형식을 명시적으로 지시 또는 few-shot 예시 | 가장 간단 |
| **JSON Mode / API** | 스키마를 파라미터로 전달, 제공사가 검증 | 상용 API 권장 |
| **Function Calling** | 더미 함수 정의 → 모델이 호출 시 규격 JSON 생성 | 함수 호출 지원 모델 |
| **Constrained Decoding** | 생성 시 비호환 토큰 마스킹 → 100% 준수 | 오픈소스(Guidance, llama.cpp) |

### 주의: 형식 제약이 추론에 미치는 영향
- 2025년 연구: 엄격한 포맷 지시가 **기저 과제 성능을 저하**시킬 수 있음
- 해결 방향: **추론 단계와 포맷팅 단계를 분리** (먼저 자유롭게 추론 → 별도 포맷팅)

### 실용 권장사항 (2026)
- 상용 API: 네이티브 구조화 출력 파라미터 사용 (JSON 스키마 전달)
- 오픈소스: 제약 디코딩 사용 (Guidance, llama.cpp grammars)
- 복잡 추론 과제: 추론 단계와 포맷팅 단계 분리
- "명확한 구조와 맥락이 세련된 문장보다 중요 — 대부분의 프롬프트 실패는 모델 한계가 아니라 **모호함**에서 비롯"

---

## 출처

### 다음 토큰 예측
- [How LLM Predict the Next Token — ersantana.com](https://ersantana.com/llm/how-llms-talk)
- [How Next-Token Prediction Works — Medium](https://medium.com/@kstvkmrchanda2/how-next-token-prediction-works-in-llms-9a89c1b9f6ae)
- [Logits and Next-Token Prediction — Substack](https://mikexcohen.substack.com/p/llm-breakdown-26-logits-and-next)
- [A Law of Next-Token Prediction — Physical Review E (2025.09)](https://journals.aps.org/pre/abstract/10.1103/5rn3-49lc)
- [LLMs Are Not Just Next Token Predictors — Inquiry (2024)](https://www.tandfonline.com/doi/full/10.1080/0020174X.2024.2446240)

### 비결정론적 특성
- [Why Temperature=0 Doesn't Guarantee Determinism — Brenndoerfer](https://mbrenndoerfer.com/writing/why-llms-are-not-deterministic)
- [LLM Sampling Parameters Explained — Let's Data Science](https://www.letsdatascience.com/blog/llm-sampling-temperature-top-k-top-p-and-min-p-explained)
- [Defeating Nondeterminism — Thinking Machines Lab (2025.09)](https://thinkingmachines.ai/blog/defeating-nondeterminism-in-llm-inference/)
- [LLM-42: Determinism with Verified Speculation — arXiv (2026.01)](https://arxiv.org/html/2601.17768v1)
- [Why Deterministic Output Is Nearly Impossible — Unstract](https://unstract.com/blog/understanding-why-deterministic-output-from-llms-is-nearly-impossible/)

### 컨텍스트 윈도우
- [Best LLMs for Extended Context Windows 2026 — AIMultiple](https://aimultiple.com/ai-context-window)
- [Context Length Comparison 2026 — Elvex](https://www.elvex.com/blog/context-length-comparison-ai-models-2026)
- [LLMs Now Accept Longer Inputs — Epoch AI](https://epoch.ai/data-insights/context-windows)
- [Why Larger Context Windows Are All the Rage — IBM Research](https://research.ibm.com/blog/larger-context-window)
- [LLM Context Windows — Redis](https://redis.io/blog/llm-context-windows/)

### 구조화된 출력
- [Guide to Structured Text Generation — Dataiku](https://www.dataiku.com/stories/blog/your-guide-to-structured-text-generation)
- [Best Way to Generate Structured Output — Instill AI](https://www.instill-ai.com/blog/llm-structured-outputs)
- [Decoupling Task-Solving and Output Formatting — arXiv (2025.10)](https://arxiv.org/html/2510.03595v1)
- [Structured Outputs with LLMs — Battaglia (2025.09)](https://matiasbattaglia.com/2025/09/11/Using-Structured-Outputs-with-LLMs.html)

### 종합
- [A Comprehensive Overview of LLMs — ACM TIST (2025)](https://dl.acm.org/doi/10.1145/3744746)
- [LLMs: Architectures, Trends, Taxonomy — Springer Nature (2025)](https://link.springer.com/article/10.1007/s42452-025-07668-w)
- [The State of LLMs 2025 — Sebastian Raschka](https://magazine.sebastianraschka.com/p/state-of-llms-2025)
