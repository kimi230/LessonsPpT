# 12. 도구 및 모델 현황 비교

> 기준: 2026년 2월 / 주요 AI 모델별 포지셔닝, 도구 모듈, 벤치마크 비교

---

## 핵심 메시지

> 단일 모델이 모든 영역을 지배하지 않는다.
> **업무 목적별 선택 가이드**(환경·정책·보안·비용·품질 기준)로 접근해야 한다.

---

## 1. 주요 모델별 포지셔닝

### ChatGPT (OpenAI) — 업무 생산성 툴체인 중심
- **Projects**: 문서(PDF, Docs, Excel) 최대 40개 업로드 → 업로드 지식 기반 응답. 프로젝트 공유(2025.10)로 팀 협업
- **Canvas**: 2패인 인터페이스 (대화 + 문서/코드 직접 편집). PDF, Markdown, Word 내보내기
- **파일/데이터 분석**: 스프레드시트·PDF·스크린샷 업로드 → 트렌드 발견·보고서 요약
- **Deep Research**: 수백 개 웹사이트 검색하여 심층 리서치
- **기업 커넥터**: Dropbox, Box, Google Drive, OneDrive, SharePoint
- **GPT-5 시리즈**: 2025.08 기본 모델. 빠른/대화형/추론 모드 통합. 400K 토큰. **주간 8억 활성 사용자, 68% 시장 점유율**
- 가격: Free / Plus($20/월) / Pro($200/월)

### Claude (Anthropic) — 코딩/에이전트/도구 활용 포지셔닝
- **Claude Code**: 터미널 네이티브 에이전트(2025.02 출시). 코딩 도구가 아닌 **범용 컴퓨터 자동화 에이전트**. 2025.07까지 매출 5.5배 증가. MS, Google, OpenAI 직원도 사용
- **Computer Use**: 화면 보기·커서 이동·클릭·타이핑으로 컴퓨터 직접 조작. Chrome 확장(2025.08)
- **Claude Cowork**(2026.01): 비기술자용 GUI — 영수증→스프레드시트, 파일 정리, 보고서 초안
- **모델 라인업**:
  - Opus 4.6: 에이전트 팀, PPT 내 Claude, 14.5시간 자율 작업(METR). 16개 Opus 에이전트가 Rust로 C 컴파일러 작성 → 리눅스 커널 컴파일 성공
  - Sonnet 4.6: Opus급 코딩, 더 나은 지시 이행·도구 신뢰성
  - **Sonnet 5 "Fennec"**(2026.02.03): SWE-Bench **82.1%** — 최초 80% 돌파
- 가격: Free / Pro($20/월) / Max($100-200/월)

### Gemini (Google) — Google 생태계 연계 + 작업 자동화
- **Workspace Studio**(GA 2025.12): 노코드 AI 에이전트 빌더. 자연어로 자동화 설명 → Gemini 3가 플로우 생성. Gmail, Drive, Chat + Asana, Jira, Salesforce 통합. 초기 채택자: 초안 작성 시간 **90% 감소**, 알파 30일간 **2,000만 작업** 자동화
- **Workspace 통합**: Gmail, Docs, Sheets, Slides, Drive, Chat 사이드 패널에 Gemini 내장
- **Gemini Enterprise**: 노코드 워크벤치, Google Workspace·MS 365·Salesforce·SAP 연결, 중앙 거버넌스
- **멀티모달 퍼스트**: Gemini 3 Pro — 텍스트·이미지·오디오·비디오 네이티브 처리. 1M 토큰. AIME 2025 **95.0%**
- **시장 성장**: 5.4% → **18.2%** 시장 점유율. Q3 2025 사용자 44% 성장
- 가격: AI Pro($19.99/월) / AI Ultra($249.99/월)

### Grok (xAI) — 실시간/검색 + X(소셜) 연동
- **핵심 차별점**: X(구 Twitter) 데이터와 오픈 웹에 **실시간 접근**. 감정 분석, 뉴스 요약, 소셜 미디어 모니터링에 이상적
- **DeepSearch/DeeperSearch**: 웹 탐색, 소스 검증, 실시간 정보 수집
- **Grok 4**(2025.07): 네이티브 도구 사용, 라이브 검색 API(X·웹·뉴스), 2M 토큰
- **배포 확장**: Tesla 차량 통합, $2억 국방부 계약, $3억 Telegram 파트너십
- **Grok 5** 확인: Q1 2026, 6조 파라미터, 네이티브 멀티모달
- API: $0.20/$0.50/M 토큰 — 주요 제공사 중 **최저 단가**
- **주의**: X에서 유행하는 미검증 주장/잘못된 정보를 반영할 수 있음

