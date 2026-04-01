## Bedrock Agent Core Observability
Bedrock Agent Core Observability allows us to trace, debug and monitor Agents performance in production environment.

- The AWS Bedrock AgentCore Observability enabled using OpenTelemetry - OpenTelemetry (OTel) is an open-source, vendor-agnostic observability framework and toolkit designed to collect, process, and export telemetry data—specifically traces, metrics, and logs—from applications.

- AWS has created secure, production-ready, AWS-supported distribution of the OpenTelemetry project called AWS Distro for OpenTelemetry (ADOT) 

- observability can be enabled for agent Runtime, Memory, Gateway, Identity and built in tools.
- The Bedrock AgentCore observability goes beyond the CloudWatch metrics and logs we are used to for most of the AWS services (because of ADOT)

* (Refer slide : 17-observability.png)
The the capabilities of Agent Core Observability: 
    - **CloudWatch logs** : this is enabled both for Agents and the underlying Large Language Models(LLM) that the agents are using.
    - it also enables **CloudWatch metrics** (Latency, Throttles, Sessions,UserErrors, Errors, MemoryUsed, CPUUsed), both for agent as well as the LLM.
    - CloudWatch logs and CloudWatch metrics are conventional capabilities that are provided for most services.
    -  Bedrock AgentCore provided a new tab which has much more capabilities which is called **GenAi Observability**. it looks at observability from two    
       aspects 
       1. Model Invocation
       2. Bedrock AgentCore
    - few concepts that you need to be aware of - Session, traces and spans (Observability provides you insights on all these three)

    (Refer slide : 18-observability.png)
    - AWS has implemented Agent Core Observability through the open telemetry library and created its own distribution called as AWS Distro 
      Opentelemetry (ADOT)
    - **To enable this observability** for runtime, we need to add the AWS Opentelemetry code in dockerfile and deploy our agent application
    - Refer steps added in "AgentCore Installation and Run Guide" in the code section : 5.5.demo_app_agentcore_observability.zip 



- Bedrock Agent Core Observability also enables a lot of CloudWatch metrics, both for agent as well as the large language model.

- GenAi Observability : Bedrock AgentCore provided a new Observability which has much more capabilities
    1. model invocation
    2. Bedrock AgentCore 
        a. Session : Represent complete user Conversation
        b. Traces : Individual request-response cycles withn a session.
        c. spans : specific operations or steps within trace.
      
[Hands-On](#) : code --> 5.demo_app_agentcore_observability.zip
                 Follow the steps : AgentCore Installation and Run Guide (file availabe in in thhe same code base : 5.demo_app_agentcore_observability.zip)