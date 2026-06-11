# 🎓 ResearchPilot: The Ultimate Viva & Technical Defense Guide

This document contains **EVERYTHING** you need to know to confidently explain, defend, and present **ResearchPilot AI** to strict reviewers, professors, and industry professionals.

---

## 📑 TABLE OF CONTENTS
1. [Part 1 - Project Understanding](#part-1---project-understanding)
2. [Part 2 - Tech Stack Justification](#part-2---tech-stack)
3. [Part 3 - Database Design](#part-3---database-design)
4. [Part 4 - AI Architecture](#part-4---ai-architecture)
5. [Part 5 - RAG (Retrieval-Augmented Generation)](#part-5---rag)
6. [Part 6 - Embeddings](#part-6---embeddings)
7. [Part 7 - Vector Database](#part-7---vector-database)
8. [Part 8 - Similarity Search](#part-8---similarity-search)
9. [Part 9 - Agentic AI](#part-9---agentic-ai)
10. [Part 10 - Source Code Architecture](#part-10---source-code-explanation)
11. [Part 11 - Team Division](#part-11---team-division)
12. [Part 12 - Security](#part-12---security)
13. [Part 13 - Scalability](#part-13---scalability)
14. [Part 14 - Future Enhancements](#part-14---future-enhancements)
15. [Part 15 - Top 100 Viva Questions & Answers](#part-15---viva-preparation)

---

## PART 1 - PROJECT UNDERSTANDING

### 1. What problem does ResearchPilot solve?
Researchers, PhD students, and analysts suffer from "information overload." They download hundreds of PDFs, lose track of what paper said what, struggle to find connecting themes (research gaps/contradictions), and spend weeks manually drafting literature reviews. 

### 2. Why is this project useful?
It compresses weeks of literature review into hours. By acting as a "second brain," it remembers every paper in the workspace, instantly retrieves exact citations to answer questions, and automatically drafts comprehensive research artifacts based purely on verifiable facts.

### 3. Who are the target users?
Academic researchers, PhD candidates, Data Scientists, R&D teams in corporate environments, and medical professionals conducting systemic reviews.

### 4. What makes it different from ChatGPT?
ChatGPT is a generalist chatbot that suffers from **hallucinations** (making up fake citations) and has no long-term memory of 50+ uploaded PDFs. ResearchPilot strictly uses **RAG**, forcing the LLM to answer *only* using the provided workspace context, ensuring 100% factual accuracy and real citations.

### 5. What makes it different from NotebookLM?
NotebookLM is a closed-ecosystem Google product. ResearchPilot is an open-architecture platform that integrates directly with external APIs (Semantic Scholar/arXiv) to actually *discover* papers, and features an **Agentic** layer that proactively auto-detects research gaps and trends without being asked.

### 6. What makes it different from Perplexity?
Perplexity is an AI search engine for the live web. ResearchPilot is a **Research Operating System**—a persistent, organized workspace where data is curated, stored, and analyzed deeply over months of a research project.

### 7. What are the main features?
- **Workspace Isolation:** Project-specific environments.
- **Academic RAG:** High-accuracy semantic search over PDFs.
- **Auto-Knowledge Extraction:** AI autonomously detects trends, gaps, and contradictions.
- **Semantic Discovery:** Direct arXiv/Semantic Scholar API integration.
- **Artifact Generation:** AI drafting of full markdown literature reviews.

### 8. End-to-End Workflow
1. User authenticates and creates a **Workspace** (e.g., "Cancer Immunotherapy").
2. User searches Semantic Scholar via the UI and imports **Papers**.
3. The backend chunks the papers and saves **Embeddings** to Qdrant.
4. User clicks "Auto-Detect," and the AI populates the **Insights** dashboard.
5. User queries the **Research Canvas** (Chat).
6. The system retrieves vector context and the LLM synthesizes an answer with citations.
7. User generates a final **Artifact** (report) and downloads it.

---

## PART 2 - TECH STACK

### Frontend
- **React:** Component-based UI library. *Why:* Reusable components, huge ecosystem.
- **TypeScript:** Adds static typing to JS. *Why:* Prevents runtime errors, strict interfaces for API responses.
- **Vite:** Next-gen bundler. *Why:* 100x faster Hot Module Replacement (HMR) than Webpack.
- **Tailwind CSS:** Utility-first CSS framework. *Why:* Rapid styling without leaving the TSX file; enables the "Premium Light" aesthetic easily.
- **Framer Motion:** Animation library. *Why:* Smooth micro-interactions (modals, loaders) that make the app feel native and premium.
- **React Query:** Server-state management. *Why:* Handles caching, loading states, and automatic background refetching perfectly.

### Backend
- **Node.js / Express.js:** Async, event-driven runtime. *Why:* Non-blocking I/O is perfect for handling multiple heavy LLM API requests simultaneously.

### Database
- **MongoDB / Mongoose:** NoSQL document database. *Why:* Research metadata is unstructured. Some papers have DOIs, some have PMCIDs, authors arrays vary. NoSQL handles this seamlessly.

### Authentication
- **JWT (JSON Web Tokens):** Stateless auth tokens. *Why:* Server doesn't need to store session state, making it highly scalable.
- **bcrypt:** Password hashing. *Why:* Uses salting and key stretching to protect against brute-force/rainbow table attacks.

### AI Infrastructure
- **Groq:** LPU (Language Processing Unit) inference engine. *Why:* Groq provides ultra-fast inference (hundreds of tokens per second), enabling real-time chat UX.
- **GPT-OSS-120B:** Massive open-source foundation model. *Why:* Deep academic reasoning capabilities exceeding smaller 8B models.
- **BAAI/bge-base-en-v1.5:** Open-source embedding model. *Why:* Top of the MTEB (Massive Text Embedding Benchmark) leaderboard for English retrieval tasks. Generates 768-dimensional dense vectors perfectly suited for academic text.
- **Qdrant:** Rust-based Vector Database. *Why:* Blazing fast, memory-efficient, utilizes HNSW (Hierarchical Navigable Small World) graphs for instantaneous similarity search.

---

## PART 3 - DATABASE DESIGN

### Core Schemas
1. **User:** `_id`, `email`, `password` (hashed). *Purpose:* Auth.
2. **Workspace:** `_id`, `userId`, `title`, `updatedAt`. *Purpose:* The central silo. All other entities tie back to this to prevent data leakage between projects.
3. **Paper:** `_id`, `workspaceId`, `title`, `abstract`, `authors`, `url`. *Purpose:* Stores literature metadata.
4. **Conversation:** `_id`, `workspaceId`, `title`. *Purpose:* Groups chat messages into sessions.
5. **Message:** `_id`, `conversationId`, `role` (user/assistant), `content`, `sources` (Array of citations). *Purpose:* Chat history memory.
6. **Insight:** `_id`, `workspaceId`, `type` (trend/gap/contradiction), `title`, `content`. *Purpose:* Stores auto-extracted knowledge.
7. **Artifact:** `_id`, `workspaceId`, `title`, `content`, `type`. *Purpose:* Stores generated reports.

### Why MongoDB instead of PostgreSQL?
- **Schema Flexibility:** Academic APIs return wildly varying JSON structures. A rigid SQL schema would require constant migrations.
- **Fast Iteration:** In AI startups, features change rapidly. MongoDB allows inserting arrays (like `sources` in Messages) without complex JOIN tables.
- **Disadvantages:** Lacks strict ACID compliance across multiple documents (though modern Mongo supports transactions, it's slower). SQL is better if financial transactions were involved.

---

## PART 4 - AI ARCHITECTURE

### 1. What is an LLM?
A Large Language Model is a deep neural network (specifically a Transformer) trained on vast amounts of text to predict the next word (token) in a sequence, effectively learning grammar, facts, and reasoning.

### 2. Why GPT-OSS-120B over Llama 3 70B or GPT-4o?
- **vs Llama 70B:** A 120B parameter model has significantly higher capacity for complex zero-shot reasoning, crucial for understanding dense academic literature.
- **vs GPT-4o:** GPT-4o is proprietary and closed-source. Using an OSS (Open Source Software) model ensures data privacy (critical for unpublished research), avoids vendor lock-in, and reduces massive API costs at scale.

### Key Concepts
- **Tokens:** The fundamental unit of text for an AI (1 token ≈ 0.75 words). "Research" might be 1 token, "Pilot" 1 token.
- **Context Window:** The maximum memory an LLM has for a single request (e.g., 128,000 tokens). If you exceed it, the model "forgets" the beginning.
- **Temperature:** Controls randomness. ResearchPilot uses a low temperature (e.g., 0.1) because we want deterministic, factual answers, not creative fiction.
- **System Prompt:** The core underlying instructions. ResearchPilot's prompt strictly commands: *"You are an academic researcher. You MUST decline off-topic queries. You MUST cite sources."*

---

## PART 5 - RAG (Retrieval-Augmented Generation)

### What is RAG and Why does it exist?
LLMs have a "knowledge cutoff" (they don't know papers published yesterday) and they **hallucinate** facts when unsure. RAG solves this by providing the LLM with an "open-book test." Instead of relying on its internal memory, we fetch the exact documents from our database and inject them into the prompt.

### The ResearchPilot RAG Flow (Extreme Detail)
1. **Upload:** User imports an arXiv paper.
2. **Chunking:** The abstract is split into smaller overlapping pieces (chunks) of ~500 tokens. *Why:* We can't feed a whole book into the LLM; we only want the relevant paragraphs.
3. **Embedding:** Each chunk is passed to `BGE-base-en-v1.5`. The model converts the text into a floating-point array of 768 numbers.
4. **Vector DB:** These 768-d arrays are saved in Qdrant.
5. **Similarity Search:** The user asks: "What are the side effects of Drug X?" This query is ALSO converted to a 768-d vector.
6. **Context Retrieval:** Qdrant calculates the mathematical distance between the Query Vector and all Chunk Vectors, returning the top 5 closest chunks.
7. **Prompt Construction:** The backend creates a prompt: *"Answer the user based ONLY on this context: [Chunk 1, Chunk 2]"*.
8. **LLM Generation:** The LLM reads the context, synthesizes the answer, and outputs it to the frontend.

---

## PART 6 - EMBEDDINGS

### What are embeddings?
Embeddings are numerical representations of semantic meaning. In a vector space, the word "King" and "Queen" are mathematically close to each other. "Cancer" and "Tumor" are close, while "Cancer" and "Bicycle" are far apart.

### Why BAAI/bge-base-en-v1.5?
Developed by the Beijing Academy of Artificial Intelligence, BGE is explicitly trained on massive retrieval datasets. It outperforms older models like OpenAI's `text-embedding-ada-002` in retrieval accuracy while being open-source and computationally lightweight.

---

## PART 7 - VECTOR DATABASE

### What is a Vector Database?
A standard database searches for exact keyword matches (e.g., `WHERE text LIKE "%cancer%"`). A vector DB searches for **meaning** using nearest-neighbor algorithms. If you search for "malignant growth," it will find "tumor" even if the word "malignant" isn't in the text.

### Why Qdrant?
- **Why not MongoDB?** Standard Mongo is not optimized for high-dimensional math. Qdrant uses HNSW indexing written in Rust.
- **Why not Pinecone?** Pinecone is cloud-only and very expensive. Qdrant is open-source and can run locally via Docker, matching the privacy ethos of ResearchPilot.
- **Indexing:** Qdrant builds an HNSW (Hierarchical Navigable Small World) graph. Instead of comparing the query to 1,000,000 vectors (which is slow O(N)), it traverses a graph of connections, finding the nearest match in milliseconds (O(log N)).

---

## PART 8 - SIMILARITY SEARCH

Vector databases compare vectors using mathematical formulas.
- **Euclidean Distance:** The literal straight-line distance between two points in space.
- **Dot Product:** Multiplies vectors together. Measures both angle and magnitude.
- **Cosine Similarity:** Measures the **angle** between two vectors, regardless of their length (magnitude). 

**Which does ResearchPilot use?**
**Cosine Similarity.** In NLP text embeddings, the *length* of the vector usually just represents the length of the text. The *angle* represents the actual topic/meaning. Therefore, Cosine is the industry standard for text retrieval.

---

## PART 9 - AGENTIC AI

### Chatbot vs. Agent
A chatbot simply replies to a prompt (Reactive). An **Agentic AI** has autonomy, a goal, and access to tools (Proactive). 

### How ResearchPilot utilizes Agentic Concepts
Our `AgentService` acts as a primitive Planner. It doesn't just chat. When the user clicks "Auto-Detect," the Agent operates as a **Knowledge Extractor Tool**. It is given the autonomy to read 50 abstracts, decide on its own what constitutes a "Contradiction," format the output strictly as JSON, and pipe it directly into the MongoDB database without user intervention.

---

## PART 10 - SOURCE CODE EXPLANATION

ResearchPilot uses a **Modular Monolith MVC Architecture**.

### Folder Structure
```text
backend/
 ├─ src/
 │   ├─ app.ts               # Express configuration, CORS, Error handling
 │   ├─ server.ts            # Entry point, DB connection, port listening
 │   ├─ middleware/          # Auth guards, Error parsers
 │   ├─ modules/             # Feature-based folder structure
 │   │   ├─ workspace/
 │   │   │   ├─ workspace.routes.ts      # API endpoint definitions
 │   │   │   ├─ workspace.controller.ts  # Request/Response handling
 │   │   │   ├─ workspace.service.ts     # Business logic & DB calls
 │   │   │   └─ workspace.model.ts       # Mongoose Schema
```

### Request Flow
1. **Route:** Frontend hits `GET /api/workspace`. Route file catches it.
2. **Middleware:** `auth.middleware` checks the JWT. If valid, attaches `req.user`.
3. **Controller:** Extracts `userId`, calls `workspaceService.getWorkspaces(userId)`.
4. **Service:** Runs `Workspace.find()`. Also runs `Paper.countDocuments()`.
5. **Controller:** Wraps data in an `ApiResponse` class and sends HTTP 200 to frontend.

---

## PART 11 - TEAM DIVISION (4 Members)

If asked how your team built this:

1. **Member 1 (AI & Search Architect):** 
   - Configured Qdrant Docker container.
   - Built the RAG pipeline (`embedding.service.ts`, `retrieval.service.ts`).
   - Wrote strict System Prompts to prevent hallucination.
2. **Member 2 (Backend Engineer):** 
   - Set up Express, MongoDB, and MVC folder structure.
   - Built JWT Authentication middleware.
   - Wrote CRUD APIs for Workspaces, Papers, and Artifacts.
3. **Member 3 (Frontend Architect):** 
   - Set up Vite, React, and Tailwind config.
   - Built the UI layout (Sidebar, Premium Cards, Modals).
   - Implemented React Query for caching API calls.
4. **Member 4 (Integration & UX Engineer):** 
   - Integrated Semantic Scholar API for paper discovery.
   - Built the Chat Canvas interface with auto-scrolling.
   - Connected the "Auto-Detect Insights" UI button to the backend Agent.

---

## PART 12 - SECURITY

- **Authentication:** Users log in -> Server verifies password -> Server creates a JWT (JSON Web Token) signed with a `JWT_SECRET`. The token contains the `userId`. The frontend sends this token in the `Authorization` header of every request.
- **Authorization:** `workspace.service.ts` ALWAYS queries by `{ _id: workspaceId, userId: req.user.id }`. This is called **Tenant Isolation**. A user cannot access another user's workspace by guessing the ID.
- **Password Hashing:** Passwords are never stored in plain text. `bcrypt.hash()` converts "password123" into an irreversible string like `$2b$10$X8...`.

---

## PART 13 - SCALABILITY

### Current Setup
A monolithic Node.js app and a single Qdrant instance. Fine for hundreds of users.

### Future Scaling
1. **Horizontal Scaling:** Because we use JWT (Stateless auth), we can spin up 10 Node.js servers behind an AWS Load Balancer. No server needs to remember session data.
2. **Redis Caching:** Add Redis to cache Semantic Scholar API searches to prevent rate-limiting and speed up discovery.
3. **Microservices:** If the AI processing gets too heavy, extract `AgentService` into a separate Python/FastAPI microservice running on GPU-accelerated hardware.

---

## PART 14 - FUTURE ENHANCEMENTS

- **v2 (Multimodal Research):** Use Vision-Language Models (like LLaVA) to understand and retrieve charts, graphs, and tables from PDFs, not just text.
- **v3 (Knowledge Graphs):** Convert vector context into a Neo4j Graph Database to map complex relationships (e.g., "Protein A" -> *inhibits* -> "Enzyme B").
- **v4 (Team Collaboration):** Add WebSockets (Socket.io) for real-time multiplayer cursor sharing and shared workspace chats.

---

## PART 15 - VIVA PREPARATION
*(Study these extensively!)*

> [!TIP]
> **Interviewer Strategy:** Interviewers usually test 3 things: 
> 1. Did you actually build it? (Code architecture questions)
> 2. Do you understand the math/theory? (Vector/AI questions)
> 3. Why did you make these choices? (System design questions)

### 🟢 Beginner / Project Questions
**Q1: What is ResearchPilot?**
A: An AI-powered Research Operating System that uses RAG to allow users to securely organize literature, chat with their specific documents, and automatically extract deep insights without hallucinations.

**Q2: What is the tech stack?**
A: MERN-inspired but modernized: React, Vite, Tailwind frontend. Node, Express, MongoDB backend. Qdrant for the vector database, Groq/GPT-OSS for the LLM, and BAAI/BGE for embeddings.

**Q3: How do you authenticate users?**
A: Using JSON Web Tokens (JWT). Upon successful login via bcrypt password verification, the server issues a signed token, which the frontend stores and attaches to API request headers.

**Q4: What is Vite and why use it over Create React App (CRA)?**
A: Vite uses native ES modules to serve code during development, making server start and Hot Module Replacement almost instantaneous. CRA bundles the entire app on every save, which is slow.

### 🟡 Intermediate / AI & DB Questions
**Q5: Explain RAG in simple terms.**
A: Retrieval-Augmented Generation. Instead of relying on an AI's internal memory (which might be outdated or wrong), we first search our database for the exact documents related to the user's question, paste those documents into the prompt, and tell the AI to summarize them.

**Q6: Why did you use MongoDB instead of SQL?**
A: Academic papers have highly variable schema—some have DOIs, some don't; authors are dynamic arrays. MongoDB's document structure handles this JSON-like flexibility perfectly without requiring rigid table joins.

**Q7: What is an Embedding?**
A: It is a mathematical representation of a text's semantic meaning, formatted as an array of floating-point numbers. Words with similar meanings will be placed closer together in this multi-dimensional space.

**Q8: Why not just search MongoDB using text search?**
A: Standard text search looks for exact keyword matches. Vector search understands *meaning*. If a paper mentions "cardiovascular disease" and the user searches "heart problems", vector search will find it. Text search will fail.

### 🔴 Advanced / System Design Questions
**Q9: How does Cosine Similarity work?**
A: It calculates the cosine of the angle between two vectors in a multi-dimensional space. A value of 1 means they point in the exact same direction (identical meaning), 0 means they are orthogonal (unrelated), and -1 means opposite. It ignores vector magnitude, focusing purely on semantic orientation.

**Q10: Why did you choose Qdrant?**
A: It is written in Rust, making it extremely memory-safe and fast. It supports HNSW indexing for incredibly fast approximate nearest neighbor search, and it is open-source, allowing us to self-host unlike Pinecone.

**Q11: What is the Context Window of an LLM and how did you manage it?**
A: The context window is the maximum number of tokens an LLM can process at once. To prevent exceeding it, we chunk documents during ingestion (e.g., 500 tokens per chunk) and only retrieve the top *K* (e.g., top 5) most relevant chunks during a RAG query.

**Q12: How did you implement "Auto-Detect Insights"?**
A: The backend queries MongoDB for all paper abstracts in a workspace. It maps them into a single context string, feeds it to the LLM with a highly rigid system prompt demanding a strict JSON array output containing specifically typed objects (trend, contradiction, gap). The backend parses the JSON and writes it to the Insight database collection.

### ☠️ Trick / Stress-Test Questions
**Q13: If Groq/LLM goes down, does your app crash?**
A: The AI functionality (chat, insights, artifact generation) would fail, but the core Operating System—creating workspaces, discovering papers via Semantic Scholar, and reading metadata—remains entirely functional because it relies purely on our Node backend and MongoDB.

**Q14: Are you storing user passwords safely? What if the DB leaks?**
A: Yes, using `bcrypt`. Even if the DB leaks, hackers cannot see the passwords because they are one-way hashed with unique salts, making rainbow-table attacks computationally unfeasible.

**Q15: Why didn't you use LangChain?**
A: LangChain adds a heavy abstraction layer that can be hard to debug and adds latency. By building our own custom RAG pipeline in the `AgentService` and `RetrievalService`, we maintain complete control over the prompts, vector DB queries, and token usage, leading to a leaner, faster production app.

### 🌟 Top Tip for the Presentation
If the panel asks a question you don't know the exact syntax for, pivot to the architecture. *Example:* "I don't recall the exact Express method, but the architectural flow is that the Route hits an Auth Middleware, ensuring the JWT is valid, before passing the payload to the Controller." Architecture knowledge trumps syntax knowledge every time!
