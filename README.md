# CKP Token Burn Snapshot Calculator
A blockchain data analysis tool for calculating CKP token burn compensation on Binance Smart Chain.

## 📊 Project Overview

This tool tracks CKP token burns through a specific smart contract and creates snapshots for user compensation calculations.

**Snapshot Date:** January 15, 2026, 12:00 PM UTC+8

## 🔧 Technical Stack

- **Blockchain:** Binance Smart Chain (BSC)
- **Query Platform:** Dune Analytics
- **Data Processing:** Google Sheets
- **Languages:** SQL

## 📈 Key Metrics

- Burn Contract: `0x7ae3f2672486adA489E41E8A23bBa414Ee7dB4c7`
- Token Contract: `0x2B5D9ADea07B590b638FFc165792b2C610EdA649`
- Burn Destination: `0x0000000000000000000000000000000000000000`

## 🔍 How It Works

1. Query BSC blockchain data via Dune Analytics
2. Track transactions to the burn contract
3. Filter token transfers to zero address
4. Calculate total burned amount per address
5. Create snapshot at specified timestamp

## 💻 SQL Query

The core query tracks burn transactions and calculates totals:
```sql
WITH burn_transactions AS (
    SELECT DISTINCT
        tx.hash as tx_hash,
        tx."from" as burner_address,
        tx.block_time
    FROM bnb.transactions tx
    WHERE tx."to" = 0x7ae3f2672486adA489E41E8A23bBa414Ee7dB4c7
        AND tx.block_time < TIMESTAMP '2026-01-15 04:00:00'
),
ckp_burns AS (
    SELECT 
        bt.burner_address,
        SUM(CAST(t.value AS DECIMAL(38,0))) as total_burned_amount
    FROM burn_transactions bt
    INNER JOIN erc20_bnb.evt_Transfer t
        ON bt.tx_hash = t.evt_tx_hash
    WHERE t.contract_address = 0x2B5D9ADea07B590b638FFc165792b2C610EdA649
        AND t."to" = 0x0000000000000000000000000000000000000000
    GROUP BY bt.burner_address
)
SELECT 
    burner_address,
    total_burned_amount
FROM ckp_burns
ORDER BY total_burned_amount DESC
```

## 🚧 Challenges Solved

### 1. Address Format Normalization
**Problem:** Addresses from different sources had inconsistent formatting

**Solution:** Applied `TRIM()` and `LOWER()` functions to standardize address formats

### 2. Large Number Precision
**Problem:** Token amounts in wei (10^18) required precise calculation

**Solution:** Used `DECIMAL(38,0)` data type to maintain full precision

### 3. Cross-Platform Data Matching
**Problem:** Matching addresses between Dune and Google Sheets failed

**Solution:** Implemented data cleaning pipeline with format standardization

## 📚 Skills Demonstrated

- Blockchain data analysis
- SQL query optimization
- ERC-20 token standard understanding
- Data normalization techniques
- Problem-solving in production environment

## 🎯 Future Improvements

- [ ] Automate data export with Dune API
- [ ] Build Python script for data processing
- [ ] Create web dashboard for real-time tracking
- [ ] Add visualization charts

## 📧 Contact

For questions or collaboration: [你的邮箱]

---

**Built with:** Dune Analytics • BSC • SQL • Google Sheets
```

---

## ✅ 完整操作步骤（跟着做）

**第1步：打开浏览器**
- 访问 [github.com](https://github.com)

**第2步：创建账号/登录**
- 如果没账号：点 "Sign up" → 填写邮箱、密码、用户名
- 如果有账号：点 "Sign in" 登录

**第3步：创建仓库**
- 点右上角 `+` 号
- 点 "New repository"
- 填写：
```
  Repository name: ckp-burn-snapshot-calculator
  Description: Blockchain data analysis for CKP token burns on BSC