### 기타 주목 모델

| 모델 | 특징 |
|------|------|
| **Llama 4** (Meta) | 10M 토큰 컨텍스트(Scout), MoE. 최대 오픈소스 생태계 |
| **Mistral 3 Large** | 675B MoE(41B 활성), Apache 2.0. 유럽어 강점(다국어 MMLU 85.5%) |
| **DeepSeek V3.2** | 685B MoE, MIT 라이선스, $0.27/$1.10/M 토큰. GPT-5로 $15 드는 작업 ~$0.50 |
| **DeepSeek R1** | 투명한 사고 과정, o1 경쟁. 도구 사용에 사고 직접 통합 최초 |
| **Qwen 3** (Alibaba) | 중국어 강점, 경쟁력 있는 벤치마크 |
| **Phi-4** (MS) | 소형 모델이 대형과 경쟁하는 추론 성능, 온디바이스 배포 적합 |

---

## 2. 도구 모듈 — 플랫폼별 비교

### 웹 검색/리서치
| 플랫폼 | 기능 |
|--------|------|
| ChatGPT | Deep Research(수백 웹사이트), 웹 검색 |
| Gemini | Google Search 네이티브 통합, DeepResearch 에이전트 |
| Grok | DeepSearch + 실시간 X 데이터 |
| Claude | 웹 검색, 딥 리서치 확장 중 |
| Perplexity | AI 검색 엔진(인용 포함) |

### 파일 기반 Q&A
| 플랫폼 | 기능 |
|--------|------|
| ChatGPT Projects | 최대 40 파일 업로드, 문서 기반 응답 |
| Google NotebookLM | 사용자 문서 기반 답변, Audio Overview 생성 |
| Claude | 200K(표준)/1M(베타) 컨텍스트로 장문 문서 처리 |
| Gemini | 1M 토큰으로 대규모 문서셋 네이티브 처리 |

### 데이터 분석 (표/그래프/시각화)
| 플랫폼 | 기능 |
|--------|------|
| ChatGPT | Code Interpreter/Advanced Data Analysis — 차트·Python 실행·스프레드시트 |
| Gemini in Sheets | Google Sheets에서 AI 지원 데이터 분석 |
| Claude | Artifacts — 인터랙티브 시각화·데이터 분석 생성 |

### 문서 작성 (Word/PPT/보고서 초안)
| 플랫폼 | 기능 |
|--------|------|
| ChatGPT Canvas | 2패인 작성/편집, PDF·Markdown·Word 내보내기 |
| Claude | Opus 4.6 "Claude in PowerPoint", Cowork 보고서 초안 |
| Gemini | Docs "Help me write", Workspace Studio 문서 워크플로우 |
| Gamma | 노트 → 프레젠테이션·문서·웹페이지 변환 전문 |

### 코드 생성 및 실행
| 도구 | 특징 |
|------|------|
| **Claude Code** | 터미널 네이티브, SWE-bench **82.1%** (Sonnet 5) |
| **GitHub Copilot** | IDE 통합, 광범위 언어 지원, 68% 시장 점유 |
| **Cursor** | 리포지토리 수준 에이전트, 다파일 리팩터·디버깅 |
| **ChatGPT Canvas** | 코드 리뷰·로그 추가·버그 수정·언어 포팅 바로가기 |
| **Gemini Code Assist** | 무료 티어 월 180,000 코드 완성 |
| **Amazon Q Developer** | AWS 통합 코딩·운영 어시스턴트 |

---

## 3. 벤치마크 비교 (2025-2026)

### 코딩
| 모델 | SWE-bench Verified |
|------|-------------------|
| Claude Sonnet 5 "Fennec" | **82.1%** (최초 80% 돌파) |
| Claude Opus 4.5 | 80.9% |
| GPT-5.1 | 76.3% |
| Gemini 3 Pro | 76.2% |
| Grok 4.1 | 74.9% |

### 수학/추론
| 모델 | AIME 2025 | GPQA Diamond |
|------|-----------|-------------|
| Gemini 3 Pro | **95.0%** | **91.9%** (인간 전문가 초과) |
| GPT-5.1 | 94.6% | — |
| Grok 4.1 | 88.0% | — |

