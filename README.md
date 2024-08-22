## Multi-Tenant Serverless RAG Implementation on AWS using Pinecone as Vector Store

The solution is published under an Apache 2.0 license and is targeted for users who want to experiment and productionize different Gen AI use cases. The solution uses [LangChain](https://www.langchain.com/) open-source software (OSS) to configure connections to your choice of Large Language Models (LLMs) for different use cases. 

Some of the features of this application are:

-   Multi-tenant subject-specific data storage and query to isolate personal/sensitive data
-   Integration with [Amazon Bedrock](https://aws.amazon.com/bedrock/), and select third-party vector stores using knowledge base
-   Multi-LLM comparison and experimentation with metric tracking using [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/) dashboards
-   Experimentation with LLaVA (LMM) for multi-modal use case requirements - image detection capabilities

For a detailed solution implementation guide, refer to [The Generative AI Application Builder on AWS](https://docs.aws.amazon.com/solutions/latest/generative-ai-application-builder-on-aws/overview.html)

## Architecture Overview

There are 3 unique user personas that are referred to in the solution walkthrough below:

-   The **admin** are responsible for managing the content contained within the deployment.
-   The **users** represents the individuals who the use case has been deployed for. They are the consumers of the knowledge base and the customer responsible for evaluating and experimenting with the LLMs.
    
### Use Cases

Once the Deployment Dashboard is deployed, the admin user can then deploy multiple use case stacks. When a use case stack is deployed by the admin user, the following components are deployed in the AWS account:

1. Business users can log in to the use case UI. This will be modified to cater different persona's access rules
2. [Amazon CloudFront](http://aws.amazon.com/cloudfront/) delivers the web UI which is hosted in an Amazon S3 bucket.
3. The web UI leverages a WebSocket integration built using [Amazon API Gateway](https://aws.amazon.com/api-gateway/). The API Gateway is backed by a custom Lambda Authorizer function, which returns the appropriate IAM policy based on the Amazon Cognito group the authenticating user is part of.
4. [Amazon Cognito](https://aws.amazon.com/cognito/) authenticates users and backs both the Cloudfront web UI and API Gateway.
5. The LangChain Orchestrator is a collection of Lambda functions and layers that provide the business logic for fulfilling requests coming from the business user.
6. The LangChain Orchestrator leverages Parameter store and DynamoDB to get the configured LLM options and necessary session information (such as the chat history).
7. The Amazon Bedrock Knowledge base will be modified to use Pinecone instead of Amazon Kendra[The current deployment code leverages [Amazon Kendra](http://aws.amazon.com/kendra/) to run a search query to retrieve document excerpts.]
8. Using the chat history, query, and context from Pinecone, the LangChain Orchestrator creates the final prompt and sends the request to the LLM hosted on [Amazon Bedrock](https://aws.amazon.com/bedrock/) or [Amazon SageMaker](https://aws.amazon.com/sagemaker/).
9. If using a third-party LLM outside of Amazon Bedrock or Amazon SageMaker, the API key is stored in [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/) and must be obtained before making the API call to the third-party LLM provider.
10. As the response comes back from the LLM, the LangChain Orchestrator Lambda streams the response back through the API Gateway WebSocket to be consumed by the client application.
11. Using [Amazon Cloudwatch](https://aws.amazon.com/cloudwatch/), operational metrics are collected by various services to generate custom dashboards used for monitoring the deployment's health.

## Deployment

> **_NOTE:_**

-   To use Amazon Bedrock, you must request access to models before they are available for use. Refer to [Model access](https://docs.aws.amazon.com/bedrock/latest/userguide/model-access.html) in the Amazon Bedrock User Guide for more details.

## AK : TODO : Detailed deployment procedure
 - For integrating Amazon Bedrock Knowledge base with Pinecone vector store
     - Setting up data sources for multi-tenancy
     - Deriving embeddings and sync'ing with vector store
     - Securing API keys for invoking Pinecone APIs
 - Deploying RAG pipeline with Bedrock Agent
 - Setting up Amazon Cognito for
     - User authentication
     - Setting up lambda triggers for injecting custom token attributes (to achieve multi-tenancy)
