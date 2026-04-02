## Bedrock AgentCore Identity 
Bedrock AgentCore Identity is an **Agent's identity and Credential management service** that helps you to securely build enterprise-ready agents.
It solves the unique security challenges of AgenticAI by providing centralized, secure handling of authentication, authorization, and credentials.
It enables:
        - Agents to be invoked securely by users or applications.
        - Agents to call downstream AWS resources or third-party services (e.g., GitHub, Google Drive, Salesforce) without hardcoding secrets.
        - Full auditability, zero-trust verification on every request, and support for OAuth 2.0 (2-legged and 3-legged flows), IAM SigV4, JWT/OIDC, and API keys.

        Zero Trust means :
               Never trust any request by default — **always verify**:
                Identity
                Device
                Context

🧠 Core Idea
        Inbound authentication = Someone is calling your Agent
        Outbound authentication = Your Agent is calling something else



* (Refer slide : 19-Identity.png)
🔐 Inbound Authentication (Who can call my Agent?)
        Q : The request hits AgentCore Runtime or Gateway" ?
        Ans : The initial user request (the one carrying the user's JWT or IAM signature) can hit either:
                The AgentCore Runtime endpoint (most common for invoking the main agent)
                                                OR
                The AgentCore Gateway endpoint (if you're directly exposing tools via Gateway).

        📌 Definition
                Inbound auth is how AgentCore verifies the caller before allowing access to your agent.
                - It protects the agent from unauthorized callers.
                - This validates and authorizes the caller (user or application) before the request even reaches your agent code.

        📌 Supported mechanisms:
                - IAM SigV4 (default, automatic—no extra config needed; uses AWS IAM credentials).
                - JWT Bearer Token (OAuth/OpenID Connect) from an external Identity Provider (IdP) like Amazon Cognito, Okta, Entra ID, etc.

        🔍 Step-by-step
                1. User authenticates with your Identity Provider (IdP)  (e.g., Cognito User Pool) → receives a JWT access token.    
                2. Client sends a request to the AgentCore Runtime (or Gateway) endpoint with the header: Authorization: Bearer <JWT_TOKEN>.
                3. The runtime’s Inbound Authorizer (configured at runtime creation) validates the JWT:
                        - Signature and expiry (using the IdP’s discovery URL).
                        - Issuer (iss), client ID (client_id), audience (aud), scopes, and any custom claims you defined.
                4. On success, AgentCore Identity
                        - Creates or uses the runtime’s automatic Workload Identity (a special agent-specific identity).
                        - Issues an internal Workload Access Token that bundles the workload identity + validated user identity (e.g., the sub or username claim from the JWT).
                5. This token and user context are passed to your agent code (available in the request payload header).
                        - If validation fails → request is rejected immediately (401/403). No agent code runs.

        🧪 Example
                - A frontend app calls your agent API
                - It sends a JWT from Cognito / IdP
                - AgentCore Identity:
                        Verifies signature
                        Extracts user identity
                        Checks access policy

        🔑 Key Responsibility
                👉 Protect the Agent from unauthorized access

        --- video notes---
        - AgentCore Identity is focused on two aspects authentication and authorization.
        - AgentCore Identity supports all the major identity providers, whether it's EntraID (Azure AD), Okta or Cognito.
        - Once the user provided his credentials and Cognito or EntraID has authenticated him, Next aspect is the authorization.
        - Once the user authenticated through the federated identity (EntraID (Azure AD), Okta or Cognito) the external token would be generated. 
                - in case of EntreID you will have an OAuth token.
                - If it's related to AWS services then IAM role would be generated.
        - Once the AgentCore Runtime has this token so it will validates the token with the agent core identity and then grant access to User. 
        - AgentCore Identity also has something known as "Agent Identity" directory, which keeps a record of all the agents and their identities. (It keeps 
        metadata details such as name, ARN, created time, last update time, and so on) ---> "Agent Identity" is the internal working of the agent core 
        identity.

* (Refer slide : 19-Identity.png)
🚀 Outbound Authentication (Agent calling external services)
        📌 Definition
                Outbound auth is how your agent proves its identity when calling other systems.
                - giving the agent credentials to act on behalf of the authenticated user, without exposing secrets.
                - This lets your agent to obtain short-lived, scoped credentials to call external services on behalf of the authenticated user.

        📌 Supported mechanisms:
                - IAM execution roles (for AWS services).
                - OAuth 2.0 clients (2LO client credentials grant or 3LO authorization-code grant with user consent).
                - API keys (stored securely).
                You configure these as Resource Credential Providers in AgentCore Identity (via console, CLI, or SDK).

        🔍 Step-by-step
                1. Your agent code invokes a tool/function decorated with something like @requires_access_token(provider_name="my-google-provider", scopes=[...]).
                2. AgentCore Identity receives the request along with the Workload Access Token (from the inbound step).
                        - Fetches credentials (IAM role / OAuth token)
                        - Signs request or attaches token
                3. Using the credential provider config (client ID/secret you supplied earlier) and the user context from the inbound JWT, it:
                        - Performs token exchange or starts a 3LO flow (generates an authorization URL for user consent if needed).
                        - Retrieves or refreshes the outbound access token.
                4. The token is securely stored in the Token Vault (keyed by Workload Identity + User ID so the same user doesn’t have to re-consent every time).
                5. The agent receives the outbound token and uses it to call the external API (e.g., Google Drive API).

                Tokens are short-lived, automatically rotated where possible, and never appear in your agent code or logs.

        🧪 Example
                - Agent calls an external API (e.g., payments service)
                - AgentCore Identity:
                        Injects API key / OAuth token
                - API verifies token → returns response     

        🔑 Key Responsibility
                👉 Allow the Agent to securely access external systems      

        --- video notes---
        - when user make a request to access SharePoint , DynamoDB or search the internet via our AgentCore application, for that he needs a certain key.
        - Here our application making request to the external data source ( access SharePoint , DynamoDB or search the internet) based on the user 
          request, this is where outbound auth comes into play.
        - Our Agent needs to validate whether it is actually who it claims to be. And it has to access the tool (lambda function) to retrieve the data 
          from DynamoDB on behalf of user.

        how does this outbound auth happen ?
        - AgentCore Identity has "Agent Token Exchange".
        - Based on the OAuth/IAM token that was provided to this AgentCore Runtime and the "Agent Identity's" metadata, based on these two the "Agent 
          Token Exchange" generates "AWS Generated Token" and send it to "AgentCore runtime", which is again passed to "AgentCore Gateway" and it 
          validates again by AgentCore Gateway.
        - Depending on what the user is trying to access
                Ex 1: if user trying to access AWS service(Ex:DynamoDB) using the AWS Lambda, then the Lambda function will get this "IAM execution 
                      role" to extract the data from DynamoDB and pass it back to the user.
                Ex 2: if user trying to access Microsoft resources (Ex:SharePoint or some other service), then it will use the "OAuth token" to access 
                      the resources from SharePoint.
                Ex 3: if user trying to acces external tool (Ex: SurperDev), For that you will need an API key. AgentCore Gateway provides  
                      "Resource Token Vault" where you can store the "API key" and the "user token" to make the request. The "AgentCore Gateway" retrieve this API key from the Resource Token vault and access that external service.

🧪 How Inbound and Outbound Flow Together (End-to-End)
        The magic happens in the identity propagation and token exchange:

        1. Inbound JWT → provides user identity (sub, scopes, etc.).
        2. Workload Identity (auto-created for the runtime) + Inbound JWT → Workload Access Token.
        3. Workload Access Token → used by AgentCore Identity to fetch Outbound Access Token from the configured credential provider.
        4. Result: The agent acts as itself (workload identity) but carries verifiable user context (delegation, not impersonation). Every hop is independently verified (zero-trust).

        👉 Key Terminology (Quick Reference)
                - Workload Identity: The agent/runtime’s own identity (auto-created).
                - Resource Credential Provider: Stores config for external services (OAuth client, API key, etc.).
                - Token Vault: Secure, encrypted store of tokens, scoped to workload + user.
                - Agent Access Token: Internal token that carries both workload and user identity.
                - 2LO vs 3LO: Machine-to-machine (no user) vs. user-consent flows.

⚖️ Inbound vs Outbound (Crystal Clear Comparison)
| Aspect                   | Inbound Auth           | Outbound Auth        |
| ------------------------ | ---------------------- | -------------------- |
| Direction                | Into Agent             | Out from Agent       |
| Who is proving identity? | Caller (user/app)      | Agent                |
| Who verifies?            | AgentCore Identity     | External service     |
| Purpose                  | Protect agent          | Enable agent actions |
| Example                  | User calling agent API | Agent calling S3/API |

🔐 Where AgentCore Identity Fits
        AgentCore Identity is the central trust engine:
        It handles:
                - Token validation (inbound)
                - Identity propagation
                - Credential injection (outbound)
                - Role/permission enforcement

⚠️ Common Confusions (Let me challenge your thinking)
        ❌ Mistake 1:
                "Inbound and outbound are separate systems"
                👉 Wrong.
                   They are two sides of the same request lifecycle.

        ❌ Mistake 2:
                "Agent handles auth itself"

                👉 Not exactly.
                   AgentCore Identity abstracts this, so your agent logic stays clean.

        ❌ Mistake 3:
                "Same token used everywhere"
                👉 Dangerous assumption.
                        Inbound → user token
                        Outbound → service credentials (often different!)

🧠 Quick Explanation of the Flow (Matching the Diagram : 21-identity-flow.svg)
        👉 Inbound Flow (Left side / Steps 1–8):

                1. User logs in via your Identity Provider → gets JWT.
                2. Client sends JWT to AgentCore Runtime.
                3. Runtime forwards to AgentCore Identity for validation.
                4. On success → Workload Access Token is issued (combines agent’s workload identity + user identity).
                5. Agent code receives the request with full user context.
        
        👉 Outbound Flow (Right side / Steps 9–14):

                1. When the agent needs to call an external service, it asks AgentCore Identity for credentials.
                2. Identity uses the Workload Access Token (from inbound) + configured Resource Credential Provider to fetch/refresh the outbound token.
                3. Token is returned securely to the agent.
                4. Agent calls the external service without ever seeing secrets.

        This design ensures secure delegation: the agent acts on behalf of the authenticated user while keeping credentials out of your code.


* (Refer slide : 20-Identity.png)
Key Features:
- Inbound Authentication: 
        Validate access for users and application calling agents or tools.
        (Is this User allowed to call the agent ? - Authorization)

- Outbound Authentication: 
        Secure access from agents to external services on behalf of users.
        (Is this Agent allowed to access the tools on user's behalf ? )

- Supports OAth Integration: 
        Enterprise ready (Supprts Identity Providers : microsoft ActiveDirectory [Entra ID], Okta, AWS Cognito)

- AWS IAM Integration: 
        Native integration with AWS identity and access management to access the AWS Services.

- Cross-Platform Support: 
        Works across AWS, other cloud providers and on-premise environment.
        (Ex: sharepoint, DropBox)

## AgentCore Gateway
AgentCore Gateway Acts as a gateway to the tools that our agents are going to invoke.
- The Agent Core Gateway integrates with the Agent Core Identity service, which allows our agent to access the tools securely through the Agent Core gateway.
(Refer slide : 22-gateway.png, 23-gateway-and-identity-handson.png,)

capabilities that Agent Core Gateway:
- Agent Core Gateway integrates with the AgentCore Identity service, which allows our agent to access the tools securely through the Agent Core gateway.

- The main functionality of AgentCore Gateway : intelligent tool discovery(Based on the user request It intelligently discovers and invokes the right tool based on the semantic search.)
Ex:
    tool 1: SerperDev Tool (gather information related to tourist attraction)
    tool 2: get the weather details
    tool 3: Invoke tool - BedrockInvokeAgentTool (invoke lambda function), lambda function is going to retrieve the data from the enterprise database and then send it back to the user.
    toon n : etc.. etc..

- MCPfies the tools : protocol conversion.
Ex: Let's say you have a lambda function, or you have any external resource or tool which uses the Rest protocol. And your agent uses the MCP protocol so that protocol conversion will happen through Agent Core Gateway.

- observability capability: Monitor agent performance in production - Logs, Metrics and GenAI Observability.

(Refer slide : 25-aws-gateway-and-identity-setup-aws.png)
- Our Vacation planner can be accessed publically for all users (Authentication is not required)
- we're setting Authorization only (machine-to-machine), because our agent will communicate with external data sources via Tools.
- create the Cognito user pool, which is going to act as our or auth server, an identity provider.
- Register the app with Cognito user pool
        FLOW: (Refer slide :  24-gateway-and-identity-handson.png)
                1. once this request comes to the vacation planner this vacation planner is already registered with this Cognito user pool or the OAuth server identity.
                2. Agent will go and ask this identity provider for a token request.
                3. identity provider will generate a JSON web token (JWT) (sometimes also called as bearer token)
                4. The  identity provider sends back the JWT token to our application agent.
                5. our application agent send that token to the agent core gateway.
                6. Once the agent Core gateway receives this token, it will take a look at this issuer URL and make a request to this Cognito identity provider to 
                   validate the JWT token.
                7. Once this token is validated, then based on the user request, AgentCore Gateway will act as an MCP server.
                8. AgentCore Gateway Invoke the right tool and send it back to the user.
- create DynamoDB table
- write an AWS Lambda function, which takes the input as city and then gives back the data to the agent. Attach policy to allow this Lambda function access to our DynamoDB. (here lambda function calling to dynamoDB)
- Create OpenAPI Schema - (link mentioned in slide), and add this schema in AgentCore Gateway as Schema.
        Q: What is OpenAPI Schema ?
        Ans: OpenAPI is a standard, machine-readable way to describe an API.
                👉 The OpenAPI schema is NOT for Lambda
                👉 It is for the LLM (Agent)

                Without it, the agent has no idea:
                        when to call your Lambda
                        what parameters it needs
                        what the response means

        Q: Lambda already has code—why do we need OpenAPI schema? 
        Ans: Because AgentCore is not calling your Lambda like a developer would. It’s letting an LLM decide how to call it.
- Create a Gateway - it provides the invocation code,  which is used to execute our agent code gateway.  (It supports the MCP protocol- which uses "jsonrpc").  
                     Test Locally : Save this tool and replace it with clientid, client secret and also add the method to call the tool and run it locally to test it.
                                    - add permission to call the tools in IAM role created for AgentCore Gateway