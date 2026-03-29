## Bedrock Agent Core Observability
Bedrock Agent Core Observability allows us to trace, debug and monitor agents performance in production environment.

- The AWS Bedrock AgentCore Observability enabled using OpenTelemetry (Open-source observability framework)

- AWS has created its own distribution called as AWS Open telemetry Telemetry distro (ADOT)

- observability can be enabled for agent runtime, memory, gateway identity and built in tools.
- The bedrock agent core observability goes beyond the CloudWatch metrics and logs we are used to for most of the AWS services.

* (Refer slide : 17-observability.png)
The the capabilities of Agent Core Observability: 
    - **CloudWatch logs** : this is enabled both for agents and the underlying large language models(LLM) that the agents are using.
    - it also enables **CloudWatch metrics** (Latency, Throttles, Sessions,UserErrors, Errors, MemoryUsed, CPUUsed), both for agent as well as the large 
      language model.
    -  CloudWatch logs and CloudWatch metrics are conventional capabilities that are provided for most services.
    -  Bedrock AgentCore provided a new tab which has much more capabilities which is called GenAi Observability. it looks at observability from two    
       aspects 
       1. Model Invocation
       2. Bedrock AgentCore
    - few concepts that you need to be aware of - Session, traces and spans (Observability provides you insights on all these three)

    (Refer slide : 18-observability.png)
    - AWS has implemented Agent Core Observability through the open telemetry library and created its own distribution called as AWS Distro 
      Opentelemetry (ADOT)
    - To enable this observability for runtime, we need to add the AWS Opentelemetry code in dockerfile and deploy our agent application
    - Refer steps added in "AgentCore Installation and Run Guide"



- Bedrock Agent Core Observability also enables a lot of CloudWatch metrics, both for agent as well as the large language model.

- GenAi Observability : Bedrock Agent Core provided a new Observability which has much more capabilities
    1. model invocation
    2. Bedrock AgentCore 
        a. Session : Represent complete user Conversation
        b. Traces : Individual request-response cycles withn a session.
        c. spans : specific operations or steps within trace.