# Algorithm: LLM Reasoning (ReAct)

The intelligence of Rez is not just in its underlying model, but in the **recursive reasoning loop** it executes. This algorithm, based on the ReAct (Reasoning and Acting) framework, allows the agent to dynamically explore its environment.

## The Interaction Algorithm

The `TradingAgent` manages a conversation history that can span up to 6 turns (default) per trading cycle.

### Turn 1: The Zero-Shot Context
The agent sends the initial "System Prompt" and "Market Context".
*   **Prompt**: "You are Rez. Your goal is to maximize PnL while adhering to the [RISK_PROFILE]..."
*   **Context**: A broad snapshot of current prices and standard indicators (EMA 20, RSI 14).

### Turn 2-N: The Recursive Discovery
The LLM analyzes the context. If it identifies a gap in its knowledge, it emits a `fetch_indicator` tool call.

```python
# Pseudo-logic of the ReAct turn
while turn < max_turns:
    response = llm.generate(history)
    if response.has_tool_call:
        result = execute_tool(response.tool_call)
        history.append(tool_result)
        turn += 1
    else:
        return parse_final_decision(response)
```

## The Reasoning Engine Prompting

Rez uses "Chain of Thought" prompting to force the model to verbalize its internal logic.
1.  **Analyze Trends**: "Observe the slope of the 4h EMA."
2.  **Verify Confluence**: "Check if the 5m RSI supports the long-term trend."
3.  **Evaluate Risk**: "Assess if the distance to the next major resistance justifies the leverage."
4.  **Form Decision**: Output the final trade parameters.

## Output Parsing & Resilience

The final turn must result in a structured JSON object.

### The Sanitization Recursive Step
If the primary model returns text instead of JSON, Rez triggers the `_sanitize_output` algorithm:
1.  Extract raw strings that look like JSON.
2.  Pass the raw strings to a high-speed "Resilience Model" (e.g., GPT-4o-mini).
3.  The resilience model is constrained by a **Strict JSON Schema**.
4.  Validation: The system checks that required fields (`action`, `leverage`, `tp_price`) are present and mathematically valid (e.g., `tp_price` > `entry_price` for a buy).

## Failure Handling
If the LLM fails to provide a valid decision after the recursion limit or sanitization fails, the algorithm defaults to a `hold` action to protect the user's capital.
