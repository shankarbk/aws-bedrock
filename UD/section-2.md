- What is Amazon Bedrock AgentCore ?
Amazon Bedrock AgentCore is an **agentic platform** to build, deploy, and operate highly capable agents securely at scale. 

Amazon Bedrock AgentCore service provides a set of capabilities that can help you transition from POC built on agentic AI to production ready systems.

- Amazon Bedrock AgentCore and Amazon Bedrock Agents are two different services in the AWS GenAI Ecosystem. 
    - Amazon Bedrock Agents : is a fully managed service that allows you to build and configure autonomous agents without managing infrastructure or writing custom code. 
    - Amazon Bedrock AgentCore :  is a comprehensive set of services designed to deploy and operate AI agents securely at scale using any AI framework and Large Language Model.

- Core Problems AgentCore Fixes:
    1. LLMs are stateless
        LLMs don’t remember or plan.
        👉 AgentCore adds:
            Memory
            Context tracking
            Multi-step reasoning

    2. Tool usage is hard
        LLMs don’t naturally call APIs reliably.
        👉 AgentCore:
            Defines tools (APIs, Lambda, DBs)
            Lets the model choose and invoke them safely

    3. No built-in orchestration
        Multi-step workflows = messy code
        👉 AgentCore:
            Handles step-by-step execution
            Automatically chains actions

    4. Unpredictability of LLMs
        LLMs hallucinate, loop, or fail silently
        👉 AgentCore:
            Adds guardrails
            Retry logic
            Structured execution

    5. Scaling & Productionization
        A demo ≠ production system
        👉 AgentCore provides:
            Observability
            Logging
            Integration with AWS ecosystem

    🧩 Simple Mental Model

        User Request
            ↓
        AgentCore (brain + workflow engine)
            ↓
        Foundation Model (Claude / Titan)
            ↓
        Tools (APIs, DBs, Lambda)
            ↓
        Final Action / Response

🧠 Real-World Example
    Without AgentCore:

    You manually code:
        “If user asks X → call API A”
        “Then parse response → call API B”
        “Store state somewhere”
        “Retry if failure”

    With AgentCore:
        You define:
            Goal: “Plan a trip”
            Tools: Flight API, Hotel API

        👉 AgentCore handles the rest.

⚖️ Honest Take (Important)
    AgentCore is powerful—but not magic:

    👍 Strengths
        Reduces engineering complexity
        Standardizes agent design
        Integrates deeply with AWS

    👎 Limitations
        Less flexible than custom frameworks (e.g., LangChain-style setups)
        Still dependent on LLM reliability
        Can feel like a “black box” in complex workflows

- Once we built AgenticAI (RAG or GenerativeAI ) applications, deploying them to production wrt scalability, observability monitoring is a big challenge, thats where Bedrock AgentCore come into. 


* (Refer slide : 10-bedrock-agentCore-capabilities.png)
    - Bedrock AgentCore allows you to deploy AgenticAI apps which is built on any framework and foundation model to **AgentCore Runtime**.
    - AgentCore Runtime is like lambda for AI-Agents
    - In Existing code you need to add "AgentCore runtime decorator" (@app.entrypoint) to deploy it in AgentCore Runtime.
    - AgentCore Runtime supports any AgenticAI Frameworks (Ex: Langchain, crewAI, Strands Agent etc.. etc..)
    - AgentCore Runtime supports any LLM models (Ex: OpenAI, Gemini, Amazon Bedrock etc.. etc..)
    - When you deploy your AgenticAI application on AWS AgentCore Runtime, it provides you 2 endpoints 
        1. /invocation Endpoint(POST)
        2. /ping Endpoint (Health Check) 
    - You can deploy your AgenticAI application publicly accessible (allow agents to operate in the public environment) or you deploy the Agents within 
      VPC cloud for secure private communication and tighter network access control.
    - Once app is deployed, AgentCore provides the invocation code. we are going to use this to access the endpoint. (this invocation code is used to 
      create a lambda function and expose endpoint to the AWS API gateway.)

* (Refer slide : 11-bedrock-agentCore-capabilities.png)
    - Once the application is hosted on AgentCore Runtime, User issues requests to our AgenticAI application
    - Before the user hits request to application we need to make sure the user is Authenticated and Authorised to access our application.
    - To Authenticate the user, Bedrock AgentCore provides the capability called **AgentCore Identity**
    - AgentCore Identity works in 2 stages
        1. Inbound :
                    - User needs to authenticate before he can access the agent.
                    - It is the Authentication and Authorization between the end user and AgenticAI application.
                    - For this it supports any Identity providers (Ex: Amazon Cognito, EntraID, Octa etc.. etc..)
                    - When user has Authenticated using Amazon Cognito, The Amazon Cognito generates Barer token and send it to AgentCore Identity, here it validates the token, once its approved then user able to send the request to Endpoint("/invocation Endpoint") and access the AgenticAI application.

        2. Outbound: --we'll see it later--


