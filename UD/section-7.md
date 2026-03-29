##  Bedrock AgentCore Memory - Implementing Memory to our Agentic AI app

## Bedrock AgentCore Capabilities - (Short-term and Long-term memory)

(Refer slide : 26-memory-overview.png)
- LLMs are stateless : Every interaction that we have with a large language model is a new interaction, and it does not have any context of the previous interaction.
  Ex (LLM without memory) : 
    interaction 1: who are the top three tennis players in the world?.
                   --> it generates response with three names.

    interaction 1: which are the next three?
                   --> LLM will not be able to understand that we are referring to the next three tennis players.   

- To build any meaningful applications you need to include a Memory component along with a large language model.
- To build an agent application, the developer will need to use custom memory. need to use some of the open source frameworks such as Lang Chain Or Dynamo DB to maintain the statefulness of the LLM or the agent application.
- Bedrock AgentCore provides a fully managed service that gives build the memory component with few clicks (that gives your agents the ability to remain past interactions)

(Refer slide : 27-memory-flow.png)
- once the user sends a request, a corresponding session ID will be created and it will store 
  all the Chat messages + Blob.
- chat message is the user interaction.
- blob is basically the configuration details related to this memory that is being created.
- The AgentCore Memory also provides the capability of monitoring and logging enabled via the agent core observability.

Short-term memory(RAW storage):
- Once the user sends this request, a corresponding session ID will be created and stored along with chat messages as events.
- Then it will interact with the **Memory Extraction Module** that AWS has written. these interactions can be extracted and then use it by our Agentic app.
- short term memory is applicable only for the duration of the session, As soon as this particular interaction with the user ends, this short term memory exit, but you have the option to store it between 7 to 365 days.

Long-teram Memory(Vector Storage):
- long term memory extracts the data from **Memory Extraction Module** and storing it in the long term memory.
- How it extracts data ? : It uses the **Memory Consolidation and Embedding module** (it managed by AWS.) 
- **Memory Consolidation and Embedding module** converts all that conversation into embeddings and stores it in a **Vector DB** for long term storage.
- Long-Term memory Strategy (once all conversation has of session ended, you have the option to store this in long term memory in three different ways.)
    1. semantic : It's going to store a lot of factual information. (Ex: store the address ofrestaurant, store the address of a particular hotel.)
    2. Summary : Its going to summarize all that conversation and send it to LLM and store.
    3. user preference: store the user preference (Ex: I want to go on vacation to a beach)

    * next time you have a conversation and you log on to this application, it's going to retrieve the context from this **vector db** and then embed it to the conversation in that particular session.

* Key Terms to understand:
(Refer slide : 28-memory-internal.png)
- Every time we create a virtual space in the bedrock AgentCore Memory, it generates a memory ID. (In Slide --> Memory ID = abc24526)
- For each user there will be an Actor ID will be created.
- User sends the request to AgenticAI app, a session ID would be generated and all the conversation remains in that session id.
- For each conversation/request to the AgenticAI app an Event ID is created.
- All these details such as session ID, event ID, Actor ID would be stored in the **AgentCore Memory** along with the actual conversation.

To Add memory for Our AgenticCore Application:
  - Create memory - it generates an integration code ( use this code in your AgenticCore appliation- crew.py )
  - Deploy this applicaton again (after developing memory) to ECR --> AgentCore Runtime.

