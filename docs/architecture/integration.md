# Integration

The integration between Rez's Python backend and its Next.js frontend is designed for real-time visibility and auditability. The frontend acts as a monitoring dashboard, consuming data structured by the backend.

## Data Exchange Mechanism

Rez uses a file-based streaming architecture for its primary logs, ensuring that data is persisted even if the frontend or storage layer restarts.

### The Trade Diary (`diary.jsonl`)
Every important event in the trading lifecycle is appended to the `diary.jsonl` file. This includes:
*   Account balance snapshots.
*   The agent's verbose reasoning and summary.
*   Asset execution details (side, size, leverage).
*   Trigger order updates (TP/SL).

The frontend reads this JSON-Lines file to populate the timeline and trade history.

### Real-Time Syncing API
A lightweight `aiohttp` server runs alongside the trading loop. This API exposes endpoints that the frontend polls or connects to:
*   `/diary`: Streams the latest entries from the diary.
*   `/state`: Provides the real-time status of the trading loop (Active/Idle/Paused).
*   `/meta`: Exposes configuration metadata (Current Risk Profile, tracked assets).

## Workflow: From Trade to Dashboard

1.  **Backend Cycle**: Rez completes a trading cycle.
2.  **Persistence**: The `decide_trade` result and execution status are written to `diary.jsonl`.
3.  **API Fetch**: The Next.js frontend (via `SWR` or `React Query`) fetches the latest data from the local backend API.
4.  **Visualization**:
    *   **Reasoning**: The "inner monologue" of the LLM is displayed in a dedicated log viewer.
    *   **PnL Tracker**: Account value updates are plotted on a 3D-enhanced chart.
    *   **Active Positions**: Current exposure is visualized with real-time PnL updates.

## Technical Requirements for Integration

*   **CORS Configuration**: The backend explicitly allows requests from the frontend origin (typically `localhost:3000`).
*   **JSON Schema Consistency**: Both the Python backend (`src/agent/`) and TypeScript frontend share the same decision schema to ensure seamless parsing of trading data.
