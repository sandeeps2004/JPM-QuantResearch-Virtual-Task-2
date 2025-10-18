JP Morgan Quantitative Research

Task 2 – Price a Commodity Storage Contract

This repository contains files for Task 2 of the J.P. Morgan Quantitative Research Virtual Job Experience. The goal is to use the price forecasting model from Task 1 to build a valuation tool for a physical commodity storage contract.

📊 Problem Statement

A client wants to execute a seasonal arbitrage strategy (buy low in summer, sell high in winter). We were tasked with building a model to calculate the Net Present Value (NPV) of their proposed trading schedule.

The model needed to:

Take all contract parameters as inputs (injection/withdrawal dates, volumes, rates, and costs).

Calculate all associated cash flows (purchases, sales, storage costs, transaction fees).

Respect physical constraints like maximum storage capacity and available inventory.

Return the final net profit or loss of the contract.

🛠️ Approach

The solution is an Event-Driven Simulation, which processes all contract events in chronological order to accurately model cash flows and inventory.

Steps:

Create a Timeline: All injection and withdrawal dates are combined and sorted chronologically to create a master event schedule.

Initialize State: The simulation starts with key variables at zero (e.g., current_stored_volume, total_cash_flow).

Iterate Through Events: The model loops through the timeline, processing each event one by one.

Apply Business Logic: For each event, the model:

Calculates storage costs incurred since the last event.

Checks physical constraints (Is there space to inject? Is there gas to withdraw?).

Calculates the cash flow for the purchase or sale using the forecasted price for that day.

Updates the current_stored_volume and total_cash_flow.

Return Final Value: After all events are processed, the final total_cash_flow is returned as the contract's value.

📈 Results

The model successfully calculates the NPV of a given trading strategy by simulating all associated cash flows.

It correctly enforces real-world physical constraints, preventing unrealistic scenarios like selling non-existent gas.

A key output is a detailed transaction log in a pandas DataFrame, which makes the final valuation transparent and easy for a trader to audit.

Example Usage

The main function price_commodity_storage_contract is called with the strategy parameters. It returns the final value and the detailed log.

# Example of how the pricing function is called
final_value, transaction_log = price_commodity_storage_contract(
    injection_dates=injection_days_list,
    withdrawal_dates=withdrawal_days_list,
    injection_volume_per_day=35000,
    withdrawal_volume_per_day=35000,
    max_storage_volume=1000000,
    storage_cost_per_mmbtu_per_day=0.001,
    injection_withdrawal_cost_per_mmbtu=0.01,
    price_estimator_func=price_estimator
)

print(f"Final Contract Value: ${final_value:,.2f}")
display(transaction_log)
