# 05. RAG와 검색 — 데이터를 잘 찾고 먹이는 능력

> 기준: 2026년 2월 / AI 활용성은 "정확한 데이터를 찾고 먹이는 능력"에 의해 크게 갈린다

---

## 핵심 메시지

> AI의 출력 품질은 입력 데이터의 품질에 직결된다.
> PDF를 "전부" 넣기보다, 질문과 관련된 부분을 잘라/나눠 넣을수록 정확해진다.

---

## 1. 키워드 검색 vs 시맨틱 검색

| 구분 | 키워드 검색 | 시맨틱 검색 |
|------|-----------|-----------|
| 원리 | 역인덱스 + BM25 (단어 빈도·역문서 빈도·문서 길이) | 신경망이 텍스트를 벡터 임베딩으로 변환, 의미 유사도 측정 |
| 강점 | 빠름, 투명, 해석 가능 | 동의어·의도·자연어 질의 처리 가능 |
| 약점 | 동의어·의도 이해 불가 | 연산 비용 높음, "블랙박스" |
| 적합 | 정확한 조회(에러코드, SKU, 법률 용어) | 자연어 질의, 개념적 검색 |

### 2025-2026 합의: 하이브리드 검색
- 프로덕션 앱은 **키워드 + 벡터 검색을 결합**해야 함
- 단일 쿼리가 키워드·벡터 결과를 독립 검색 → 통합 랭킹으로 병합
- 하이브리드 사용 시 관련성 **두 자릿수 향상**

---

## 2. RAG 고수준 흐름 (비개발자용)

### 수집 단계 (오프라인)
1. **문서 입력** — PDF, Word, 웹페이지, DB 등 원본 데이터 연결
2. **텍스트 추출** — 파싱 라이브러리로 텍스트 추출, 노이즈 제거, 메타데이터 보강
3. **청킹** — 텍스트를 작은 조각(256-512 토큰)으로 분할
4. **임베딩** — 각 청크를 벡터 표현으로 변환
5. **인덱싱** — 벡터 + 메타데이터를 벡터 DB에 저장

### 검색 단계 (런타임)
1. **질의 임베딩** — 사용자 질문을 같은 임베딩 모델로 벡터화
2. **유사도 검색** — 벡터 DB에서 가장 유사한 상위 K개 청크 검색
3. **컨텍스트 주입** — 검색된 청크를 LLM 프롬프트에 구조화된 컨텍스트로 삽입
4. **답변 생성** — LLM이 학습 데이터가 아닌 **검색된 컨텍스트 기반**으로 답변 생성

---

## 3. RAG 최신 개선 (2025-2026)

| 기법 | 설명 |
|------|------|
| **GraphRAG** | 벡터 검색 + 지식 그래프/온톨로지 결합. 검색 정밀도 최대 **99%** |
| **Self-Reflective RAG** | 언제/어떻게 검색할지 동적 결정; 로컬 검색 불충분 시 웹 검색 트리거 |
| **Agentic RAG** | 검색 전략을 계획하고 부분 결과에 기반해 반복, 단일 검색이 아닌 반복적 접근 |
| **Multi-Hop Reasoning** | GFM-RAG, KG2RAG 등 그래프 기반 접근 → HotpotQA/MuSiQue에서 F1 **4-10% 향상** |
| **RAG-Fusion** | 여러 재구성 쿼리 결과를 상호 순위 융합으로 결합 |
| **Multimodal RAG** | 텍스트, 이미지, 테이블 모달리티 간 검색 확장 |
| **Reranking** | 검색 후 재순위화 레이어 추가 → 정확도 향상 |

- **핵심 인사이트**: 새로운 LLM 기능의 70% 이상이 프로덕션에서 조용히 실패 — RAG를 **설계에 통합**하지 않고 **덧붙이기만** 하기 때문

---

## 4. 청킹 전략

| 전략 | 설명 | 성과 |
|------|------|------|
| **재귀 문자/토큰 분할** | 계층적 분할(단락→문장→단어). 시작점으로 권장 | 512 토큰에서 69% 정확도 |
| **시맨틱 청킹** | 임베딩으로 의미 기준 그룹화 | 최대 91-92% 리콜, 비용 높음 |
| **적응형 청킹** | 논리적 토픽 경계에 맞춤 | 임상 연구에서 87% 정확도 (고정 13%대비) |
| **구조 인식 청킹** | 문서 구조(Markdown 헤더, HTML 태그) 활용 | 구조화된 문서에서 가장 쉽고 큰 개선 |
| **계층적 청킹** | 요약 청크(고수준) + 상세 청크(세부사항) 다층 생성 | — |

### 권장 기본값 (2026.02 검증)
- **256-512 토큰**, 10-20% 오버랩
- NVIDIA: FinanceBench에서 1,024토큰 청크 + 15% 오버랩이 최적
- **컨텍스트 절벽**: ~2,500 토큰 부근에서 응답 품질 급락하는 현상 확인(2026.01)

### 2026 신기법
- **Late Chunking**: 전체 문서를 먼저 임베딩 후 청크 분할
- **Contextual Retrieval**: 각 청크에 제목/헤딩/요약을 추가한 후 임베딩
- **Cross-Granularity Retrieval**: 다양한 단위의 청크를 동시 검색

