## Overview of AI Agents
Sample user request : john is looking to take a two week skiing vacation from mid December if he has adequate leaves. He also needs help with vacation planning like hotel booking, airline booking and so on. It's not a simple task like generating content or summarizing content, but it's a collection of tasks and decisions.

The application needs to check the leaf balance. If he has two weeks leave then maybe research locations for skiing, check the weather report and see if it's going to snow in December.
Book a flight, book a hotel.

- when you have user request, which are complex in nature, requiring decision making, a large 
  language model might not be the right fit in that scenario.

- So one of the limitations of the large language models can be overcome by building RAG (retrieval augmented generation) based applications by connecting the LLMs to the internal data sources.(Ex: HR Data source)

- let's say we have built an Agentic app and this request is sent to this Agentic app or agents.
    (agents are driven by large language models(LLM). So the primary brain or thinking capability that agents possess is powered by a large language model.)

    - Here we used CrewAI (Vacation planner AI Gaentic app)


characteristics of AI agents:
there are five key characteristics of AI agents.

1. Planning : Dynamic Task planning
              Ability to do planning by task decomposition. 

2. tools and actions : Access any data via API's
                        tools and actions that an AI agent can perform.
                        Ex: writing custom lambda code(to fetch whether report) in AWS and integrating with agent

3. Memory : User interaction
            stores the user interaction between the agent and the user.

4. guardrails : Toxicity and Hallucinations
                used to block user request for Toxicity and Hallucinations

5. communication: Single or multi agent


## CrewAI Refresher
What is CrewAI ?
It's the multi-agent platform, to build powerful AI agents by using any LLM and cloud platform.
it is also cloud platform agnostic. its built using python and completely independent.
its opensource.

CrewAI Key Concepts:
1. Agents : works on specialized tasks ( These agents accomplish some Tasks)

2. Tasks : tasks are defined to achieve the goal. And those tasks are assigned to different 
            agents.

3. Process : It defines how the agents collaborate amongst each other.
            it can be  either sequential or hierarchical.

4. Crew : crew organizes the overall operations to achieve the final outcome.
             you can think of this crew as a orchestrator, which works with the different agents, tasks, processes to generate the final outcome.

Agents in CrewAI : 
    Agent is an autonomous unit that can:

    - It can perform **specific tasks** --> It can search the web or Maybe it can read the data from PDF files and it can do whole bunch of specific tasks.
    
    - uses **tools** to accomplish some objectives. --> 
                                                    SerperDev tool which searches the internet.
                                                    RAG based tools where you read the data from PDF, or read the data from database and so on.

    - **communicate and collaborate** with other agents. --> Ex: So in our case, this travel agent can collaborate with the Summarizer agent  
    
    - maintain memory of interactions --> It can be current interactions or previous interactions.

autonomous unit means that it is able to make decisions independently with minimal guidance.

Agent Attributes(mandatory attribute to define to build an agent):
1. Role - it is a role that you provide to the agent, It's a AGENTS specialized function.
         It can be a travel agent, summarizer agent, coding agent.

2. Goal : The agent's purpose and motivation.
            Ex: in our case it's search web for popular tourist destinations.

3. BackStory : Agent's experience and perspective.
            Ex: in our case this travel agent maybe spend ten years as travel agent helping travelers plan their vacation. (years of previous experience doing a similar job.)

    Refer the crewAi documentation for more info.

Task in CrewAI : 
    Tasks represent the concrete work that the agent will perform with a detailed instructions and expected output.
    Ex: our use case, then our research agent is going to execute the research task. And that research task is going to be gather comprehensive information from internet on the tourist spots and history about London.

    The key task attributes (while defining a Task in CrewAI):
        Description (mandatory)
        Expected Output (mandatory)
        Name
        Agent
        Tools

Processes in CrewAI :
    processes orchestrate the execution of tasks by the agents.
    Sequential
    Hirarchial

Crew in CrewAI:
    crew represents collaborative group of agents working together to achieve a set of tasks or outcome.
    Key attributes of Crew:
        Agents (mandatory) - when you are defining the crew, you have to define all the agents that are going to be used as part of that crew.
        Tasks (mandatory) - have to define all the tasks that will be executed by these agents.
        verbose - printing out the logs.


## MCP (Model Context Protocol)

M - Large Language Model that you use to build your AI application.
C - Context of the module
    Ex: To build an HR chatbot application to answer the employee query it needs access to certain enterprise HR data or third party data.
        So when you retrieve that data and provide it to the Large Language Model, you provided here some context (HR data context).
P - Protocol means certain set of rules which allow you to exchange data between two devices.

* Its a Bridge between the Large Language Model and tools (different data sources, whether internal or external).
* MCP is based on a client-server architecture

Definition : MCP is an  open-source protocol that allows AI models (LLMs) to seamlessly connect with external data, tools, and systems. It acts as a universal, standardized **bridge between Large Language Models (LLMs) and software like databases, APIs, and file systems, eliminating the need for custom integrations for every tool.**

*  It solves the M*N custom integration problem. (Refer slide : section8-video35-slide1.png)
    - So you will have to write custom integration code (Ex: lambda) for these three applications to access the data sources. Then these three a total 
      of nine integrations.
    - MCP client connects 1 to 1 with this MCP server which connects to these three data sources. So you will have 3+3=6 implementations. So will reduce 
      the number of integration points.
    - AWS offers more than 50 plus MCP servers.

* The core components of the Model Context Protocol (MCP) (Refer slide : section8-video35-slide2.png, section8-video35-slide3.png)
    - **MCP Host**: The AI application or LLM (e.g., Claude Desktop) that acts as the user interface and coordinates operations.
                    Ex: Claude Desktop, an IDE like Cursor or VS Code with AI features or Chatbot or a custom AI app
    - **MCP Client**: Its a component within the host that manages the connection to a specific server, handling the data transfer and protocol 
                    translation.
                    Ex: Cline, Amazon Q CLI, Windsurf, Claude Desktop
    - **MCP Server**: A lightweight program that sits between the client and data sources, offering a standardized interface to resources.
                    Ex: Built by Service provider - AWS
                        The most common one is local system or Docker deployment, where you deploy the server on your laptop itself.
                        To check all the availabe servers : https://awslabs.github.io/mcp