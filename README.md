# Hoteling — 호텔 예약 취소 예측 & 오버부킹 관리

호텔 예약 데이터를 기반으로 **예약 취소 확률을 예측**하고, 그 예측을 활용해
**오버부킹 의사결정**을 돕는 웹 애플리케이션입니다. 예약별 위험도 예측,
오버부킹 요약, AI 인사이트(연관 요인·추천 시나리오), 예약 조치(action) 기록·리포트 기능을 제공합니다.

프론트엔드(React + Vite)와 백엔드(FastAPI + ML)를 하나의 저장소로 관리합니다.

## 주요 기능

- **예약 취소 예측(Prediction)** — 예약별 취소 확률을 ML 모델로 예측
- **오버부킹 관리(Overbooking)** — 예측 기반 오버부킹 요약 및 의사결정 지원
- **예약 조치(Reservation Action)** — 예약에 대한 조치 등록·조회·삭제, 리포트/내보내기
- **AI 인사이트(AI Insight)** — OpenAI 기반 취소 연관 요인 및 추천 시나리오 생성
- **기준 정보 관리** — 호텔 / 고객 / 객실 타입 / 예약 데이터 조회
- **모델 정보(Model Info)** — 사용 중인 예측 모델 메타 정보 제공

## 기술 스택

| 구분 | 사용 기술 |
|------|-----------|
| 프론트엔드 | React 19, Vite 5, React Router 7, Tailwind CSS 3, Axios, react-icons |
| 백엔드 | FastAPI, Uvicorn, asyncpg (PostgreSQL), Pydantic |
| ML / AI | TensorFlow 2.8, NumPy, OpenAI API (gpt-4o) |
| 배포 | Render (`render.yaml`) |

## 프로젝트 구조

```
Hoteling/
├── front/
│   └── hotel_f/              # React + Vite 앱
│       ├── index.html
│       ├── vite.config.js
│       ├── tailwind.config.cjs
│       ├── render.yaml       # 프론트 배포 설정
│       ├── public/
│       └── src/              # 컴포넌트 · 페이지 · API 클라이언트
│
└── back/                     # FastAPI + ML API
    ├── main.py               # 앱 · CORS · 라우터 등록
    ├── requirements.txt
    ├── runtime.txt
    ├── .env.example          # 환경 변수 템플릿
    ├── router/               # 기능별 API 라우터
    ├── db/                   # dbpool, schema.sql, 마이그레이션
    ├── ml/                   # features, predictor (예측 로직)
    ├── ml_model/             # 학습된 모델 파일
    ├── ai/                   # insight.py (OpenAI 인사이트)
    └── settings/Settings.py  # 환경설정(.env 로드)
```

## 시작하기

### 1. 저장소 클론

```bash
git clone https://github.com/seoyoon1209/Hoteling.git
cd Hoteling
```

### 2. 백엔드 실행

```bash
cd back
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# 환경 변수 설정
cp .env.example .env          # 값 채우기 (아래 표 참고)

uvicorn main:app --reload     # http://localhost:8000, API 문서: /docs
```

### 3. 프론트엔드 실행

```bash
cd front/hotel_f
npm install

# 백엔드 주소 설정
echo "VITE_API_BASE=http://localhost:8000" > .env

npm run dev                   # http://localhost:5173
```

## 환경 변수

### 백엔드 (`back/.env`)

`back/.env.example`를 복사해 만드세요. 실제 `.env`는 `.gitignore`로 커밋되지 않습니다.

| 변수 | 설명 |
|------|------|
| `DB_HOST` | 데이터베이스 호스트 |
| `DB_PORT` | 데이터베이스 포트 |
| `DB_SERVICE_NAME` | 데이터베이스(서비스) 이름 |
| `DB_USER` | DB 사용자 |
| `DB_PASSWORD` | DB 비밀번호 |
| `OPENAI_API_KEY` | OpenAI API 키 (AI 인사이트 기능) |
| `OPENAI_MODEL` | 사용할 모델 (기본 `gpt-4o`) |

### 프론트엔드 (`front/hotel_f/.env`)

| 변수 | 설명 | 예시 |
|------|------|------|
| `VITE_API_BASE` | 백엔드 API 주소 | `http://localhost:8000` |

### 데이터베이스 초기화

```bash
psql -f back/db/schema.sql
psql -f back/db/migration_002_reservation_action.sql   # 마이그레이션(필요 시)
```

## API 개요

모든 엔드포인트는 `/api` 프리픽스를 사용합니다. (예: `/api/reservations`)

| 도메인 | 프리픽스 |
|--------|----------|
| 호텔 | `/hotels` |
| 고객 | `/customers` |
| 객실 타입 | `/room-types` |
| 예약 | `/reservations`, `/reservations/{id}` |
| 예측 | `/reservations/{id}/predictions` |
| AI 인사이트 | `/reservations/{id}/ai-insight` |
| 예약 조치 | `/reservations/{id}/actions` |
| 조치 리포트 | `/actions/report`, `/actions/export` |
| 오버부킹 | `/overbooking/summary` |
| 모델 정보 | `/model-info` |

> 전체 스펙은 백엔드 실행 후 `/docs` (Swagger UI)에서 확인할 수 있습니다.

## 참고

본 프로젝트의 예측 결과는 참고용 정보이며, 실제 오버부킹·예약 운영 의사결정 시
운영 정책과 함께 종합적으로 판단하시기 바랍니다.
