# 🏛️ Midas Quant

**Professional Quantitative Investment Platform for Korean Stock Market**

## 🎯 프로젝트 개요

Midas Quant는 대한민국 주식시장에 특화된 전문가용 퀀트 투자 전략 개발 및 자동매매 플랫폼입니다.

### 🌟 주요 특징

- **🇰🇷 한국 주식시장 특화**: KOSPI, KOSDAQ 완벽 지원
- **🔗 키움증권 API 연동**: 실시간 데이터 및 자동매매
- **📊 멀티-팩터 전략**: 가치, 모멘텀, 품질, 저변동성 팩터 조합
- **🔄 다이나믹 자산 배분**: 시장 상황에 따른 동적 포트폴리오 조정
- **⚡ 고성능 백테스팅**: 현실적 거래비용 반영
- **🌐 크로스 플랫폼**: 맥 서버 + 윈도우 클라이언트

## 🏗️ 시스템 아키텍처

```
┌─────────────────────────────────────────────────────────────┐
│                    맥미니 M4 (서버)                          │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────┐  │
│  │   백엔드 API    │  │   데이터베이스   │  │   NAS 기능   │  │
│  │ FastAPI + 도커  │  │ PostgreSQL      │  │ 파일 저장소  │  │
│  │ 키움증권 연동   │  │ Redis           │  │ 백업 시스템  │  │
│  │ 퀀트 엔진       │  │ InfluxDB        │  │             │  │
│  └─────────────────┘  └─────────────────┘  └─────────────┘  │
└─────────────────────────────────────────────────────────────┘
                              ↕ HTTP API
┌─────────────────────────────────────────────────────────────┐
│                   윈도우 PC (클라이언트)                      │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐                   │
│  │  프론트엔드     │  │  키움증권 API   │                   │
│  │ React + TS      │  │ OpenAPI+ 연동   │                   │
│  │ 웹 브라우저     │  │ 실시간 거래     │                   │
│  └─────────────────┘  └─────────────────┘                   │
└─────────────────────────────────────────────────────────────┘
```

## 🛠️ 기술 스택

### Backend
- **Framework**: FastAPI + Python 3.11
- **Database**: PostgreSQL 15, Redis 7, InfluxDB 2.0
- **ORM**: SQLAlchemy 2.0 (Async)
- **Authentication**: JWT + OAuth2
- **Containerization**: Docker + Docker Compose

### Frontend
- **Framework**: React 18 + TypeScript
- **Build Tool**: Vite
- **UI Library**: Tailwind CSS + Headless UI
- **State Management**: Zustand
- **Charts**: Recharts + D3.js

### Infrastructure
- **Server**: Mac Mini M4 (24/7 운영)
- **Client**: Windows PC (키움증권 API)
- **Deployment**: Docker Containers
- **Monitoring**: Prometheus + Grafana

## 🚀 빠른 시작

### 1. 저장소 클론
```bash
git clone https://github.com/[username]/midas-quant.git
cd midas-quant
```

### 2. 백엔드 실행 (맥미니)
```bash
# Docker Compose로 전체 스택 실행
docker-compose up -d

# 또는 개발 모드로 실행
cd backend
python -m uvicorn app.main:app --reload
```

### 3. 프론트엔드 실행 (윈도우 PC)
```bash
cd frontend
npm install
npm run dev
```

### 4. 키움증권 API 설정
- 키움증권 OpenAPI+ 설치 (Windows 전용)
- 계좌 정보 및 API 키 설정
- 모의투자로 테스트 후 실거래 전환

## 📋 주요 기능

### 🔬 백테스팅 엔진
- 고성능 벡터화 연산 (Pandas/NumPy)
- 현실적 거래비용 반영 (수수료, 세금, 슬리피지)
- 다양한 성과 지표 (샤프 비율, MDD, 칼마 비율)

### 📊 멀티-팩터 전략
- **가치 팩터**: PBR, PER, EV/EBITDA
- **모멘텀 팩터**: 가격 모멘텀, 어닝스 모멘텀
- **품질 팩터**: ROE, 부채비율, 이익품질
- **저위험 팩터**: 변동성, 베타

