# ResearchPilot: Autonomous AI Research Operating System

ResearchPilot is a premium, enterprise-grade Research Operating System designed for scientists, PhD students, analysts, and innovation teams. Moving beyond generic AI chatbots, ResearchPilot offers a beautifully crafted, highly focused workspace environment where researchers can organize literature, interact with a domain-specific autonomous agent, and extract deep insights using an advanced Retrieval-Augmented Generation (RAG) architecture.

![ResearchPilot Demo](https://raw.githubusercontent.com/boyinatulasiram/ResearchPilot/main/frontend/public/favicon.ico) <!-- Placeholder -->

## ✨ Features

- **Premium Workspace Architecture:** Segregate your literature reviews, datasets, and synthesized insights into distinct, organized projects.
- **Academic RAG Pipeline:** Intelligent ingestion of academic papers with automatic vectorization.
- **Deep Research Canvas:** A dedicated chat interface strictly bounded to your uploaded literature. The agent will decline off-topic queries and focus solely on academic synthesis.
- **Auto-Detect Knowledge Extraction:** Single-click capability to automatically analyze workspace abstracts and detect emerging trends, contradictions, and research gaps.
- **Artifact Generation:** Automatically draft comprehensive research reports, summaries, and literature reviews in Markdown, ready for download.
- **Semantic Paper Discovery:** Integration with Semantic Scholar to search for and import literature directly into your workspace.

---

## 🧠 AI Architecture & RAG Workflow

ResearchPilot utilizes a sophisticated hybrid AI architecture combining local embedding processing with cloud-based LLM inference.

### The RAG Workflow
1. **Ingestion:** When a paper is added to a workspace, its title, authors, and abstract are combined into a text chunk.
2. **Embedding:** The text chunk is converted into a 384-dimensional vector using an embedding model.
3. **Storage:** The vector, along with the paper's metadata, is stored in a Qdrant Vector Database within a collection dynamically named after the workspace ID.
4. **Retrieval:** When a user queries the Research Canvas, the query is vectorized. Qdrant performs a Cosine Similarity search to find the top 10 most relevant chunks.
5. **Synthesis:** The retrieved chunks are injected as Context into a strict system prompt and fed into the Large Language Model to synthesize an answer with proper citations.

### Agents & Models
ResearchPilot utilizes **1 Core Autonomous Agent** configured dynamically into **3 Sub-roles** based on task execution, powered by **2 Distinct Models**.

#### Models (2)
1. **Embedding Model (Local):** `Xenova/all-MiniLM-L6-v2` (via Hugging Face Transformers.js) - Handles all vector space operations natively on the backend for zero latency and high privacy.
2. **Language Model (Cloud):** `meta-llama/Meta-Llama-3-8B-Instruct` (via Hugging Face Serverless Inference) - Handles reasoning, synthesis, and JSON extraction.

#### Agent Roles (1 Core, 3 Personas)
* The `AgentService` acts as the orchestrator, adopting the following personas:
  1. **The Research Synthesizer:** Used in the Research Canvas. Enforces strict domain focus, denies off-topic queries, and generates formal markdown reports with inline citations.
  2. **The Knowledge Extractor:** Used in the Insights auto-detection. Instructed to parse multiple abstracts and strictly output formatted JSON containing *Emerging Trends*, *Contradictions*, and *Research Gaps*.
  3. **The Artifact Drafter:** Used to generate long-form, standalone literature reviews and reports based on workspace topic context.

---

## 🛠️ Technology Stack

**Frontend:**
- React 18 & TypeScript
- Vite
- Tailwind CSS (Premium Light Architecture)
- React Query (State & Cache Management)
- React Router DOM
- Lucide React (Icons)

**Backend:**
- Node.js & Express.js
- TypeScript
- MongoDB & Mongoose
- Qdrant (Vector Database)
- Hugging Face Transformers.js

---

## 🚀 Getting Started

### Prerequisites
- Node.js (v18+)
- Docker (for Qdrant Vector DB)
- MongoDB Database (Local or Atlas)
- Hugging Face API Token (`HF_TOKEN`)

### 1. Database Setup
Start the Qdrant Vector Database via Docker:
\`\`\`bash
docker run -p 6333:6333 -p 6334:6334 qdrant/qdrant
\`\`\`

### 2. Backend Setup
Navigate to the backend directory, install dependencies, and configure environment variables.
\`\`\`bash
cd backend
npm install
\`\`\`
Create a `.env` file in the `backend/` directory:
\`\`\`env
PORT=5000
MONGODB_URI=mongodb://localhost:27017/researchpilot
JWT_SECRET=your_super_secret_jwt_key
HF_TOKEN=hf_your_huggingface_token
QDRANT_URL=http://localhost:6333
\`\`\`
Start the backend development server:
\`\`\`bash
npm run dev
\`\`\`

### 3. Frontend Setup
Navigate to the frontend directory, install dependencies, and start the Vite server.
\`\`\`bash
cd frontend
npm install
npm run dev
\`\`\`

The application will be accessible at `http://localhost:5173`.

---

## 📜 License

This project is licensed under the MIT License.