* (Refer slide : 12-bedrock-agentCore-capabilities.png)
    - **AgentCore Memory** is the managed service, provides memory in 2 levels
        1. Short-term memory :
            It is only active during session duration.
            we have option to keep the short-term memory between 7 to 365 days. 

        2. Long-term memory
            Provides multiple strategies to keep the memory : Summarization, Semantic and user preference.

* (Refer slide : 13-bedrock-agentCore-capabilities.png)
    - The AgentCore Observability monitors the **Agent performance** in production via Cloudwatch Logs, Cloudwatch metrics and GenAi Observability.

* (Refer slide : 14-bedrock-agentCore-capabilities.png)
    - 👉 These three (AgentCore Gateway, AgentCore Browser, AgentCore Interpretor) are how an agent actually interacts with the outside world.

    **AgentCore Gateway**
    - Our AgentCore application needs some information from backend resources (Ex: S3 bucket, AWS Lambda, OpenAPI target, DataBase etc.. etc..), The 
      AgentCore Gateway helps to retreive those information, for this **AgentCore Gateway** helps in 3 ways.
        1. AgentCore Gateway integrate with AgentCore Identity (Here the "Outbound" Came into picture.)
           The **AgentCore Outbound Identity** validates whether the AgentCore Application has permission to access the backend resources 
           retrived through these tools. ( does it have right API Key to access such information ? )

        2. Tool Discovery : Based on the user query, the AgentCore Gateway autometically does the tool discovery and use the right tool amoung N number 
           of tools.

        3. MCPfying the tools used by our Agentic application.
    
    **AgentCore Browser**
    - AgentCore Browser is not a UI browser like Chrome. It’s a controlled programmatic web interaction capability for AI agents.
    - AgentCore Browser allows an AI agent to interact with websites like a human would — but programmatically.
        That includes:
            Opening web pages
            Clicking buttons
            Filling forms
            Extracting data
            Navigating multi-step flows
        
        “Why not just call APIs?”
            Because in reality:

            ❌ Many systems don’t expose APIs
                Legacy enterprise tools
                Internal dashboards
                Vendor portals

            ❌ APIs are incomplete
                Missing workflows
                Limited access

            ❌ Some workflows are UI-only
                Login → click → download → upload → submit

        🧩 Example (Real-World DevOps Use Case)
            🎯 Task:
                “Check EC2 pricing for a specific region and generate report”

            Without Browser:
                Find API
                Handle auth
                Parse JSON
                
                
            With AgentCore Browser:
                Agent:
                    Opens AWS pricing page
                    Searches instance type
                    Extracts pricing table
                    Summarizes

                👉 No API required.

    **AgentCore Interpretor**
    - AgentCore Interpreter is not just a code runner. It’s the execution engine that lets an AI agent safely run logic (code, calculations, data 
      processing) as part of its reasoning loop.
    - AgentCore Interpreter (within Amazon Bedrock AgentCore) allows an agent to:
        👉 Write → execute → observe → refine code during task execution
        
        🧩 Real Examples
            📊 Example 1: Data Analysis
                Task:
                    “Analyze Kubernetes pod logs and find error spikes”
                Agent flow:
                    Load logs
                    Write Python code to parse
                    Aggregate errors by timestamp
                    Detect spikes
                    Return summary

                👉 Interpreter executes all steps


Core services in Amazon Bedrock AgentCore ( GO KRIM ):
1. AgentCore Runtime : 👉 The execution engine of your Agent

                            What it is:
                                The environment where your agent actually runs
                                Manages the agent loop (plan → act → observe → repeat)

                            What it handles:
                                Step-by-step execution
                                Tool invocation lifecycle
                                State transitions
                                Scaling (multiple agent runs)

                    ---video notes---
                    - Provides a secure, serverless environment purpose-built for deploying and scaling dynamic agents and tools.
                        AgentCore Runtime is used to run our AgenticAI application that we built.
                    - its similar to AWS lambda.
                    - to run the code we need to add "AgentCore Runtime decorator" (its a python decorator)
                    - we can use any AgenticAI framework to run ourcode on AgentCore runtime(Ex: LangGraph, CrewAi, Strands Agent)
                    - we can also use any LLM models (Ex: OpenAi, Gemini, AMazon Bedrock)
                    - Once we deployed our AgenticAI code to AgenticCore Runtime, it provides 2 endpoints (POST - invocation endpoint 
                              PING  - Health check endpoint) 

