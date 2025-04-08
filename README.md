# Building-an-AI-Powered-Financial-Analysis-Agent-with-Amazon-Bedrock

## Summary
This project guides you through the process of creating an AI-driven financial analysis agent using Amazon Bedrock. By leveraging large language models (LLMs), CloudFormation templates, and a knowledge base synced with Federal Open Market Committee (FOMC) reports, this agent can analyze economic trends, generate company portfolios, and provide actionable insights. The setup includes deploying AWS resources, integrating a knowledge base, and launching a Streamlit-based user interface on an EC2 instance. The result is a powerful tool for exploring financial data and economic policies through natural language prompts.

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
   - [1 - S3_Dataload Stack](#): Creates an S3 bucket and uploads FOMC reports.
   - [2 - Agent_Lambda Stack](#): Sets up an Amazon Bedrock agent, action group, and Lambda function.

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
- [3 - EC2_UI Stack](#): Deploys an EC2 instance for the Streamlit app.
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
- Use the example prompts provided earlier to test functionality. If an error occurs on the first try, retry the prompt.

<img width="917" alt="image" src="https://github.com/user-attachments/assets/3586b364-a6dd-4153-9898-6ad7857d7a42" />
