# Design Document

## Overview

Midas Quant 프로젝트의 기반 구축을 위한 설계 문서입니다. 이 설계는 Docker 기반의 마이크로서비스 아키텍처를 채택하여 확장성과 유지보수성을 보장하며, 현대적인 웹 개발 스택을 활용합니다.

## Architecture

### System Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        🌍 글로벌 데이터 수집 레이어                           │
├─────────────────────────────────────────────────────────────────────────────┤
│  📊 거시경제     📈 TradingView    📝 투자블로그    📺 YouTube    📰 뉴스     │
│  (Fed, ECB,     (전략, 지표)     (전문가 의견)   (시장분석)    (감성분석)    │
│   PBOC 등)                                                                  │
└─────────────────────────────────────────────────────────────────────────────┘
                                    ↓ 실시간 크롤링 & API
┌─────────────────────────────────────────────────────────────────────────────┐
│                         🧠 AI 분석 & 전략 엔진 (맥미니 M4)                   │
├─────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │ 전략 크롤러  │  │  AI 분석기  │  │ 백테스팅    │  │   투자 날씨 예보     │  │
│  │             │  │             │  │   엔진      │  │                     │  │
│  │• 실시간 수집 │  │• 성과 분석  │  │• 전략 검증  │  │• 시장 상황 분석     │  │
│  │• 전략 파싱  │  │• 패턴 인식  │  │• 리스크 측정│  │• 섹터 전망         │  │
│  │• 데이터 정제 │  │• 예측 모델  │  │• 최적화    │  │• 투자 기회 발굴     │  │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────────────┘  │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                    📊 통합 데이터베이스                              │    │
│  │  PostgreSQL (전략DB) + Redis (캐시) + InfluxDB (시계열)            │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
                                    ↓ HTTP API
┌─────────────────────────────────────────────────────────────────────────────┐
│                      💻 스마트 트레이딩 인터페이스 (윈도우 PC)                │
├─────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │ 투자 날씨   │  │ AI 어드바이저│  │ 실시간 거래 │  │   성과 분석         │  │
│  │   대시보드   │  │             │  │             │  │                     │  │
│  │• 시장 전망  │  │• 전략 추천  │  │• 키움증권   │  │• 수익률 분석        │  │
│  │• 섹터 분석  │  │• 리스크 경고│  │  API 연동   │  │• 벤치마크 비교      │  │
│  │• 투자 기회  │  │• 타이밍 알림│  │• 자동 실행  │  │• 개선 제안         │  │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Technology Stack

**Frontend:**
- React 18 with TypeScript for type safety
- Vite for fast development and building
- Ant Design + Tailwind CSS for UI components
- Zustand for state management
- React Router for client-side routing
- Axios for HTTP client with interceptors

**Backend:**
- FastAPI with Python 3.11 for high-performance API
- SQLAlchemy 2.0 with async support
- Alembic for database migrations
- Pydantic V2 for data validation
- JWT for authentication
- Structured logging with Python logging

**Database:**
- PostgreSQL 15 as primary database
- Redis 7 for caching and session storage
- InfluxDB 2.0 for time-series data (future use)

**AI & 머신러닝:**
- OpenAI GPT for natural language processing
- Hugging Face Transformers for sentiment analysis
- Prophet & LSTM for time series forecasting
- Scikit-learn for factor analysis
- Stable Baselines3 for reinforcement learning

**데이터 수집 & 크롤링:**
- Scrapy for web scraping
- BeautifulSoup for HTML parsing
- Selenium for dynamic content
- YouTube API for video analysis
- News API for real-time news

**DevOps:**
- Docker & Docker Compose for containerization
- GitHub Actions for CI/CD
- Pre-commit hooks for code quality

### 실시간 매매 엔진 설계

#### 1. **실시간 데이터 수신 아키텍처**
```python
# 키움증권 실시간 데이터 수신 시스템
class RealTimeEngine:
    def __init__(self):
        self.kiwoom_api = KiwoomAPI()
        self.filter_engine = TieredFilterEngine()
        self.signal_processor = SignalProcessor()
        self.order_recommender = OrderRecommender()
    
    async def start_monitoring(self):
        """전 종목 실시간 감시 시작"""
        # 1. 전 종목 실시간 등록
        await self.register_all_stocks()
        
        # 2. 실시간 이벤트 핸들러 등록
        self.kiwoom_api.OnReceiveRealData.connect(self.on_real_data)
        
    async def on_real_data(self, stock_code, real_data):
        """실시간 데이터 수신 시 처리"""
        # 1차 경량 필터링
        if await self.filter_engine.light_filter(stock_code, real_data):
            # 2차 중량 필터링
            if await self.filter_engine.heavy_filter(stock_code):
                # 매수 신호 생성
                signal = await self.signal_processor.generate_signal(stock_code)
                # 매수 전략 추천
                recommendations = await self.order_recommender.get_recommendations(signal)
                # 사용자에게 알림
                await self.notify_user(signal, recommendations)
```

