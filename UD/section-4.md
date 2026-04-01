## Deploy our AgenticAI application to Bedrock AgentCore

* (Refer slide :16-deploy-agenticai-to-bedrock.png)
- Build the agent application on our local system.
- add some agent core runtime decorator.
    **@app.entrypoint**
    def agent_invocation(payload, context):  --> you can rename this to anything, here the "decorator" (@app.entrypoint) is important.
        ...
        ...
- Create a Docker file.
- create a Docker image and push it to the Amazon ECR service.
- we're using this image from ECR to deploy our application to the Amazon Bedrock AgentCore service.
- once its deployed, Bedrock AgentCore provide an invocation endpoint (POST).
    - invocation post (POST) would be where the user can send its request and get a response.
    - the ping can be used for health check.
- Bedrock AgentCore provides invocation code in Python(Boto3).
- by using this invocation code we're going to write AWS Lambda function, which then integrate it with the AWS API gateway.
- at last we're going to integrate that with Streamlit (or any UI developed by developer) so that our end user can access this through an UI.

[Hands-On](#) : code --> 4.demo_app_agentcore_runtime.zip
                Follow the steps : AgentCore Installation and Run Guide (file availabe in same code base : 4.demo_app_agentcore_runtime.zip)
                Refer Lambda function code sequentially in screenshots : lambda_function-1.png and lambda_function-2.png
                create api gateway post method (because our agentCore accepts POST method) : api-gateway.png
                Once you created the API gateway, test it with Postman
                once postmen gets the proper response, integrate the API gateway with UI (for our case its streamlitUI-add the apigateway URL in UI code : add-api-gateway-url-to-ui.png)