---

## 5. 벡터 데이터베이스 현황

| DB | 특징 | 적합 환경 |
|----|------|----------|
| **Pinecone** | 완전 관리형, 50ms 미만 쿼리, SOC 2 Type II, HIPAA | 기업 프로덕션 |
| **Weaviate** | 오픈소스, 강력한 하이브리드 검색(BM25+벡터), GraphQL API | 하이브리드 검색 필요 시 |
| **Qdrant** | 오픈소스, Rust 기반, 성능+필터링 최고 조합, SOC 2 | 비용 대비 성능 균형 |
| **ChromaDB** | Python 네이티브, 제로 설정, Rust 리라이트(2025)로 4배 속도 | 프로토타이핑, 1,000만 벡터 이하 |
| **Milvus** | 수십억 규모 데이터셋 최적, 저지연 벤치마크 선도 | 대규모 데이터 엔지니어링 |
| **pgvector** | PostgreSQL 확장, 기존 Postgres에 벡터 검색 추가 | 별도 DB 없이 운영 |

- **핵심 인사이트**: "대부분의 RAG 실패는 자초한 것이지 DB 탓이 아니다." 임베딩 모델이 관련성을 결정하고, DB는 성능과 확장성에 영향. 최고의 DB도 **나쁜 임베딩은 보상 못함**

---

## 6. 실용 가이드: 전체 PDF vs 관련 부분 추출

### 전체 문서 투입의 문제
- "프롬프트 스터핑"은 모델 주의력을 분산시킴
- **"Lost in the Middle"** 효과: 긴 컨텍스트의 중간 정보를 체계적으로 놓침
- Chroma(2025.07): 18개 모델 테스트, 컨텍스트 길이 증가 시 검색 성능 저하 확인

### 의사결정 프레임워크

| 상황 | 권장 접근법 |
|------|-----------|
| 짧고 집중된 문서(FAQ, 제품 페이지) | 전체 문서 투입 |
| 긴 다주제 문서(매뉴얼, 정책, 보고서) | RAG + 청킹 (400-512 토큰) |
| 요약/전체 문서 이해 | 장문 컨텍스트 윈도우 |
| 대규모 지식베이스 대상 정밀 Q&A | RAG (비용 효과적, 정밀) |
| 복잡한 기업 니즈 | **하이브리드**: RAG 검색 + 장문 컨텍스트로 심층 분석 |

- 약 **60%** 질문에서 두 접근법 모두 동일한 답변 생성
- 장문 컨텍스트: 문서 전체에 걸친 추론에 우수
- RAG: 정밀 검색에 우수
- **2026 모범 사례**: RAG로 관련 섹션 검색 → 확장된 컨텍스트 윈도우에 전체 주변 맥락 로드

---

## 출처

- [Semantic vs. Keyword Search — Couchbase](https://www.couchbase.com/blog/semantic-search-vs-keyword-search-whats-the-difference/)
- [What is Semantic Search — Google Cloud](https://cloud.google.com/discover/what-is-semantic-search)
- [Semantic vs. Keyword Search — Redis](https://redis.io/blog/semantic-search-vs-keyword-search/)
- [End-to-End RAG Pipeline — Medium](https://medium.com/@puvanakopis/end-to-end-rag-pipeline-ingestion-embeddings-retrieval-generation-804153d35425)
- [How to Build a RAG Pipeline 2026 — kapa.ai](https://www.kapa.ai/blog/how-to-build-a-rag-pipeline-from-scratch-in-2026)
- [Enhancing RAG Best Practices — arXiv](https://arxiv.org/abs/2501.07391)
- [RAG in 2026 — Squirro](https://squirro.com/squirro-blog/state-of-rag-genai)
- [2025 Guide to RAG — EdenAI](https://www.edenai.co/post/the-2025-guide-to-retrieval-augmented-generation-rag)
- [Best Chunking Strategies for RAG 2026 — Firecrawl](https://www.firecrawl.dev/blog/best-chunking-strategies-rag)
- [Finding Best Chunking Strategy — NVIDIA](https://developer.nvidia.com/blog/finding-the-best-chunking-strategy-for-accurate-ai-responses/)
- [Chunking Strategies for RAG — Weaviate](https://weaviate.io/blog/chunking-strategies-for-rag)
- [Vector Database Comparison — LiquidMetal AI](https://liquidmetal.ai/casesAndBlogs/vector-comparison/)
- [Best Vector Databases 2026 — Firecrawl](https://www.firecrawl.dev/blog/best-vector-databases)
- [Top 7 Vector Databases 2026 — DataCamp](https://www.datacamp.com/blog/the-top-5-vector-databases)
- [RAG vs. Prompt Stuffing — Spyglass](https://www.spyglassmtg.com/blog/rag-vs.-prompt-stuffing-overcoming-context-window-limits-for-large-information-dense-documents)
- [RAG vs Large Context Window — Redis](https://redis.io/blog/rag-vs-large-context-window-ai-apps/)
- [Is RAG Obsolete? — Dataiku](https://www.dataiku.com/stories/blog/is-rag-obsolete)
- [Long-Context LLMs and RAG — deepset](https://www.deepset.ai/blog/long-context-llms-rag)
