# Indicator Engine

The Indicator Engine (`src/indicators/`) is responsible for providing the deterministic "ground truth" that the AI uses for its reasoning. It transforms raw price movements into technical signals.

## Local vs. API-Based Calculation

Unlike many trading bots that rely on external Technical Analysis (TA) APIs, Rez calculates indicators **locally**.

### Why Local?
1.  **Reduced Latency**: No network round-trips for every indicator lookup.
2.  **Cost Efficiency**: Eliminates dependencies on paid TA API tiers.
3.  **Accuracy**: Rez controls exactly how indicators are calculated, ensuring consistency with the exchange's data.

## The `LocalIndicatorCalculator`

The calculation process follows a three-step pipeline:

### 1. Raw Data Fetching
Rez pulls raw OHLCV (Open, High, Low, Close, Volume) data directly from the **Binance `/klines` API** (the industry standard for depth and reliability).

### 2. Warmup & Series Generation
To ensure accuracy, Rez implements a "warmup period" for recursive indicators like EMA and RSI.
*   **Formula**: If 10 results are requested, Rez fetches `10 * 3 = 30` candles. 
*   **Rationale**: This ensures that the initial "cold" values of the exponential averages have vanished, and the current value matches what a trader would see on an exchange chart.

### 3. Processing with `pandas-ta`
Rez leverages the `pandas-ta` library, a high-performance technical analysis library built on top of Pandas. This allows for complex calculations like MACD or ATR to be completed in milliseconds.

## Supported Indicators

| Indicator | Short Name | Key Use Case |
| :--- | :--- | :--- |
| **Exponential Moving Average** | `ema` | Trend identification and Support/Resistance. |
| **Relative Strength Index** | `rsi` | Overbought/Oversold levels. |
| **MACD** | `macd` | Momentum crossovers. |
| **Average True Range** | `atr` | Volatility-adjusted Stop-Loss placement. |

## Tool-Calling Integration

The Indicator Engine is exposed to the `TradingAgent` via the `fetch_indicator` tool. When an LLM calls this tool, it passes the `asset`, `indicator_name`, and `interval` (e.g., `5m`, `1h`, `4h`). The engine calculates it on the fly and returns the precise value.