2.  AgentCore Identity : 👉 Security + authentication layer for Agents

                            What it is:
                                Manages who the agent is and what it can access

                            What it controls:
                                Access to tools (APIs, DBs, AWS services)
                                Credentials handling
                                Permissions (IAM integration)
                        
                        ---video notes---
                        Allows agents to securely access and operate across AWS resources or third-party tools and services, on behalf of users or by themselves.
                        which Authenticates and Authorises the users to request to our application.
                        - it works in 2 layers 
                            1. Inbound - Authentication and Authorization (it supports any identity providers Ex: Amazon Cognito, Octa)
                            2. Outbound - 

3. AgentCore Memory : 👉 Persistent brain of the agent

                            What it is:
                                Stores context across interactions

                            Types of memory:
                                Short-term → current session
                                Long-term → history, learned context

                            What it enables:
                                Multi-step workflows
                                Personalized responses
                                Context-aware decisions

                            Without Memory:
                                Agent forgets everything after each step
                                Becomes a dumb chatbot
                        
                        ---video notes---
                        Enables developers to build context-aware agents by eliminating complex memory infrastructure management while providing full control over agent memory.
                        its amanaged service which provides memory at 2 levels
                        1. Short term memory - used for session duration
                        2. Long term memory - used for Summarization, semantic and User experiance

4. AgentCore Observability : 👉 Monitoring + debugging layer

                                What it is:
                                    Tracks what the agent is doing internally

                                What it provides:
                                    Logs (steps, tool calls)
                                    Traces (execution flow)
                                    Metrics (latency, failures)

                            ---video notes---
                            Gives developers complete visibility into agent workflows to trace, debug, and monitor agent's performance.
                            It monitors the Agent performance in production using Aws Cloudwatch, GenAI Observability.

5. AgentCore Gateway: 👉 The unified entry point for tools/APIs

                        What it is:
                            A standardized interface for agents to call external systems

                        What it does:
                            Connects agent → APIs → AWS services → external tools

                        Handles:
                            Authentication
                            Request/response formatting
                            Routing
                      
                      ---video notes---
                      Using AgentCore Gateway, The Agent that we built can interact with Services or API's or tools.
                      **Its a management layer between the agents and the tools**.
                        - We can also transform our existing API's (Ex: our application) into agentic tools through gateway.
                        - supports both OpenAPI and smithy API specificaion formats.
                        - It supports Lambda functions to expose to AgentCore gateway.
                        - Gateway Symantically serach the correct tool to get better outcomes (also limits and filters the tool amoung the hundreds of tools.)
                        - it supports the MCP protocol which helps the Agents to interact with AgentCore Gateway.
                        - It also supports the Authenticatin via identity providers
                        - Its like an API Gateway — but for AI agents

6. AgentCore Browser : 👉 Web interaction capability (UI automation)

                            What it is:
                                A controlled browser environment for Agents

                            What it does:
                                Open websites
                                Click buttons
                                Fill forms
                                Extract data

                        ---video notes---
                        Provides a fast, secure, cloud-based browser runtime to enable agents to interact with websites.
                        Provides cloud-based browser to enable AI Agents to interact with websites at scale.

7. AgentCore Interpretor: 👉 Execution engine for code + computation

                                What it is:
                                    A sandbox where agents can run code (typically Python)

                                What it does:
                                    Perform calculations
                                    Analyze data
                                    Transform files
                                    Run logic

                            ---video notes---
                            Enables agents to write and execute code securely in sandbox environments, enhancing their accuracy and expanding ability to solve complex end-to-end tasks.

- Which AgenticAI frameworks does AWS Bedrock AgentCore support ?
AWS Bedrock AgentCore works with custom frameworks and any open-source framework. Ex: CrewAI, LangGraph, LlamaIndex, Google ADK, OpenAI Agents SDK, and Strands Agents.

- Which Large Language Models does AWS Bedrock AgentCore support ?
AWS Bedrock AgentCore is designed to be model-agnostic, working with any foundation model in or outside of Amazon Bedrock including OpenAI, Google's Gemini, Anthropic's Claude, Amazon Nova, Meta Llama, and Mistral models.