### 🔄 다이나믹 자산 배분
- 추세 추종 전략
- 변동성 타겟팅
- 리스크 패리티

### 🌐 글로벌 접근
- DDNS를 통한 원격 접근
- VPN 보안 연결
- 모바일 모니터링

## 📁 프로젝트 구조

```
midas-quant/
├── frontend/                    # React 프론트엔드
│   ├── src/
│   │   ├── components/         # UI 컴포넌트
│   │   ├── services/          # API 서비스
│   │   ├── store/             # 상태 관리
│   │   └── types/             # TypeScript 타입
│   └── package.json
├── backend/                     # Python 백엔드
│   ├── app/
│   │   ├── api/               # API 라우터
│   │   ├── core/              # 핵심 설정
│   │   ├── models/            # 데이터베이스 모델
│   │   ├── services/          # 비즈니스 로직
│   │   └── main.py           # FastAPI 앱
│   ├── engines/
│   │   ├── factors/           # 팩터 계산
│   │   ├── backtesting/       # 백테스팅
│   │   └── trading/           # 거래 실행
│   └── requirements.txt
├── database/
│   ├── migrations/            # DB 마이그레이션
│   └── schemas/               # 스키마 정의
├── docs/                       # 문서
├── scripts/                    # 유틸리티 스크립트
├── .kiro/                      # Kiro IDE 설정
│   └── specs/                 # 개발 스펙
└── docker-compose.yml
```

## 🔧 개발 환경 설정

### 필수 요구사항
- **서버**: macOS (Mac Mini M4 권장)
- **클라이언트**: Windows 10/11 (키움증권 API용)
- **Docker**: 최신 버전
- **Node.js**: 18+ (프론트엔드)
- **Python**: 3.11+ (백엔드)

### 환경 변수 설정
```bash
# backend/.env
DATABASE_URL=postgresql://user:password@localhost:5432/midas_quant
REDIS_URL=redis://localhost:6379
KIWOOM_API_ENABLED=true
KIWOOM_ACCOUNT_NUMBER=your_account

# frontend/.env
VITE_API_URL=http://localhost:8000
```

## 📈 투자 전략

### 멀티-팩터 모델
```python
# 팩터 가중치 (예시)
factor_weights = {
    'value': 0.25,      # 가치 팩터
    'momentum': 0.25,   # 모멘텀 팩터
    'quality': 0.25,    # 품질 팩터
    'low_risk': 0.25    # 저위험 팩터
}
```

### 리밸런싱 전략
- **주기**: 월 1회 (매월 첫째 주 금요일)
- **종목 수**: 20-50개 (포트폴리오 크기에 따라)
- **최대 비중**: 종목당 10% 제한
- **최소 비중**: 0.5% 이상

## 🔒 보안 및 리스크 관리

### 보안 기능
- JWT 기반 인증
- API 키 암호화 저장
- VPN 원격 접근
- 2FA 인증 지원

### 리스크 관리
- 포지션 사이즈 제한
- 손실 제한 (Stop Loss)
- 집중도 리스크 관리
- 실시간 모니터링

## 📊 성과 지표

### 백테스팅 결과 (예시)
- **연평균 수익률**: 15.2%
- **샤프 비율**: 1.34
- **최대 낙폭**: -12.8%
- **승률**: 68.5%

## 🤝 기여하기

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 라이선스

이 프로젝트는 MIT 라이선스 하에 배포됩니다. 자세한 내용은 [LICENSE](LICENSE) 파일을 참조하세요.

## ⚠️ 면책 조항

이 소프트웨어는 교육 및 연구 목적으로 제공됩니다. 실제 투자에 사용할 경우 발생하는 모든 손실에 대해 개발자는 책임지지 않습니다. 투자는 본인의 판단과 책임 하에 진행하시기 바랍니다.

## 📞 연락처

- **개발자**: Midas Touch
- **이메일**: [your-email@example.com]
- **GitHub**: [https://github.com/[username]/midas-quant]

---

**Made with ❤️ for Korean Stock Market Investors**