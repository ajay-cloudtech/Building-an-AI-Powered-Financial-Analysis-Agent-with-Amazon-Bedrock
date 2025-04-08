# Building-an-AI-Powered-Financial-Analysis-Agent-with-Amazon-Bedrock

## Summary
This project guides you through the process of creating an AI-driven financial analysis agent using Amazon Bedrock with Retrieval-Augmented Generation (RAG). By leveraging large language models (LLMs), CloudFormation templates, and a knowledge base synced with Federal Open Market Committee (FOMC) reports, this agent can retrieve relevant data to analyze economic trends, generate company portfolios, and provide actionable insights. The setup includes deploying AWS resources, integrating a knowledge base, and launching a Streamlit-based user interface on an EC2 instance. The result is a powerful tool for exploring financial data and economic policies through natural language prompts.

<img width="347" alt="Bedrock_Model_Arch" src="https://github.com/user-attachments/assets/5093ad7e-286a-4369-940c-500572b35dd6" />

## Setup

### Grant LLM Access
To begin, you’ll need to enable access to the required large language models in Amazon Bedrock.

1. **Navigate to Amazon Bedrock Console**: Log in to the AWS Management Console and go to the Amazon Bedrock service.
2. **Access Model Settings**: On the left sidebar, scroll down and select **Model access**.
3. **Enable Specific Models**: Click the **Enable specific models** button on the right.
4. **Select Models**:
   - Check the box for **Amazon: Titan Embeddings G1 - Text** (if not already enabled).
   - Check the box for **Anthropic: Claude 3 Haiku**.
5. **Request Access**: Scroll to the bottom right and click **Request model access**. This may take a few minutes to process.
6. **Verify Access**: Once complete, ensure the access status for both models shows as green with **Access granted**.
7. **Submit Configuration**: Review your settings and submit.

<img width="715" alt="LLM_Models" src="https://github.com/user-attachments/assets/00b01d88-21d6-41a2-8eae-eb97dce20586" />

### Deploy Resources via CloudFormation
Next, deploy the necessary AWS resources using provided CloudFormation templates (YAML files).

