# Backend Architecture

The Rez backend is a modular Python-based system designed for high reliability and intelligent trade execution. It is divided into three primary functional engines:

## Components

### 1. [Trading Engine](trading.md)
The gateway to the Hyperliquid exchange. It handles secure transaction signing, market data fetching, and order lifecycle management.

### 2. [Decision Agent](agent.md)
The AI-driven core. It uses a ReAct pattern to orchestrate multi-turn reasoning cycles with Large Language Models, transforming indicators into trade decisions.

### 3. [Indicator Engine](indicators.md)
The computational layer. It calculates technical indicators like EMA, RSI, and MACD locally using Binance data and `pandas-ta`, ensuring the agent has access to low-latency, accurate signals.

---

For a higher-level view, see the [System Overview](../README.md).