#### 2. **단계적 필터링 시스템**
```python
class TieredFilterEngine:
    async def light_filter(self, stock_code, real_data):
        """1차 경량 필터링 - 빠른 계산"""
        current_price = real_data['current_price']
        volume = real_data['volume']
        
        # 조건 1: 거래량 급증 (전일 대비 50% 이상)
        if volume < self.get_volume_threshold(stock_code):
            return False
            
        # 조건 2: 가격 상승률 (시가 대비 +5% 이상)
        if self.get_price_change_rate(stock_code) < 0.05:
            return False
            
        # 조건 3: 거래대금 (5분간 10억 이상)
        if self.get_trading_value_5min(stock_code) < 1000000000:
            return False
            
        return True
    
    async def heavy_filter(self, stock_code):
        """2차 중량 필터링 - 복잡한 분석"""
        # 과거 데이터 로드 (무거운 작업)
        historical_data = await self.load_historical_data(stock_code)
        
        # 기술적 분석
        if not self.check_breakout_pattern(historical_data):
            return False
            
        # 224일선 관계 분석
        if not self.check_moving_average_224(historical_data):
            return False
            
        # 기관 수급 분석
        if not self.check_institutional_flow(stock_code):
            return False
            
        return True
```

#### 3. **매수 전략 추천 시스템**
```python
class OrderRecommender:
    async def get_recommendations(self, signal):
        """매수 전략 추천 생성"""
        stock_code = signal.stock_code
        current_price = signal.current_price
        
        recommendations = []
        
        # 전략 A: 눌림목 매수 (안정 지향)
        pullback_price = await self.calculate_pullback_price(stock_code)
        recommendations.append({
            'strategy': 'pullback_buy',
            'name': '눌림목 매수 (안정 지향)',
            'price': pullback_price,
            'order_type': 'limit',
            'probability': self.calculate_execution_probability(pullback_price, current_price),
            'description': f'5분봉 5이평선 {pullback_price:,}원에 지정가 매수'
        })
        
        # 전략 B: 돌파 확인 매수 (확신 지향)
        breakout_price = await self.calculate_breakout_price(stock_code)
        recommendations.append({
            'strategy': 'breakout_buy',
            'name': '돌파 확인 매수 (확신 지향)',
            'price': breakout_price,
            'order_type': 'market_on_breakout',
            'probability': self.calculate_execution_probability(breakout_price, current_price),
            'description': f'당일 고점 {breakout_price:,}원 돌파 시 시장가 매수'
        })
        
        return recommendations
```

## Components and Interfaces

### Backend Components

#### 1. Core Configuration Module
```python
# app/core/config.py
class Settings(BaseSettings):
    app_name: str = "Midas Quant API"
    debug: bool = False
    database_url: str
    redis_url: str
    secret_key: str
    algorithm: str = "HS256"
    access_token_expire_minutes: int = 30
    
    class Config:
        env_file = ".env"
```

#### 2. Database Models
```python
# app/models/user.py
class User(Base):
    __tablename__ = "users"
    
    id = Column(Integer, primary_key=True, index=True)
    username = Column(String, unique=True, index=True, nullable=False)
    email = Column(String, unique=True, index=True, nullable=False)
    hashed_password = Column(String, nullable=False)
    is_active = Column(Boolean, default=True)
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)

# app/models/stock.py
class Stock(Base):
    __tablename__ = "stocks"
    
    code = Column(String, primary_key=True, index=True)
    name = Column(String, nullable=False)
    market = Column(String, nullable=False)  # KOSPI, KOSDAQ
    sector = Column(String)
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)
```

#### 3. API Router Structure
```python
# app/api/v1/api.py
api_router = APIRouter()
api_router.include_router(auth.router, prefix="/auth", tags=["authentication"])
api_router.include_router(users.router, prefix="/users", tags=["users"])
api_router.include_router(stocks.router, prefix="/stocks", tags=["stocks"])
api_router.include_router(health.router, prefix="/health", tags=["health"])
```

