# 🛡️ AI Community Nudge
**AI 기반 실시간 유해 콘텐츠 탐지 및 데이터 로깅 시스템**

Microsoft AI School 1차 팀 프로젝트 · 6인

---

### 🧭 Product Snapshot

커뮤니티 댓글의 유해 표현을 올라간 뒤에 지우는 대신, **쓰는 순간에 한 번 더 생각하게** 만드는 서비스입니다. 텍스트와 이미지를 함께 판정하고, 위험도에 따라 개입 강도를 나눴습니다.

| 판정 | 기준 (`main.py`) | 작성자에게 보이는 것 | 운영자에게 남는 것 |
| :--- | :--- | :--- | :--- |
| `none` | 텍스트 독성 점수 0.44 미만 | 그대로 게시 | 전체 댓글 모니터링 |
| `offensive` | 텍스트 독성 점수 0.44 이상 0.55 미만 | 경고 팝업에서 **수정** 또는 **확인(게시)** 선택 | 경고를 무시하고 게시한 댓글 목록 |
| `hate` | 텍스트 독성 점수 0.55 이상, 또는 이미지 유해 확률 0.9 이상 | 등록 차단 | 모델 추론 로그 |

독성 점수 = `offensive 확률 × 0.5 + hate 확률 × 1.0`

**설계 포인트**
- **막기 전에 고칠 기회부터**: 애매한 표현은 차단하지 않고 경고와 선택지를 줍니다. 경고를 무시하고 게시하면 관리자에게 기록된다는 사실도 팝업에서 미리 알립니다.
- **AI 판정의 한계 고지**: 경고·차단 팝업마다 "AI 판정 결과이며 오류가 있을 수 있다"는 문구를 붙였습니다.
- **이미지는 보수적으로**: 이미지는 유해 확률 0.9 이상일 때만 차단합니다.
- **운영 근거 남기기**: 모델 버전별 추론 시간과 경고 무시 댓글을 기록해, 이후 기준을 조정할 수 있는 데이터를 쌓습니다.
- **동의 기록**: 사용자의 개인정보 수집 동의 여부와 시각을 저장하는 API를 두었습니다.

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
| Junsang | Image Data Collection & Preprocessing |
| Juhee | Text Data Preprocessing |
