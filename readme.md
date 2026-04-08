* What is Amazon Bedrock AgentCore ?   
It is the Serverless agentic platform for building, deploying, and operating highly effective agents securely at scale using any framework and foundation model. 

- Its a fully managed service that makes FMs (foundation models) from leading AI startups and Amazon availabe via API, so that you can choose from wide range of FMs (Foundation Models) to find the model that suited for your usecse.

* What is LLM models ?   
A Large Language Model is a deep learning model trained on massive text datasets to understand and generate human language. 
Using deep learning and neural networks, LLMs predict the next likely word in a sequence to create contextually relevant content, such as answering questions, translating languages, and writing code.   

- Key LLMs on Bedrock: Anthropic Claude, Meta Llama, Amazon Titan, Mistral, Cohere.   

LLM’s role in AgentCore:   
    The LLM is the “brain” — it does reasoning, planning, tool selection, and final response generation. AgentCore orchestrates everything around the LLM   
    
* What is foundation models (FMs) ?   
These are large-scale AI LLM models trained on vast, typically unlabeled data, acting as the versatile base (or foundation) for many AI applications
In AWS, LLMs are exposed as Foundation Models (FMs) via ***Amazon Bedrock***  

* what is RAG ?   
RAG (Retrieval-Augmented Generation) is the process of optimizing LLM output so it references an authoritative Knowledge Base outside its training data before generating a response. 
    - It solves a core LLM problem: models have a knowledge cutoff and don't know your private/company data.
    Ex: Getting data from Enterprise DB instead of guessing

- ***Amazon Bedrock*** Knowledge Base implements the entire RAG workflow   
    1. Ingestion phase (one-time / scheduled)
    2. Query-time phase (every request)

- RAG Integration with AgentCore:
    1. A Knowledge Base is exposed as a tool inside AgentCore’s Gateway.
    2. The agent (orchestrated by the LLM) decides when to call the Knowledge Base (intelligent retrieval, not always-on).
    3. This creates agentic RAG – the agent can do multi-step reasoning + retrieval.

- How RAG works:   
    1. Retrieve relevant documents/chunks from an external knowledge base (vector search). - one-time / scheduled  
    2. Stuff those chunks into the LLM prompt as context.   
    3. LLM generates an answer grounded in real data + cites sources.

- Quick interview tips:
    - LLM = brain; 
    - AgentCore = production runtime + orchestration layer; 
    - Knowledge Base = RAG engine.
    - Always mention the ***orchestration loop*** (pre-process → reason → tool/KB call → observe → repeat).
    - Highlight agentic RAG vs simple RAG (agent decides when/how to retrieve).
    - Common follow-up: “How do you handle hallucinations?” → “RAG + Guardrails + source citation + AgentCore Policy.”

- What is AI hallucinations ?   
these are incorrect or misleading results that AI models generate. These errors can be caused by a variety of factors, including insufficient training data, incorrect assumptions made by the model, or biases in the data used to train the model. AI hallucinations can be a problem for AI systems that are used to make important decisions, such as medical diagnoses or financial trading.

Agentic AI frameworks (most famous)
1. AutoGen
2. CrewAI
3. LangGraph   
4. LangChain

- How to Choose:   
    - Complexity & Control: Use ***LangGraph*** for complex, stateful workflows.   
    - Speed & Simplicity: Use ***Agno*** or ***CrewA***I for faster development.   
    - Enterprise/Microsoft Stack: Use ***Semantic Kernel***.   
    - Data-Heavy Tasks: Use ***LlamaIndex***.

- MCP(Model Context Protocol) : Its communication protocol between ***Agent*** and ***Tools***.

- What is **GenerativeAI** ?   
Generative AI creates new content (text, images, code) in response to prompts, acting as a reactive tool.

What is **AgenticAI** ?   
Agentic AI acts proactively as an ***Autonomous agent***, it breaking down complex goals into multi-step, independent actions using tools.

What is **AI-Agents** ?    
AI Agents are specialized, often standalone, ***software programs designed to perform specific, predefined tasks*** (Ex: such as booking a meeting).
    AI agents are softwares, while Agentic AI is the system that coordinates them.

- Open Source AgenticAI Frameworks (actively used frameworks as below):
    1. General-Purpose Agent Frameworks (Most Popular)
        These are the ones most engineers start with.

        🔹LangChain : it has Huge ecosystem (tools, memory, chains)   
            👉 Use when: You want full control + ecosystem

        🔹LlamaIndex : Best-in-class for RAG (Retrieval-Augmented Generation)   
            👉 Use when: Your agent depends heavily on data retrieval

        🔹Haystack : Mature NLP pipeline framework evolving into agents
    
    2. Agent-Native Frameworks (Built Specifically for Agents):
        These are closer to what “AgentCore” is trying to solve.

        🔹 AutoGPT : First “fully autonomous agent” concept   
            👉 Reality: Great for demos, weak for production
        
        🔹 CrewAI : Clean abstraction for multi-agent systems   
            👉 Use when: You want “team of agents” design

        🔹 SuperAGI: Tries to be a full agent platform (like AWS Bedrock)

        🔹 MetaGPT: Simulates a software company of agents

    3. Research / Advanced Agent Frameworks
        These push the boundaries.

        🔹 LangGraph : Built by LangChain team to fix its own problems          
            👉 This is closer to real production-grade agents

        🔹 DSPy: Treats prompts like programs that can be optimized

⚖️ Brutal Comparison (What Actually Matters)
| Category                | Best Framework  |
| ----------------------- | --------------- |
| Beginner / ecosystem    | LangChain       |
| RAG-heavy apps          | LlamaIndex      |
| Multi-agent systems     | CrewAI          |
| Production control      | LangGraph       |
| Research / optimization | DSPy            |
| Enterprise (non-AWS)    | Semantic Kernel |

🧠 Key Insight (Most People Miss This)   
    ❗ Framework choice matters less than agent design patterns   
        Even the best framework won’t save you if:   
            Your tool definitions are bad   
            Your prompts are weak   
            Your workflows are unclear