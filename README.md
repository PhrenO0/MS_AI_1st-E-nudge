# 🛡️ AI Community Nudge
**AI 기반 실시간 유해 콘텐츠 탐지 및 데이터 로깅 시스템**

Microsoft AI School 1차 팀 프로젝트 · 6인

---

### 🧭 Product Snapshot

댓글을 올리는 순간 텍스트와 이미지를 함께 판정하고, 위험도에 따라 개입 강도를 나눴습니다.

| 판정 | 기준 (`main.py`) | 작성자에게 보이는 것 | 운영자에게 남는 것 |
| :--- | :--- | :--- | :--- |
| `none` | 텍스트 독성 점수 0.44 미만 | 그대로 게시 | 전체 댓글 모니터링 |
| `offensive` | 텍스트 독성 점수 0.44 이상 0.55 미만 | 경고 팝업에서 **수정** 또는 **확인(게시)** 선택 | 경고를 무시하고 게시한 댓글 목록 |
| `hate` | 텍스트 독성 점수 0.55 이상, 또는 이미지 유해 확률 0.9 이상 | 등록 차단 | 모델 추론 로그 |

독성 점수 = `offensive 확률 × 0.5 + hate 확률 × 1.0`

---

### 🛠 Tech Stack
- **Framework**: `FastAPI`
- **Cloud**: `Azure SQL Database`, `Azure Blob Storage`
- **AI/ML**: `Scikit-learn (NLP)`, `Custom Vision API`
- **Database**: `SQLAlchemy` (NVARCHAR 다국어 처리)

---

### 📡 Data Pipeline
데이터 엔지니어링 관점의 **비정형 데이터 처리 및 로깅** 흐름입니다.

1. **Ingestion**: 사용자로부터 텍스트와 이미지 동시 수집
2. **Parallel Process**: `asyncio.gather`를 통한 AI 분석 및 스토리지 업로드 병렬화 (응답 속도 최적화)
3. **Storage Strategy**:
    - **Blob Storage**: 이미지 원본 저장 후 고유 **URL** 추출
    - **SQL DB**: 분석 스코어(Toxicity Score)와 이미지 URL을 매핑하여 적재
4. **MLOps Logging**: 모델 버전별 추론 시간(`inference_time`)을 개별 테이블에 기록하여 성능 모니터링 기반 마련



---

### 🗄️ Database Schema (ERD)
데이터 분석 효율성을 고려한 관계형 설계입니다.

- **Comments**: AI 분석 라벨 및 독성 스코어 실시간 저장
- **AdminLog**: 유해 콘텐츠 사후 관리를 위한 감사 로그
- **MLModel**: 모델 성능 지표 및 버전 관리 (Data Analysis용)



---

### 🌟 Core Value
- **Scalability**: 이미지 원본은 스토리지에, 주소는 DB에 저장하여 데이터 확장성 확보
- **Traceability**: 모든 AI 추론 이력을 로그화하여 모델 성능 분석 및 데이터 기반 의사결정 가능
- **User Experience**: '넛지(Nudge)' 로직을 통한 클린한 커뮤니티 환경 조성



---

### Team 

| Member | Role |
| :--- | :--- |
| Youn Jae | Dev Lead / Project Management |
| Kenzie | ML Modeling / Pseudo-labeling |
| Yongju | Backend API / DB Design |
| Yuri | Frontend Development |
| Junsang | Image Data Collection & Preprocessing · Image Analysis Pipeline (Custom Vision) |
| Juhee | Text Data Preprocessing |

---

### 🙋 My Part · 오준상

Microsoft AI School에서 처음 진행한 팀 프로젝트입니다. 악성 댓글이 올라간 뒤에 지우는 방식 대신, 글을 올리기 직전에 작성자가 스스로 고쳐 쓰도록 돕는 방향을 잡았습니다. 그중 저는 개입이 언제, 어떤 화면으로 나타날지를 정하는 설계와 이미지 판정 영역을 맡았습니다.

- **개입 시점 UX**: 경계가 모호한 표현은 곧장 게시를 막지 않고, 경고 창을 띄워 ‘수정’과 ‘확인’ 중 하나를 직접 고르게 했습니다. 경고를 넘기고 그대로 올리면 관리자에게 기록된다는 점도 같은 창에서 미리 안내했습니다.
- **이미지 데이터와 판정 기준**: 부적절한 이미지 데이터를 직접 수집하고 전처리해 Azure Custom Vision 분류 모델의 학습 데이터로 구성했습니다. 문제없는 이미지가 잘못 막히는 일을 줄이려고, 유해 확률이 0.9 이상일 때만 차단하도록 기준을 높게 잡았습니다.
- **모델 테스트**: Custom Vision과 함께 다른 딥러닝 분류 모델도 직접 테스트하며 판정 결과를 비교했습니다.
- **책임 있는 AI 기준**: 경고와 차단 화면마다 ‘AI 판정은 틀릴 수 있다’는 안내 문구를 함께 적어 두었습니다.