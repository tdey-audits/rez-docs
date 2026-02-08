# AI-Powered Trading

Rez's core intelligence resides in its ability to process qualitative reasoning alongside quantitative data. Unlike traditional bots that follow "If A then B" logic, Rez asks, "Why should I execute B given the current state of A?"

## The Neuro-Symbolic Approach

Rez utilizes a hybrid AI architecture:

*   **Symbolic Layer (The Tools)**: Hard-coded mathematical functions (indicators like RSI, EMA) provide objective truths about market structure.
*   **Neural Layer (The Brain)**: Large Language Models (LLMs) interpret these truths. The LLM doesn't just see "RSI is 70"; it understands "RSI is 70, suggesting overbought conditions, but EMA is sloping upwards, indicating strong momentum."

## The ReAct (Reasoning + Acting) Loop

Rez doesn't make decisions in a single shot. It follows a multi-turn interaction pattern known as **ReAct**:

1.  **Observation**: Rez gathers the initial market context (Price, Account Balance, basic indicators).
2.  **Reasoning**: The LLM writes out a "thought" process analyzing the data.
3.  **Action**: If the LLM needs more data (e.g., "I want to see the 4h ATR to judge volatility"), it emits a **Tool Call**.
4.  **Observation Update**: The backend executes the tool locally and feeds the results back to the LLM.
5.  **Final Decision**: This loop repeats until the LLM has enough information to issue a `buy`, `sell`, or `hold` order.

### Why this matters?

This approach ensures that Rez's trades are **context-aware**. It can decide to stay out of a "perfect" technical setup if the overall reasoning suggests high risk or upcoming volatility.

## Chain of Thought Transparency

Every cycle, Rez generates a `reasoning` field. This is not just a log; it is the actual internal "inner monologue" the model used to arrive at its decision. This transparency allows users to understand the "why" behind every "what".
