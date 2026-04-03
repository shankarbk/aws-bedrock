## 1. Inbound Authentication Sequence (Protecting Access TO the Agent)
![Inbound Authentication Sequence](/UD/images/Inbound-Authentication-Sequence.svg)   
<iframe src="/UD/images/inbound_auth_flow.html" width="100%" height="500px"></iframe>
<!-- right click on file "outbound_auth_flow.html" -> show preview (need "Live Preview" extension from microsoft)-->


* How an external client proves identity to AgentCore before any LLM is touched.   

| Step | What happens |
|------|-------------|
| 1 | Client gets a JWT / SigV4 credential from its IdP (Cognito, Okta, etc.) |
| 2 | HTTPS request arrives at AgentCore Gateway (via PrivateLink inside VPC) |
| 3 | AgentCore Identity validates the token — signature, expiry, issuer, audience |
| 4 | IAM checks authorization — is this caller allowed to invoke this agent? |
| 5 | AgentCore Policy enforces guardrails (Cedar policies, Bedrock Guardrails) |
| 6 | All checks pass → agent execution begins |

## 2. Outbound Authentication Sequence (Secure Access FROM the Agent)
![Outbound Authentication Sequence](/UD/images/outbound-Authentication-Sequence.svg)
<iframe src="/UD/images/outbound_auth_flow.html" width="100%" height="500px"></iframe> 
<!-- right click on file "outbound_auth_flow.html" -> show preview (need "Live Preview" extension from microsoft)-->


* How the agent proves its identity to external tools/APIs when making tool calls.   

| Step | What happens |
|------|------------|
| 1 | LLM decides to call a tool; Runtime intercepts before any outbound call |
| 2 | AgentCore Identity fetches the right credential from Secrets Manager / STS |
| 3 | AgentCore Gateway enforces Cedar policies — can this agent call this tool? |
| 4 | Request is signed (SigV4 for AWS, Bearer token for OAuth, API key for others) |
| 5 | External service validates and processes the request |
| 6 | Result returned to agent context — credentials never exposed to the LLM |

## 3. Combined End-to-End Flow (Inbound + Outbound Together)
![Combined End-to-End Flow](/UD/images/combined-End-to-End-Flow.svg) 

👉 Key Points Highlighted in the Diagrams

- **Inbound** focuses on protecting the entry point to your agent (validating the caller).

- **Outbound** focuses on giving the agent secure, short-lived credentials to act externally.

- The **Workload Access** Token acts as the bridge: it carries both the agent’s workload identity and the authenticated user’s context.

- All tokens are short-lived, stored securely in the **Token Vault**, and never hardcoded.

- Supports both **2LO** (machine-to-machine) and **3LO** (user consent) for outbound.