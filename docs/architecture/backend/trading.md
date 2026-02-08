# Trading Engine

The Trading Engine (`src/trading/`) serves as the low-level execution layer for Rez. It is responsible for all secure communication with the Hyperliquid blockchain and maintaining account state.

## Core Responsibilities

1.  **Authentication & Signing**: Manages private keys (via `.env`) and signs EIP-712 transactions required by Hyperliquid.
2.  **Order Execution**: Translates abstract "buy/sell" decisions into specific API calls (Market, Limit, or Trigger).
3.  **State Synchronization**: Continuously fetches account equity, available margin, and active positions.
4.  **Order Management**: Tracks open triggers to ensure Take-Profit and Stop-Loss orders are correctly synced with active positions.

## Key Files

### `hyperliquid_api.py`
The primary interface for Hyperliquid. It wraps the Hyperliquid SDK to provide simplified methods:
*   `get_user_state()`: Returns detailed account info.
*   `place_market_order(asset, is_buy, size)`: Executes immediate trades.
*   `set_trigger_order(asset, pnl_configs)`: Sets up TP/SL triggers.
*   `close_position(asset)`: Closes current exposure with a market order.

## Safety Mechanisms

To protect capital during execution, the Trading Engine implements several guardrails:

*   **Size Buffer**: All orders include a small buffer to handle fee fluctuations and ensure minimum exchange requirements (~$10-12) are met.
*   **Dual-Order Entry**: When a market order is filled, the engine immediately (sub-second) attempts to place the corresponding TP/SL orders. If trigger placement fails, it logs a critical alert.
*   **Position Reconciliation**: Before opening any new trade, the engine verifies that no conflicting orders or positions already exist for that asset.

## Integration with Main Loop

The main loop calls the trading engine at the beginning and end of every cycle:
```python
# Startup: Sync state
state = trader.get_user_state()

# Execution: Place orders
if decision.action != "hold":
    trader.execute_decision(decision)
```
