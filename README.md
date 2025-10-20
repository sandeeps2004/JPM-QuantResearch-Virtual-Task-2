\# 🧮 JP Morgan Quantitative Research – Task 2  
## Price a Commodity Storage Contract  

This repository contains my solution for **Task 2** of the **J.P. Morgan Quantitative Research Virtual Job Experience** on Forage.  
The objective is to develop a **valuation tool** that prices a **physical commodity storage contract** based on forecasted commodity prices.  

This exercise demonstrates quantitative modeling, event-driven simulation, and financial engineering techniques commonly used in **energy markets** and **commodities trading**.

---

## 📘 Abstract  

Commodity storage contracts are widely used by energy traders to capture **seasonal price spreads** — buying natural gas when prices are low (summer) and selling it when prices are high (winter).  

This project builds a **simulation-based valuation model** that calculates the **Net Present Value (NPV)** of a storage strategy while respecting real-world operational and physical constraints such as:  
- Injection and withdrawal rate limits  
- Maximum storage capacity  
- Daily/monthly storage and transaction costs  

The result is a **transparent event-driven simulation** that produces both the **final valuation** and a **detailed transaction log**, allowing traders and analysts to audit every cash flow.

---

## 📊 Problem Statement  

The client’s goal is to execute a **seasonal arbitrage strategy** using natural gas storage.  
We must determine whether the proposed trading schedule is profitable, and by how much.  

The model must:
1. Take all **contract parameters** as inputs (dates, volumes, costs).  
2. Calculate **cash flows** associated with purchases, sales, and costs.  
3. Enforce **capacity and rate constraints**.  
4. Output the **Net Present Value (NPV)** and **detailed event log**.

---

## 🧠 Quantitative Framework  

### 1. Cash Flow Definition  

For any time step *t*, the incremental cash flow is:
\[
CF_t = Q_t (P_t^{sell} - P_t^{buy}) - C_{storage,t} - C_{trans,t} - C_{oper,t}
\]

Where:
- \( Q_t \): quantity injected or withdrawn at time *t*  
- \( P_t^{buy}, P_t^{sell} \): forecasted buy/sell price at time *t*  
- \( C_{storage,t} \): storage cost for inventory  
- \( C_{trans,t}, C_{oper,t} \): transport and operational costs  

The **Net Present Value (NPV)** is the discounted sum:
\[
NPV = \sum_{t=1}^{T} \frac{CF_t}{(1+r)^{t/365}}
\]
where \( r \) is the annual discount rate (can be set to 0 for simplicity).

### 2. Arbitrage Intuition  

If the forward curve exhibits a **seasonal spread**, such that:
\[
\Delta P = P_{winter} - P_{summer} > 0
\]
then storing volume \( Q \) yields an arbitrage gain:
\[
Profit = Q (\Delta P - C_{storage} - C_{trans})
\]

The simulation quantifies this gain dynamically, handling non-linear constraints and real-world costs.

---

## 🧮 Model Design  

### Simulation Architecture  

1. **Input Layer**
   - Takes forecasted price data (`price_data`) and schedule inputs.  
   - Validates all date mappings and price availability.

2. **Event Processing Layer**
   - Iterates through all injection and withdrawal events chronologically.  
   - For each event, it:
     - Reads the price on that date  
     - Computes transaction cost or revenue  
     - Applies storage cost since last event  
     - Updates cumulative `cash_flow` and `inventory`

3. **Output Layer**
   - Returns final `cash_flow` and a detailed pandas `DataFrame` log of all events.  
   - Each log entry includes date, price, action type, volume, and resulting balance.

---

## 🧰 Function Description  

### `price_storage_contract()`

```python
def price_storage_contract(
    injection_dates,
    withdrawal_dates,
    price_data,
    injection_rate,
    withdrawal_rate,
    max_storage,
    storage_cost_per_month,
    injection_cost,
    withdrawal_cost,
    transport_cost
)