1. **Download Templates**:
   - [1 - S3_Dataload Stack](https://github.com/ajay-cloudtech/Building-an-AI-Powered-Financial-Analysis-Agent-with-Amazon-Bedrock/blob/main/1-s3-dataload-template.yaml): Creates an S3 bucket and uploads FOMC reports.
   - [2 - Agent_Lambda Stack](https://github.com/ajay-cloudtech/Building-an-AI-Powered-Financial-Analysis-Agent-with-Amazon-Bedrock/blob/main/2-bedrock-agent-lambda-template.yaml): Sets up an Amazon Bedrock agent, action group, and Lambda function.

2. **Deploy via AWS CloudFormation**:
   - From the AWS Management Console, search for **CloudFormation** and navigate to the service.
   - Select **Create stack** > **With new resources (standard)**.
   - Choose **Upload a template file**, then upload the first template (S3_Dataload).
   - Provide a stack name (e.g., `s3-dataload`), keep the default instance type (`t3.small`), and click **Next**.
   - On the **Configure stack options** page, leave defaults and click **Next**.
   - Acknowledge the capabilities warning and submit.
   - Once the first stack is complete (status: `CREATE_COMPLETE`), repeat the process for the second template (e.g., name it `agent-lambda`).
  
  <img width="781" alt="image" src="https://github.com/user-attachments/assets/037c498b-0b8f-4ed5-a718-53fbdf49daa7" />
<img width="775" alt="image" src="https://github.com/user-attachments/assets/e428ad16-6b7f-440a-aafe-ac2c03f66208" />
<img width="701" alt="image" src="https://github.com/user-attachments/assets/8886817c-59c1-4345-95b4-ed4abbf42c74" />
  
### Create Knowledge Base and Add to Agent
Set up a knowledge base and integrate it with the Bedrock agent.

1. **Access Bedrock Console**: From the AWS Management Console, search for **Bedrock** and select it.
2. **Create Knowledge Base**:
   - Under **Builder tools** on the left, select **Knowledge bases**.
   - Click the orange **Create knowledge base** button.
   - Use the default name or enter your own, then click **Next**.
3. **Sync S3 Bucket**:
   - Link the S3 bucket `knowledgebase-bedrock-agent-{alias}` to the knowledge base.
   - For the embedding model, select **Titan Embeddings G1 - Text V1.2**, leave other options as default, and click **Next**.
4. **Review and Create**:
   - Review your configuration and click **Create knowledge base**.
   - Wait for the blue banner to turn green, indicating the vector database setup is complete.
5. **Sync Data**:
   - Scroll to the **Data source** section, select your knowledge base, and click **Sync**.
   - Wait for the green banner confirming the sync is complete.
6. **Integrate with Agent**:
   - Under **Builder tools**, select **Agents**, then choose the agent created during deployment.
   - Click **Edit in Agent Builder**, scroll to **Knowledge base**, and click **Add**.
   - Provide instructions:  `This knowledge base contains information for understanding data sets related to economic trends, company financials, and policy decisions made during Federal Open Market Committee meetings.`
   - - Review and click **Add**.
- Save changes by clicking **Save**, then **Prepare**, and finally **Save and exit**.

<img width="482" alt="image" src="https://github.com/user-attachments/assets/549c12c9-37e0-4976-9079-e434812500aa" />

### Setup and Run Streamlit App on EC2
Deploy a Streamlit UI for interacting with the agent.

1. **Download Template**:
- [3 - EC2_UI Stack](https://github.com/ajay-cloudtech/Building-an-AI-Powered-Financial-Analysis-Agent-with-Amazon-Bedrock/blob/main/3-ec2-streamlit-template.yaml): Deploys an EC2 instance for the Streamlit app.
2. **Deploy via CloudFormation**:
- Navigate to **CloudFormation** in the AWS Management Console.
- Select **Create stack** > **With new resources (standard)**.
- Upload the EC2_UI template, name it (e.g., `ec2-streamlit`), keep the default instance type (`t3.small`), and proceed through the steps, acknowledging capabilities.
3. **Edit Agent IDs**:
- Go to the **EC2 Instances** console, select the instance (e.g., `EC2-Streamlit-App`), and connect via **EC2 Instance Connect**.
- If connection fails due to CIDR range issues, redeploy the template with the correct range for your region (e.g., `18.206.107.24/29` for us-east-1).
- Run: `sudo vi app/streamlit_app/invoke_agent.py`.
- Press `i` to edit, update the **AGENT ID** and **Agent ALIAS ID**, then save with `:wq!`.
4. **Run Streamlit**:
- Execute: `streamlit run app/streamlit_app/app.py`.
- Copy the external URL provided and open it in a browser to access the app.
5. **Test the App**:
- Use example prompts to test functionality. `Create a portfolio with 3 companies in the real estate industry`

<img width="917" alt="image" src="https://github.com/user-attachments/assets/3586b364-a6dd-4153-9898-6ad7857d7a42" />

## Definitions
- **Amazon Bedrock**: A managed service for building and scaling generative AI applications using foundation models.
- **Agent**: An AI-powered entity in Bedrock that processes queries, retrieves data from a knowledge base, and executes actions like portfolio generation.
- **Knowledge Base**: A repository of structured data (e.g., FOMC reports) that supports RAG by providing information for the agent to retrieve and use.
- **Retrieval-Augmented Generation (RAG)**: A technique where an AI retrieves relevant data from a knowledge base to enhance the accuracy and context of its generated responses.
- **CloudFormation**: An AWS service for provisioning and managing infrastructure as code through templates.
- **Streamlit**: An open-source Python framework for creating interactive web applications with minimal coding.
- **EC2 (Elastic Compute Cloud)**: An AWS service providing scalable virtual servers to host applications like the Streamlit UI.
- **Large Language Model (LLM)**: An AI model (e.g., Claude 3 Haiku) trained on vast text data to understand and generate human-like responses.
- **S3 (Simple Storage Service)**: An AWS object storage service used to store and retrieve data like the FOMC reports.

## How It Works

When a user submits a prompt, such as `What were the key economic trends in January 2023?`, the system processes it as follows:

1. **Prompt Received by the Agent**:  
   The AI agent, hosted on Amazon Bedrock, captures the user’s query. It is powered by a large language model (e.g., Anthropic Claude 3 Haiku) to understand and respond to natural language inputs.

2. **Retrieval-Augmented Generation (RAG) Engages**:  
   To ensure accurate and data-driven responses, the agent employs Retrieval-Augmented Generation (RAG). Instead of relying solely on the LLM’s general knowledge, RAG retrieves relevant information from the knowledge base to augment the response.

3. **Knowledge Base Query (Vector Search)**:  
   - The knowledge base contains market data (e.g., economic reports) stored in an S3 bucket, converted into vector embeddings using Amazon Titan Embeddings G1 - Text.
   - Bedrock manages the vector storage and retrieval,  leveraging Amazon OpenSearch Serverless as its backend. OpenSearch Serverless indexes these embeddings for efficient similarity searches.
   - When the agent queries for “January 2023 trends,” the system performs a vector search to identify the most relevant data chunks—e.g., a report noting “inflation rose in January 2023”—and retrieves them for the agent.

4. **Response Generation**:  
   The agent combines the retrieved data with the user’s prompt and processes it through the LLM. Bedrock orchestrates this interaction, ensuring the model generates a precise and contextually relevant response, such as:  
   `In January 2023, key economic trends included rising inflation and tighter monetary policies`