### 일반 추론 (LMArena Elo)
- Gemini 3 Pro: **1501** (최초 1500 돌파)

### 창작/글쓰기
- GPT-5.1: Creative Writing v3 벤치마크 **1위**

### 컨텍스트 윈도우
| 모델 | 윈도우 |
|------|--------|
| Grok 4 | 2,000,000 토큰 |
| Gemini 3 Pro | 1,000,000 토큰 |
| GPT-5.1 | 400,000 토큰 |
| Claude Opus 4.5 | 200,000 (1M 베타) |

### 비용 효율 (API, /M 토큰)
| 모델 | 입력 | 출력 |
|------|------|------|
| DeepSeek V3.2 | $0.27 | $1.10 |
| Grok 4.1 | $0.20 | $0.50 |
| GPT-5.1 | GPT-4o 대비 75% 저렴 | — |

---

## 4. 모델 선택 가이드

| 업무 목적 | 추천 모델 | 이유 |
|----------|----------|------|
| **코딩/개발** | Claude (Sonnet 5/Code) | SWE-bench 1위, 터미널 에이전트 |
| **수학/추론** | Gemini 3 Pro | AIME·GPQA 최고 성능 |
| **글쓰기/창작** | GPT-5.1 | Creative Writing 1위 |
| **실시간 정보/소셜** | Grok 4 | X 데이터 + 실시간 웹 |
| **비용 민감** | DeepSeek V3.2 | 30배+ 저렴 |
| **Google 생태계** | Gemini | Workspace 완전 통합 |
| **에어갭/온프레미스** | Llama 4 / Mistral | 오픈소스 + 자체 호스팅 |

> 2026년 가장 생산적인 팀은 **여러 모델을 작업 유형에 따라 전략적으로 사용**한다.

---

## 출처

### ChatGPT
- [Projects in ChatGPT — OpenAI](https://help.openai.com/en/articles/10169521-projects-in-chatgpt)
- [Introducing Canvas — OpenAI](https://openai.com/index/introducing-canvas/)
- [What Is ChatGPT 2026 — SearchAtlas](https://searchatlas.com/blog/what-is-chatgpt/)

### Claude
- [Claude Code is the Inflection Point — SemiAnalysis](https://newsletter.semianalysis.com/p/claude-code-is-the-inflection-point)
- [Eight Trends Defining Software in 2026 — Claude Blog](https://claude.com/blog/eight-trends-defining-how-software-gets-built-in-2026)
- [Claude Sonnet 4.6 — Anthropic](https://www.anthropic.com/claude/sonnet)

### Gemini
- [Google Workspace Studio — Google](https://workspace.google.com/blog/product-announcements/introducing-google-workspace-studio-agents-for-everyday-work)
- [Gemini Enterprise — Google Cloud](https://cloud.google.com/blog/products/ai-machine-learning/introducing-gemini-enterprise)
- [Gemini for Workspace Guide 2026 — Refractiv](https://refractiv.co.uk/news/gemini-google-workspace-guide/)

### Grok
- [What is Grok AI — BiTechnology](https://www.bitechnology.com/what-is-grok-ai-xai-features-and-use-cases/)
- [What to Expect from Grok in 2026 — SentiSight](https://www.sentisight.ai/what-to-expect-from-grok-in-2026/)
- [What Is Grok 4? — Built In](https://builtin.com/artificial-intelligence/grok-4)

### 오픈소스/기타
- [State of Open Source AI Models 2025 — Red Hat](https://developers.redhat.com/articles/2026/01/07/state-open-source-ai-models-2025)
- [15 Best Open Source AI Models 2026 — Elephas](https://elephas.app/blog/best-open-source-ai-models)
- [Top 9 LLMs Feb 2026 — Shakudo](https://www.shakudo.io/blog/top-9-large-language-models)

### 벤치마크
- [AI Model Benchmarks Feb 2026 — LM Council](https://lmcouncil.ai/benchmarks)
- [GPT-5 vs Claude 4.5 vs Gemini 3 Pro — Humai](https://www.humai.blog/best-ai-models-2026-gpt-5-vs-claude-4-5-opus-vs-gemini-3-pro-complete-comparison/)
- [Gemini 3.0 vs Others — Clarifai](https://www.clarifai.com/blog/gemini-3.0-vs-other-models)
- [AI API Pricing 2026 — IntuitionLabs](https://intuitionlabs.ai/articles/ai-api-pricing-comparison-grok-gemini-openai-claude)
