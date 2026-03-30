## 1. Inbound Authentication Sequence (Protecting Access TO the Agent)
![Inbound Authentication Sequence](/UD/images/Inbound-Authentication-Sequence.svg) 

## 2. Outbound Authentication Sequence (Secure Access FROM the Agent)
![Outbound Authentication Sequence](/UD/images/outbound-Authentication-Sequence.svg) 

## 3. Combined End-to-End Flow (Inbound + Outbound Together)
![Combined End-to-End Flow](/UD/images/combined-End-to-End-Flow.svg) 

👉 Key Points Highlighted in the Diagrams

- **Inbound** focuses on protecting the entry point to your agent (validating the caller).

- **Outbound** focuses on giving the agent secure, short-lived credentials to act externally.

- The **Workload Access** Token acts as the bridge: it carries both the agent’s workload identity and the authenticated user’s context.

- All tokens are short-lived, stored securely in the **Token Vault**, and never hardcoded.

- Supports both **2LO** (machine-to-machine) and **3LO** (user consent) for outbound.