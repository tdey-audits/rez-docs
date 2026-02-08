# Risk Profiles

Rez manages risk through predefined **Risk Profiles**. These profiles act as deterministic guardrails for the stochastic reasoning of the AI. Each profile defines the "character" of the agent, from ultra-cautious to aggressive.

## Profile Comparison

| Feature | Conservative | Moderate | High | Debug |
| :--- | :--- | :--- | :--- | :--- |
| **Max Leverage** | 3x - 5x | 5x - 10x | 10x - 20x | N/A |
| **Account Allocation** | 20-40% | 40-60% | 60-80% | Fixed (Min Order) |
| **Signal Confluence** | High (3+ indicators) | Moderate (2/3) | Single Signal | Low/Experimental |
| **Cooldown Period** | 15 Minutes | 10 Minutes | 5 Minutes | 0 Minutes |

## Profile Descriptions

### 🛡️ Conservative
The conservative profile prioritizes capital preservation. It requires strong alignment between multiple timeframes (e.g., 5m and 4h trends must match) and uses low leverage to minimize liquidation risk.

### ⚖️ Moderate
The balanced approach. It seeks a healthy ratio between risk and reward. It participates in established trends but avoids highly volatile entry points without sufficient confirmation.

### 🔥 High (Aggressive)
The aggressive profile is designed for high-volatility environments. It uses higher leverage and requires less confluence to enter a trade, aiming to capture rapid price movements. **Note**: This profile carries significantly higher risk of drawdown.

### 🛠️ Debug
Used exclusively for system testing. It executes trades frequently with the minimum exchange allowable size ($12-13) to verify execution pipelines and API connectivity regardless of market conditions.

## Selecting a Profile

The risk profile is set at startup via the `--risk-profile` CLI argument. 

```bash
# Running with Conservative profile
python -m src.main --risk-profile conservative
```

The selected profile is injected into the **System Prompt**, instructing the LLM on exactly how aggressive it should be in its reasoning.
