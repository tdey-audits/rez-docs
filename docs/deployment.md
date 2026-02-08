# Deployment & Configuration

This guide covers setting up Rez for production, either via Docker or in a Trusted Execution Environment (TEE) on EigenCloud.

## Environment Configuration

Rez is configured via a `.env` file in the root directory.

```bash
# Core Identifiers
HYPERLIQUID_PRIVATE_KEY=0x...    # Your wallet private key
OPENROUTER_API_KEY=sk-or-123...  # API key for LLM access

# AI Settings
LLM_MODEL=openai/gpt-4o          # Recommended model
RISK_PROFILE=conservative        # [conservative|moderate|high|debug]

# Trading Settings
ASSETS=BTC ETH SOL               # Space-separated list of assets to trade
INTERVAL=1h                      # Loop frequency (e.g., 5m, 15m, 1h, 4h)
LEVERAGE_CAP=5.0                 # Hard cap on leverage regardless of profile

# Optional
API_PORT=3000                    # Port for the local monitoring API
```

## Running with Docker

The easiest way to run Rez in a consistent environment is using Docker.

```bash
# 1. Build the image
docker build -t rez-agent .

# 2. Run container (background mode)
docker run -d \
  --name rez-v1 \
  --env-file .env \
  -p 3000:3000 \
  rez-agent
```

## EigenCloud Deployment (TEE)

For institutional-grade security, you can deploy Rez to an EigenCloud Trusted Execution Environment (TEE). This ensures that your private keys are encrypted and processed in a secure enclave.

### Prerequisites
*   Install the EigenX CLI: `curl -fsSL https://eigenx-scripts.s3.us-east-1.amazonaws.com/install-eigenx.sh | bash`

### Deployment Steps

1.  **Login**: Authenticate with the EigenCloud registry.
    ```bash
    eigenx auth login
    ```

2.  **Deploy**: Push your application to the secure enclave.
    ```bash
    eigenx app deploy --env-file .env
    ```

3.  **Monitor**: Watch the logs in real-time.
    ```bash
    eigenx app logs --watch
    ```

### Why TEE?
*   **Encrypted Storage**: Keys are never exposed to the host machine.
*   **Attestation**: Verify program integrity cryptographically before execution.
*   **Tamper-Proof**: Code runs in isolation, protected from external manipulation.
