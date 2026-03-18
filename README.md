# 📊 Investment Portfolio Database Design


### 프로젝트 명 : DividendTracker
## 🎯 프로젝트 개요
개인 투자 관리 시스템
- 국가 정보
- 주식 정보
- 주식 거래 내역
- 계좌 및 금융기관
- 배당 및 배당 수령 내역

---

## 🧱 아키텍처
- Backend: Java, Spring Boot
- Database: PostgreSQL
- Version Control: Git

---

## 🗂️ 테이블 설계

### 1. country
| 컬럼명 | 타입 | PK | FK | UNIQUE | 설명 |
|--------|------|----|----|--------|------|
| id | BIGSERIAL | ✅ | ❌ | ❌ | 기본 키 |
| name | VARCHAR(100) | ❌ | ❌ | ❌ | 국가명 |
| code | VARCHAR(10) | ❌ | ❌ | ✅ | ISO 코드 |

---

### 2. financial_institution
| 컬럼명 | 타입 | PK | FK | UNIQUE | 설명 |
|--------|------|----|----|--------|------|
| id | BIGSERIAL | ✅ | ❌ | ❌ | 기본 키 |
| name | VARCHAR(100) | ❌ | ❌ | ❌ | 기관명 |
| type | VARCHAR(20) | ❌ | ❌ | ❌ | BANK / SECURITIES |
| country_id | BIGINT | ❌ | ✅ | ❌ | 국가 ID |

---

### 3. account
| 컬럼명 | 타입 | PK | FK | UNIQUE | 설명 |
|--------|------|----|----|--------|------|
| id | BIGSERIAL | ✅ | ❌ | ❌ | 기본 키 |
| institution_id | BIGINT | ❌ | ✅ | ❌ | 금융기관 ID |
| account_number | VARCHAR(50) | ❌ | ❌ | ✅ | 계좌번호 |
| currency | VARCHAR(10) | ❌ | ❌ | ❌ | 통화 |

---

### 4. stock
| 컬럼명 | 타입 | PK | FK | UNIQUE | 설명 |
|--------|------|----|----|--------|------|
| id | BIGSERIAL | ✅ | ❌ | ❌ | 기본 키 |
| name | VARCHAR(100) | ❌ | ❌ | ❌ | 종목명 |
| ticker | VARCHAR(20) | ❌ | ❌ | ✅ | 종목 코드 |
| country_id | BIGINT | ❌ | ✅ | ❌ | 국가 ID |
| currency | VARCHAR(10) | ❌ | ❌ | ❌ | 통화 |

---

### 5. stock_trade
| 컬럼명 | 타입 | PK | FK | UNIQUE | 설명 |
|--------|------|----|----|--------|------|
| id | BIGSERIAL | ✅ | ❌ | ❌ | 기본 키 |
| stock_id | BIGINT | ❌ | ✅ | ❌ | 주식 ID |
| account_id | BIGINT | ❌ | ✅ | ❌ | 계좌 ID |
| trade_type | VARCHAR(10) | ❌ | ❌ | ❌ | BUY / SELL |
| quantity | NUMERIC | ❌ | ❌ | ❌ | 수량 |
| price | NUMERIC | ❌ | ❌ | ❌ | 가격 |
| trade_date | TIMESTAMP | ❌ | ❌ | ❌ | 거래일 |

---

### 6. dividend
| 컬럼명 | 타입 | PK | FK | UNIQUE | 설명 |
|--------|------|----|----|--------|------|
| id | BIGSERIAL | ✅ | ❌ | ❌ | 기본 키 |
| stock_id | BIGINT | ❌ | ✅ | ❌ | 주식 ID |
| dividend_per_share | NUMERIC | ❌ | ❌ | ❌ | 주당 배당금 |
| dividend_date | DATE | ❌ | ❌ | ❌ | 기준일 |
| payment_date | DATE | ❌ | ❌ | ❌ | 지급일 |

---

### 7. dividend_history
| 컬럼명 | 타입 | PK | FK | UNIQUE | 설명 |
|--------|------|----|----|--------|------|
| id | BIGSERIAL | ✅ | ❌ | ❌ | 기본 키 |
| account_id | BIGINT | ❌ | ✅ | ❌ | 계좌 ID |
| stock_id | BIGINT | ❌ | ✅ | ❌ | 주식 ID |
| dividend_id | BIGINT | ❌ | ✅ | ❌ | 배당 ID |
| quantity | NUMERIC | ❌ | ❌ | ❌ | 보유 수량 |
| amount | NUMERIC | ❌ | ❌ | ❌ | 수령 금액 |
| received_date | DATE | ❌ | ❌ | ❌ | 수령일 |

---

## 🔗 관계 구조
```
country
 ├── financial_institution
 │      └── account
 │            ├── stock_trade
 │            └── dividend_history
 │
 └── stock
        ├── stock_trade
        └── dividend
              └── dividend_history
```

---

## 🔑 설계 원칙

### 1. 정규화
- 중복 데이터 제거
- FK로 관계 유지

### 2. 거래 중심 설계
- 모든 계산은 거래 기반

### 3. 확장성
- 금융기관 추상화
- 다중 계좌 지원

### 4. 금융 데이터 특징
- 삭제 최소화
- 이력 중심 관리

---

## ⚙️ 인덱스 추천
```sql
CREATE INDEX idx_trade_stock ON stock_trade(stock_id);
CREATE INDEX idx_trade_account ON stock_trade(account_id);
CREATE INDEX idx_dividend_stock ON dividend(stock_id);
CREATE INDEX idx_div_history_account ON dividend_history(account_id);
```

---
