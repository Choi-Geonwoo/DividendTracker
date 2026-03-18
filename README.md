# 📊 Investment Portfolio Database Design (Final)

## 🎯 프로젝트 개요
개인 투자 관리 시스템
- 국가 정보
- 금융기관 (은행/증권사)
- 계좌
- 주식 정보
- 주식 거래 내역
- 배당
- 배당 수령 내역
- 배당 이미지 첨부

---

## 🧱 테이블 설계

### 1. country
| 컬럼명 | 타입 | PK | FK | 설명 |
|--------|------|----|----|------|
| id | BIGSERIAL | ✅ | ❌ | 기본 키 |
| name | VARCHAR(100) | ❌ | ❌ | 국가명 |
| code | VARCHAR(10) | ❌ | ❌ | ISO 코드 |

---

### 2. financial_institution
| 컬럼명 | 타입 | PK | FK | 설명 |
|--------|------|----|----|------|
| id | BIGSERIAL | ✅ | ❌ | 기본 키 |
| name | VARCHAR(100) | ❌ | ❌ | 기관명 |
| type | VARCHAR(20) | ❌ | ❌ | BANK / SECURITIES |
| country_id | BIGINT | ❌ | ✅ | 국가 |

---

### 3. account
| 컬럼명 | 타입 | PK | FK | 설명 |
|--------|------|----|----|------|
| id | BIGSERIAL | ✅ | ❌ | 기본 키 |
| institution_id | BIGINT | ❌ | ✅ | 금융기관 |
| account_number | VARCHAR(50) | ❌ | ❌ | 계좌번호 |
| currency | VARCHAR(10) | ❌ | ❌ | 통화 |

---

### 4. stock
| 컬럼명 | 타입 | PK | FK | 설명 |
|--------|------|----|----|------|
| id | BIGSERIAL | ✅ | ❌ | 기본 키 |
| name | VARCHAR(100) | ❌ | ❌ | 종목명 |
| ticker | VARCHAR(20) | ❌ | ❌ | 종목 코드 |
| country_id | BIGINT | ❌ | ✅ | 국가 |
| currency | VARCHAR(10) | ❌ | ❌ | 통화 |

---

### 5. stock_trade
| 컬럼명 | 타입 | PK | FK | 설명 |
|--------|------|----|----|------|
| id | BIGSERIAL | ✅ | ❌ | 기본 키 |
| stock_id | BIGINT | ❌ | ✅ | 주식 |
| account_id | BIGINT | ❌ | ✅ | 계좌 |
| trade_type | VARCHAR(10) | ❌ | ❌ | BUY / SELL |
| quantity | NUMERIC | ❌ | ❌ | 수량 |
| price | NUMERIC | ❌ | ❌ | 가격 |
| trade_date | TIMESTAMP | ❌ | ❌ | 거래일 |

---

### 6. dividend
| 컬럼명 | 타입 | PK | FK | 설명 |
|--------|------|----|----|------|
| id | BIGSERIAL | ✅ | ❌ | 기본 키 |
| stock_id | BIGINT | ❌ | ✅ | 주식 |
| dividend_per_share | NUMERIC | ❌ | ❌ | 주당 배당 |
| dividend_date | DATE | ❌ | ❌ | 기준일 |
| payment_date | DATE | ❌ | ❌ | 지급일 |

---

### 7. dividend_history
| 컬럼명 | 타입 | PK | FK | 설명 |
|--------|------|----|----|------|
| id | BIGSERIAL | ✅ | ❌ | 기본 키 |
| account_id | BIGINT | ❌ | ✅ | 계좌 |
| stock_id | BIGINT | ❌ | ✅ | 주식 |
| dividend_id | BIGINT | ❌ | ✅ | 배당 |
| quantity | NUMERIC | ❌ | ❌ | 보유 수량 |
| amount | NUMERIC | ❌ | ❌ | 수령 금액 |
| received_date | DATE | ❌ | ❌ | 수령일 |

---

### 8. dividend_attachment
| 컬럼명 | 타입 | PK | FK | 설명 |
|--------|------|----|----|------|
| id | BIGSERIAL | ✅ | ❌ | 기본 키 |
| dividend_history_id | BIGINT | ❌ | ✅ | 배당 수령 |
| file_name | VARCHAR(255) | ❌ | ❌ | 파일명 |
| file_path | VARCHAR(500) | ❌ | ❌ | 저장 경로 |
| file_size | BIGINT | ❌ | ❌ | 파일 크기 |
| file_type | VARCHAR(50) | ❌ | ❌ | MIME 타입 |
| created_at | TIMESTAMP | ❌ | ❌ | 생성일 |

---

## 🔗 관계 구조
```
country
 ├── financial_institution
 │      └── account
 │            ├── stock_trade
 │            └── dividend_history
 │                    └── dividend_attachment
 │
 └── stock
        ├── stock_trade
        └── dividend
              └── dividend_history
```

---

## 🎯 설계 포인트

### 1. 금융기관 추상화
- 은행 + 증권사 통합 관리

### 2. 거래 중심 설계
- 모든 데이터는 거래 기반

### 3. 배당 구조 분리
- dividend: 배당 정책
- dividend_history: 수령 내역

### 4. 이미지 분리
- dividend_attachment로 관리 (1:N 구조)

---

## 🚀 한 줄 정리
> 금융기관 → 계좌 → 거래/배당 → 이미지까지 포함한 실무형 DB 설계

