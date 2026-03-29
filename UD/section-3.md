- Amazon Bedrock Agent Core is more focused on the deployment aspect, rather than helping you build an Agentic AI app. So it's a prerequisite to build an app before you can start deploying it.

- What is CrewAI ?
CrewAI is an open-source **framework for building multi-agent AI systems** where different agents have defined roles (like a team) and collaborate to complete tasks.

👉 It focuses on role-based collaboration (e.g., researcher, writer, reviewer) and structured workflows, making it easier to design agents that work together instead of a single monolithic agent.

Go To : section-8.md

* (Refer slide : 15-agentic-app-crewai.png)
    - user request will be processed by a "travel research agent", which will have a large 
      language model.
    - "serperDev" is a tool from Google, allows you to search the internet for a particular   
       topic.
    -  The key task of this travel research agent is going to be search the internet based on 
       the user input.
    - Once it searches the internet, it's going to send that raw input to a "summarizer agent", 
      which will be again driven by a large language model and will follow a sequential process.
    - the Summarizer agent will process the input received by the travel agent, summarize and
      send the final report back to our end user.

[Hands-On](#) : code --> 3.demo_app_crewai.zip
                 Follow the steps : CrewAI Installation and Run Guide