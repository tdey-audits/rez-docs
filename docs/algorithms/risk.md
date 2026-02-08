# Algorithm: Risk Management

Risk management in Rez is a deterministic layer that caps the agent's ambition. This section covers the mathematical formulas used to calculate position size, leverage, and stop-loss levels.

## Position Sizing

The size of a trade is calculated as a percentage of the total account equity, guided by the selected Risk Profile.

$$Size_{USD} = Equity_{Total} \times \frac{Allocation_{\%}}{100}$$

### Minimum Order Enforcement
Hyperliquid and other exchanges have minimum order sizes. Rez ensures:
$$Size_{final} = \max(Size_{USD}, 12.0)$$
*The $12.0 value provides a safe buffer above the standard $10.0 minimum.*

## Leverage and Margin

Leverage is used to multiply exposure, but it is capped based on the risk profile to prevent liquidation from minor price fluctuations.

$$Margin_{Required} = \frac{Size_{final}}{Leverage}$$

## Volatility-Adjusted Stops (ATR)

While the LLM can suggest arbitrary TP/SL prices, it is often instructed to use the **Average True Range (ATR)** for more robust risk management.

### Stop-Loss (SL) Calculation
The SL is typically set at a multiple of ATR from the entry price ($P_{entry}$).

$$SL_{Long} = P_{entry} - (k \times ATR)$$
$$SL_{Short} = P_{entry} + (k \times ATR)$$

*Where $k$ (the "ATR Multiplier") is typically between 1.5 and 3.0.*

## Reward-to-Risk Ratio (RRR)

Rez validates that any trade decision meets a minimum efficiency threshold.

$$RRR = \frac{|P_{target} - P_{entry}|}{|P_{stop} - P_{entry}|}$$

**Validation Rule**: If $RRR < 1.5$, the agent is often prompted to reconsider the trade, as the potential gain does not sufficiently compensate for the risk taken.

## Drawdown Protection

The system monitors cumulative PnL. If a significant percentage of the account is lost in a short window, the "Cooldown" parameter of the risk profile is extended, effectively slowing down the agent to prevent "revenge trading" or catching falling knives.
