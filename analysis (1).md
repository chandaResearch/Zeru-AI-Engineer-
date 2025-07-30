# Wallet Risk Scoring - Compound V2 Protocol

## Objective
Assign a risk score (0–1000) to each wallet based on historical borrow, repay, and liquidation data fetched via The Graph for Compound V2 protocol.

---

## 1. Data Collection

- **Source:** The Graph Protocol (`https://api.thegraph.com/subgraphs/name/graphprotocol/compound-v2`)
- **Input:** 103 wallet addresses
- **Fetched Events:** Borrow, Repay, and Liquidation events
- **Features Extracted:**
  - Total borrowed amount
  - Total repaid amount
  - Number of borrow transactions
  - Number of repay transactions
  - Number of times liquidated
  - Active days (based on first to last interaction)

---

## 2. Feature Engineering

Each wallet was processed to compute:

- `repayment_ratio = total_repaid / total_borrowed` (default = 1 for inactive)
- `active_days = difference between first and last event timestamp`
- Missing activity → All values default to 0

---

## 3. Scoring Logic

We computed the final score using the following formula:

```python
score = 1000 - (liquidations * 50 + (1 - repayment_ratio) * 300) + active_days_bonus