#### 4. Authentication Service
```python
# app/services/auth.py
class AuthService:
    def __init__(self, db: AsyncSession):
        self.db = db
    
    async def authenticate_user(self, username: str, password: str) -> Optional[User]:
        user = await self.get_user_by_username(username)
        if not user or not self.verify_password(password, user.hashed_password):
            return None
        return user
    
    def create_access_token(self, data: dict) -> str:
        to_encode = data.copy()
        expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
        to_encode.update({"exp": expire})
        return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
```

### Frontend Components

#### 1. Application Structure
```typescript
// src/App.tsx
const App: React.FC = () => {
  return (
    <BrowserRouter>
      <ConfigProvider theme={antdTheme}>
        <Routes>
          <Route path="/login" element={<LoginPage />} />
          <Route path="/" element={<ProtectedRoute><DashboardLayout /></ProtectedRoute>}>
            <Route index element={<Dashboard />} />
            <Route path="stocks" element={<StocksPage />} />
            <Route path="portfolio" element={<PortfolioPage />} />
          </Route>
        </Routes>
      </ConfigProvider>
    </BrowserRouter>
  );
};
```

#### 2. Authentication Store
```typescript
// src/store/authStore.ts
interface AuthState {
  user: User | null;
  token: string | null;
  isAuthenticated: boolean;
  login: (credentials: LoginCredentials) => Promise<void>;
  logout: () => void;
  checkAuth: () => void;
}

export const useAuthStore = create<AuthState>((set, get) => ({
  user: null,
  token: localStorage.getItem('token'),
  isAuthenticated: false,
  
  login: async (credentials) => {
    const response = await authAPI.login(credentials);
    const { access_token, user } = response.data;
    
    localStorage.setItem('token', access_token);
    set({ user, token: access_token, isAuthenticated: true });
  },
  
  logout: () => {
    localStorage.removeItem('token');
    set({ user: null, token: null, isAuthenticated: false });
  },
  
  checkAuth: () => {
    const token = localStorage.getItem('token');
    if (token) {
      // Verify token validity
      set({ token, isAuthenticated: true });
    }
  }
}));
```

#### 3. API Service Layer
```typescript
// src/services/api.ts
class APIService {
  private client: AxiosInstance;
  
  constructor() {
    this.client = axios.create({
      baseURL: import.meta.env.VITE_API_URL,
      timeout: 10000,
    });
    
    this.setupInterceptors();
  }
  
  private setupInterceptors() {
    this.client.interceptors.request.use((config) => {
      const token = localStorage.getItem('token');
      if (token) {
        config.headers.Authorization = `Bearer ${token}`;
      }
      return config;
    });
    
    this.client.interceptors.response.use(
      (response) => response,
      (error) => {
        if (error.response?.status === 401) {
          useAuthStore.getState().logout();
        }
        return Promise.reject(error);
      }
    );
  }
}
```

## Database Schema

### User Management
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    hashed_password VARCHAR(255) NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Stock Data
```sql
CREATE TABLE stocks (
    code VARCHAR(10) PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    market VARCHAR(10) NOT NULL,
    sector VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Security Considerations

1. **Authentication & Authorization**
   - JWT tokens with expiration
   - Password hashing with bcrypt
   - Role-based access control (future)

2. **API Security**
   - CORS configuration
   - Rate limiting
   - Input validation with Pydantic
   - SQL injection prevention with SQLAlchemy

3. **Data Protection**
   - Environment variables for secrets
   - Database connection encryption
   - Secure session management

## Performance Optimization

1. **Database**
   - Connection pooling
   - Query optimization
   - Proper indexing strategy

2. **Caching**
   - Redis for session storage
   - API response caching
   - Static asset caching

3. **Frontend**
   - Code splitting with React.lazy
   - Bundle optimization with Vite
   - Image optimization

## Deployment Strategy

1. **Development Environment**
   - Docker Compose for local development
   - Hot reload for both frontend and backend
   - Separate databases for development and testing

2. **Production Environment**
   - Container orchestration (Docker Swarm or Kubernetes)
   - Load balancing
   - Health checks and monitoring
   - Automated backups

## Monitoring and Logging

1. **Application Monitoring**
   - Structured logging with Python logging
   - Error tracking and alerting
   - Performance metrics

2. **Infrastructure Monitoring**
   - Database performance monitoring
   - Server resource monitoring
   - Network monitoring

This design provides a solid foundation for the Midas Quant project, ensuring scalability, maintainability, and security while following modern development best practices.