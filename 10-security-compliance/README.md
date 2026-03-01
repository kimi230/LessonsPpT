# 10. 보안과 컴플라이언스 — AI를 쓴다는 것의 의미

> 기준: 2026년 2월 / 데이터 흐름, 프라이버시 정책, 배포 옵션, 데이터 최소화, 인증, 인간 검수

---

## 핵심 메시지

> AI를 쓴다는 건, 토큰/데이터가 네트워크를 통해 외부(클라우드/데이터센터)로 갔다가 결과로 돌아오는 것이다.
> "저장 안 한다"고 해도 리스크를 0으로 만들 수는 없다.
> 성능뿐 아니라 **데이터 프라이버시/컴플라이언스 준수 "신뢰도"를 함께** 평가해야 한다.

---

## 1. 데이터 흐름 — 토큰이 이동하는 경로

### 프롬프트-클라우드 경로
- 사용자 장치 → 네트워크 전송 → AI 제공사 클라우드/데이터센터 → 처리 → 응답 반환
- 다수 노출 지점: 클라이언트 장치, 네트워크 전송, 로드 밸런서, 추론 서버, 로깅 인프라

### 주요 리스크
- **국경 간 데이터 흐름**: Gartner — 2027년까지 AI 관련 데이터 침해의 **40% 이상**이 생성형 AI의 부적절한 국경 간 사용에서 발생
- **NHI(비인간 ID) 리스크**: 앱-앱 연결의 장기 API 토큰과 광범위 권한 → 자동 토큰 순환(24-72시간)과 중앙화된 시크릿 관리 권장

### 토큰 수준 필터링
- 선진 팀: 모델 처리 전 비밀/식별자를 **토큰 수준에서 삭제**하는 동적 프라이버시 레이어 구축
- 기밀 문구를 반향하는 응답을 플래깅하는 행동 이상 탐지
- 프롬프트 로그의 종단간 암호화

---

## 2. 로그/메타데이터 — "저장 안 함" 약속의 한계

- 벤더가 다른 리전에 "메타데이터", 텔레메트리, 백업을 저장할 수 있음 → **서면 확인**과 리스크 평가 반영 필요
- 일부 서비스: 재해 복구 데이터센터에 로그/고객 콘텐츠 백업, 보조 서버에 실시간 미러링
- **실제 침해 사례**: 중국 AI 스타트업이 100만 줄+ 로그 스트림(채팅 이력, API 키, 백엔드 메타데이터) 노출
- 계약 요구사항: 원본 데이터·로그·백업·파생물(미세조정 가중치, 임베딩, 체크포인트)의 **명확한 보존 기간**, 삭제 증명서, 삭제 SLA

---

## 3. 주요 AI 제공사 프라이버시 정책 (2025-2026)

### 2중 프라이버시 체계 — 3사 공통 패턴
> 기업 고객은 보호를 받지만, **소비자는 기본적으로 훈련 데이터**가 된다.
> **API 채널**이 훈련 방지와 보존 제어에서 더 강력한 보장.

### OpenAI (ChatGPT)
- **2025.08 모니터링 시스템**: 모든 ChatGPT 대화를 잠재적 유해 콘텐츠 스캔, 우려 대화는 인간 리뷰어에 에스컬레이션 → 법 집행 기관 신고 가능
- **API 데이터**: 기본적으로 훈련에 사용 **안 함**. 악용 모니터링용 30일 보존 (Enterprise ZDR 제외)
- **소비자 데이터**: 모델 훈련 동의 없이 채팅 이력을 무기한 유지할 수 있는 **유일한 제공사**
- **소송 보류**: 2025.06 법원 명령으로 일부 콘텐츠 보존 공개

### Anthropic (Claude)
- **2025.09 프라이버시 전환**: 소비자 채팅/코딩 세션 데이터를 **기본적으로** AI 훈련에 사용. 사용자가 능동적으로 **옵트아웃** 필요
- 옵트인 시 데이터 최대 **5년** 보존, 옵트아웃 시 **30일** 제한. 삭제된 대화는 훈련에 미사용
- **기업 보호**: Claude for Work/Gov/Education, API(Amazon Bedrock, Google Cloud Vertex AI) → 기존 프라이버시 보호 유지

