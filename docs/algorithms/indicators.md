# Algorithm: Indicator Calculation

Rez's Indicator Engine is built for mathematical precision. This section details the algorithms used to transform raw Binance OHLCV data into actionable technical signals.

## Data Pre-processing

Before any calculation, raw data is normalized:
1.  **Filtering**: Only `timestamp`, `open`, `high`, `low`, `close`, and `volume` are retained.
2.  **Casting**: Prices are cast to `float64` for high-precision arithmetic.
3.  **Time-Normalization**: All timestamps are converted to UTC to ensure consistency across assets.

## The Warmup Algorithm

Recursive indicators like the Exponential Moving Average (EMA) and Relative Strength Index (RSI) depend on previous values. To prevent "cold start" errors where the initial value is just the first price point, Rez uses a **3x Warmup Rule**.

$$L_{fetch} = \max(100, N_{results} \times 3)$$

Where:
*   $L_{fetch}$ is the number of candles fetched.
*   $N_{results}$ is the number of indicator values the agent requested.

### Rationale
By fetching three times the required data, the recursive calculation has sufficient "burn-in" time. For an EMA with a period of 20, the weighting of the first (and most inaccurate) data point is reduced significantly by the time the latest candle is reached.

## Specific Implementations

### 1. Exponential Moving Average (EMA)
Rez uses the standard exponential decay formula:

$$EMA_t = P_t \times \alpha + EMA_{t-1} \times (1 - \alpha)$$
$$\alpha = \frac{2}{period + 1}$$

### 2. Relative Strength Index (RSI)
Calculated using the Wilder's smoothing method via `pandas-ta`.
1. Calculate price change: $D_t = P_t - P_{t-1}$.
2. Split into gains ($U$) and losses ($D$).
3. Relative Strength ($RS$) = $\frac{AvgGain}{AvgLoss}$.
4. $RSI = 100 - (\frac{100}{1 + RS})$.

### 3. Precision Management
To keep the LLM context lean, all calculated values are rounded to **4 decimal places**. This maintains enough sensitivity for technical analysis while avoiding token bloat from unnecessary floating-point precision.

$$V_{clean} = \text{round}(V_{raw}, 4)$$
