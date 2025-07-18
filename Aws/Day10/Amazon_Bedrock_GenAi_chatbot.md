
## 🤖 DevOps Chatbot Using AWS Bedrock + OpenSearch + S3 (No Code, No API)



![Bedrock_genai](https://github.com/user-attachments/assets/e834d74a-cb2b-4f43-9a51-9090c0f16a02)

### 🔐 1. Create a Non-Root IAM User

1. Go to IAM in AWS Console.
2. Create a new user (e.g., `chatbot-builder`) with the following permissions:

   * `AmazonS3FullAccess`
   * `AmazonBedrockFullAccess`
   * `AmazonOpenSearchFullAccess`
3. Log in to AWS Console as this IAM user.

---

### 🗂 2. Upload `devops.pdf` to S3

1. Create a bucket (e.g., `my-devops-docs`) using the console.
2. Upload `devops.pdf` to that bucket.

   * Go to the bucket → **Upload** → Select file → Upload.

---

### 🧠 3. Create Bedrock Knowledge Base (Console)

1. Go to **Amazon Bedrock → Knowledge Bases** → Click **Create knowledge base**.
2. Fill the following:

   * **Name**: `devops-kb`
   * **Data Source**: Choose S3 and select the bucket containing `devops.pdf`
   * **Vector Store**: Select **Amazon OpenSearch Serverless**
   * **Embedding Model**: `Amazon Titan Text Embedding G1`
3. Click **Create and Ingest** to index the document.
4. Wait until ingestion is complete (green checkmark ✅).

---

### 🔎 4. Verify Vector Store in OpenSearch

1. Go to **Amazon OpenSearch → OpenSearch Dashboards**.
2. Navigate to **Indexes** under **Discover** or **Dev Tools**.
3. Confirm that an index like `bedrock-kb-*` is created (used for vector embeddings).
4. Optionally inspect the vector embeddings stored there.

---

### 💬 5. Test the Chatbot Using Bedrock Chat Playground

1. Go to **Amazon Bedrock → Chat Playground**.
2. In **Model** select:

   * Model provider: **Anthropic** or **Amazon**
   * Model: e.g., **Claude 3 Haiku**, **Claude 3 Sonnet**, or **Titan Text Express**
3. Enable **Knowledge Base**:

   * Click the ⚙️ (Settings) icon.
   * Enable **Use a knowledge base**.
   * Select your knowledge base: `devops-kb`
4. Now ask questions like:

   * ❓ *“What is DevOps?”*
   * ❓ *“What are the benefits of CI/CD in DevOps?”*
   * ❓ *“Explain DevOps tools like Jenkins and Docker”*
5. The chatbot will generate answers **grounded in your PDF**, along with **citations**.

---

### ✅ Summary Architecture

```
[User Prompt]
     ↓
[Bedrock Chat Playground]
     ↓
[Knowledge Base (uses OpenSearch for retrieval)]
     ↓
[LLM generates contextual reply with citations from devops.pdf]
```

---

### 🔒 Why Use Non-Root IAM + OpenSearch?

* **Security**: Principle of least privilege using IAM user.
* **Separation of duties**: No root access required for production.
* **Performance**: OpenSearch vector DB enables fast semantic search.

-