### Google (Gemini)
- **2025.09 옵트아웃 정책**: 파일/사진/동영상/스크린샷. 기록 유지(=훈련 동의) vs 프라이버시(=기록 상실) 선택
- **Vertex AI 보장**: 처리 데이터가 파운데이션 모델 훈련에 **사용 안 함** 계약 보장. 고객 선택 리전에 데이터 잔류

---

## 4. 기업 AI 배포 옵션

| 옵션 | 설명 | 비용/요건 |
|------|------|----------|
| **온프레미스/에어갭** | 오픈소스 모델(Llama 3.3 70B, Mistral, Phi-4) + vLLM/TGI. 데이터가 네트워크 밖으로 나가지 않음 | 높은 초기 투자 |
| **VPC 격리** | 프라이빗 서브넷, 인터넷 게이트웨이 없음, VPC 엔드포인트로 서비스 접근 | $30K-$100K/년, 1-2 FTE |
| **API + 데이터 제어** | AWS Bedrock, Cohere, IBM watsonx — IAM, KMS, 감사 로그, 가드레일 | 사용량 기반 |

- **VPC 격리 ≠ 주권**: 미국 CLOUD Act는 미국 클라우드 제공사에 리전 무관 적용 → 진정한 주권은 비미국 인프라 또는 온프레미스 필요
- Tabnine Enterprise: 완전 에어갭 배포, 텔레메트리/데이터 유출 제로 — 국방·금융 산업 선도

---

## 5. 데이터 최소화·마스킹·비식별화

### 데이터 최소화 원칙
- **목적 제한**: 각 속성의 수집 이유 정의
- **수집 제한**: "혹시 모르니" 필드 지양
- **저장 제한**: 필요한 기간만 보존
- **접근 제한**: 필요한 사람/시스템에만 제한

### 실무 단계
- 캡처 시점에 최소화; 파생 데이터 사용 (생년월일 → "연령대")
- 식별자와 속성을 토큰으로 분리, 매핑은 제한된 볼트에 보관
- 필드 수준 접근: 다른 팀이 다른 뷰를 봄
- AI 시스템은 전체 데이터 불필요 시 **가명화/삭제 버전**만 수신

### AI 생성 합성 데이터
- 통계적 정확성은 유지하면서 프라이버시 리스크 제거 → 데이터 기반 혁신의 강력한 솔루션으로 부상

### 거버넌스 격차
- LLM 프라이버시 리스크를 신뢰성 있게 측정하는 시스템을 가진 조직: **10곳 중 1곳**
- AI 데이터 프라이버시 포괄 거버넌스 프레임워크 보유: **7% (93% 미보유)**

---

## 6. 주요 플랫폼 인증 현황

### OpenAI
- ISO/IEC 27001:2022 (API, ChatGPT Enterprise, Edu). 27017, 27018, 27701 준수
- SOC 2 보고서: 보안·가용성·기밀성·프라이버시 (API, Enterprise, Edu, Team). **소비자 버전(Free, Plus) 미적용**
- 이탈리아: GDPR 위반으로 **€1,500만** 벌금

### Anthropic (Claude)
- SOC 2 Type 2, ISO/IEC 27001. 계층적 접근 제어, 환경 격리, 지속적 리스크 평가
- RBAC, JIT 접근(승인 워크플로우), MFA 필수, 분기별 접근 리뷰
- 프롬프트 인젝션 완화·모델 거버넌스에서 AI 정렬 안전 제어 선도
- EU GDPR 인증 미발표 → DPA(Data Processing Addendum) 체결 필요

### Google (Gemini/Vertex AI)
- ISO 27001, 27017, 27018, 27701. 고급 자동화와 제로 트러스트 아키텍처
- 가장 성숙한 접근 제어 구현, 세밀한 제어와 하드웨어 MFA 옵션
- Vertex AI: 데이터 레지던시 선택과 모델 훈련 금지 계약 보장

### 신규 표준
| 표준 | 상태 |
|------|------|
| **ISO 42001** (AI 관리 시스템) | 기업 AI 거버넌스의 사실상 인증으로 부상 |
| **EU AI Act** | 2026.08.02 전면 적용, 위반 시 글로벌 매출 **최대 7%** 벌금 |
| **SOC 2 AI 기준** | 모델 거버넌스·훈련 데이터 출처에 대한 AI 특화 기준 추가 중 |
| **NIST CSF AI 프로필** | 2025.12 초안 공개 |

