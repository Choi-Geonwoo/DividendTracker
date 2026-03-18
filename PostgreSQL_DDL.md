
```SQL
-- =========================
-- 1. country
-- =========================
CREATE TABLE country (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    code VARCHAR(10) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- =========================
-- 2. financial_institution
-- =========================
CREATE TABLE financial_institution (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    type VARCHAR(20) NOT NULL, -- BANK / SECURITIES
    country_id BIGINT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_institution_country
        FOREIGN KEY (country_id)
        REFERENCES country(id)
);

-- =========================
-- 3. account
-- =========================
CREATE TABLE account (
    id BIGSERIAL PRIMARY KEY,
    institution_id BIGINT NOT NULL,
    account_number VARCHAR(50) NOT NULL UNIQUE,
    currency VARCHAR(10) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_account_institution
        FOREIGN KEY (institution_id)
        REFERENCES financial_institution(id)
);

-- =========================
-- 4. stock
-- =========================
CREATE TABLE stock (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    ticker VARCHAR(20) NOT NULL UNIQUE,
    country_id BIGINT NOT NULL,
    currency VARCHAR(10) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_stock_country
        FOREIGN KEY (country_id)
        REFERENCES country(id)
);

-- =========================
-- 5. stock_trade
-- =========================
CREATE TABLE stock_trade (
    id BIGSERIAL PRIMARY KEY,
    stock_id BIGINT NOT NULL,
    account_id BIGINT NOT NULL,
    trade_type VARCHAR(10) NOT NULL, -- BUY / SELL
    quantity NUMERIC(18,4) NOT NULL,
    price NUMERIC(18,4) NOT NULL,
    trade_date TIMESTAMP NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_trade_stock
        FOREIGN KEY (stock_id)
        REFERENCES stock(id),

    CONSTRAINT fk_trade_account
        FOREIGN KEY (account_id)
        REFERENCES account(id)
);

-- =========================
-- 6. dividend
-- =========================
CREATE TABLE dividend (
    id BIGSERIAL PRIMARY KEY,
    stock_id BIGINT NOT NULL,
    dividend_per_share NUMERIC(18,4) NOT NULL,
    dividend_date DATE NOT NULL,
    payment_date DATE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_dividend_stock
        FOREIGN KEY (stock_id)
        REFERENCES stock(id)
);

-- =========================
-- 7. dividend_history
-- =========================
CREATE TABLE dividend_history (
    id BIGSERIAL PRIMARY KEY,
    account_id BIGINT NOT NULL,
    stock_id BIGINT NOT NULL,
    dividend_id BIGINT NOT NULL,
    quantity NUMERIC(18,4) NOT NULL,
    amount NUMERIC(18,4) NOT NULL,
    received_date DATE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_div_history_account
        FOREIGN KEY (account_id)
        REFERENCES account(id),

    CONSTRAINT fk_div_history_stock
        FOREIGN KEY (stock_id)
        REFERENCES stock(id),

    CONSTRAINT fk_div_history_dividend
        FOREIGN KEY (dividend_id)
        REFERENCES dividend(id)
);

-- =========================
-- INDEX
-- =========================
CREATE INDEX idx_trade_stock ON stock_trade(stock_id);
CREATE INDEX idx_trade_account ON stock_trade(account_id);
CREATE INDEX idx_trade_date ON stock_trade(trade_date);

CREATE INDEX idx_dividend_stock ON dividend(stock_id);

CREATE INDEX idx_div_history_account ON dividend_history(account_id);
CREATE INDEX idx_div_history_stock ON dividend_history(stock_id);
```


🔥 추가로 넣으면 좋은 제약조건 (선택)
✔ 거래 타입 제한
```sql
ALTER TABLE stock_trade
ADD CONSTRAINT chk_trade_type
CHECK (trade_type IN ('BUY', 'SELL'));
```

✔ 금융기관 타입 제한
```sql
ALTER TABLE financial_institution
ADD CONSTRAINT chk_institution_type
CHECK (type IN ('BANK', 'SECURITIES'));
```

🚀 설계 퀄리티 한 단계 올리는 팁
1️⃣ ON DELETE 정책 (선택)
```sql
ON DELETE RESTRICT   -- 기본 (추천)
ON DELETE CASCADE    -- 자동 삭제 (주의)
```
👉 금융 데이터는 보통 RESTRICT 유지 추천

2️⃣ 소수점 정밀도
```sql
NUMERIC(18,4)
```
👉 돈/주식 → 안전한 선택