---

## 7. 인간 검수 — AI 출력의 최종 관문

- 직원 **80%**: 구현 전 인간이 AI 출력을 검토해야 한다고 응답
- **구조화된 검수 절차**: 명확한 가이드라인, 체크리스트, 프로토콜로 AI 의사결정을 일관 평가
- **검수자 자격**: 적절한 지식·경험·권한·독립성 + 관리 가능한 사건 수 + 적절한 교육
- **오버라이드 로그**: AI 의사결정 번복 시 사유 기록, 경영진 보고
- **사실 확인**: 결과적 의사결정 전 팩트체킹, 인간 검수, 출력 검증 **의무**
- **변경 관리**: 모범 사례에 따라 AI 도구를 도입한 조직 → 성공적 결과 보고 확률 **2.6배**. 그러나 실행 기업은 **43% 미만**

---

## 실무 정책 요약

| 항목 | 권장 |
|------|------|
| 제공사 평가 | 성능 + 프라이버시/컴플라이언스 **"신뢰도"** 함께 평가 |
| 배포 선택 | 엔터프라이즈 옵션(VPC/온프레미스/API 데이터 제어) 우선 |
| 민감 정보 | **최소화·마스킹·비식별화** 원칙 |
| 결과물 검수 | 대외 커뮤니케이션/보고 전 **사람의 최종 확인** |
| 계약 | 보존 기간, 삭제 증명, 훈련 미사용 조항 **서면 확보** |

---

## 출처

- [LLM Data Privacy — Lasso Security](https://www.lasso.security/blog/llm-data-privacy)
- [Data Security within AI Environments — CSA](https://cloudsecurityalliance.org/artifacts/data-security-within-ai-environments)
- [AI Data Privacy Statistics 2025 — Protecto](https://www.protecto.ai/blog/ai-data-privacy-statistics-trends/)
- [Every AI App Data Breach Since Jan 2025 — Barrack](https://blog.barrack.ai/every-ai-app-data-breach-2025-2026/)
- [AI & Cloud Security Breaches 2025 — Reco](https://www.reco.ai/blog/ai-and-cloud-security-breaches-2025)
- [OpenAI, Google & Anthropic Privacy Settings — TVNewsCheck](https://tvnewscheck.com/business/article/openai-google-anthropic-user-privacy-settings/)
- [Anthropic Privacy Pivot — Shelly Palmer](https://shellypalmer.com/2025/08/anthropics-privacy-pivot-users-must-opt-out-by-september-28/)
- [AI Data Privacy 2026 — Drainpipe](https://drainpipe.io/ai-data-privacy-2026-the-ai-privacy-trap/)
- [On-Premise AI Architecture 2026 — PremAI](https://blog.premai.io/on-premise-ai-architecture-complete-enterprise-deployment-guide-for-2026/)
- [Enterprise AI for Air-Gapped Environments — IntuitionLabs](https://intuitionlabs.ai/articles/enterprise-ai-code-assistants-air-gapped-environments)
- [AI Data Minimization — SecurePrivacy](https://secureprivacy.ai/blog/ai-data-minimization)
- [AI Data Minimisation — UK ICO](https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-resources/artificial-intelligence/guidance-on-ai-and-data-protection/how-should-we-assess-security-and-data-minimisation-in-ai/)
- [Deep Compliance Review — TDCommons](https://www.tdcommons.org/dpubs_series/7951/)
- [OpenAI Trust Portal](https://trust.openai.com/)
- [AI Risk & Compliance 2026 — SecurePrivacy](https://secureprivacy.ai/blog/ai-risk-compliance-2026)
- [Human Review Audit Framework — UK ICO](https://ico.org.uk/for-organisations/advice-and-services/audits/data-protection-audit-framework/toolkits/artificial-intelligence/human-review/)
- [Enterprise AI Governance Guide — Liminal](https://www.liminal.ai/blog/enterprise-ai-governance-guide)
- [State of AI in Enterprise 2026 — Deloitte](https://www.deloitte.com/global/en/issues/generative-ai/state-of-ai-in-enterprise.html)
