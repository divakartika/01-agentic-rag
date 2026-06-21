# LLM Zoomcamp - Homework 1: Agentic RAG

This repository contains the solution and implementation for **Homework 1: Agentic RAG** from the [LLM Zoomcamp](https://github.com/DataTalksClub/llm-zoomcamp/tree/main) course by [DataTalksClub](https://datatalks.club/).

The project demonstrates the core differences between a plain Retrieval-Augmented Generation (RAG) pipeline and an autonomous **Agentic Loop** using function calling and tool execution.

## 🚀 Project Overview

The objective of this project is to build a course teaching assistant capable of answering student queries using a course FAQ dataset. While a traditional RAG pipeline struggles with user typos or multi-step reasoning, this implementation leverages an intelligent agent that can seamlessly recover from search failures (e.g., handling spelling mistakes like `"Olama"` vs `"Ollama"`).

### Key Features
- **Data Ingestion:** Automatically fetches the official DataTalksClub course FAQ data from a JSON endpoint.
- **Lexical Search Indexing:** Uses `minsearch` (a lightweight, in-memory text search engine) to index questions, sections, and answers.
- **Agentic Loop Workflow:** Implements tool registration and autonomous execution using `toyaikit`.
- **Error Recovery:** The agent evaluates its own search results, adjusts query keywords dynamically, and performs multiple iterative searches to find accurate answers.

---

## 🛠️ Tech Stack & Dependencies

The project is built entirely in Python inside a Jupyter Notebook environment using the following tools:
- **Python (3.14+)**
- **Jupyter Notebook**
- **`minsearch`**: In-memory keyword search framework for text indexing.
- **`toyaikit`**: An experimentation framework wrapping the agentic loop, message histories, and inline tool visualization.
- **OpenAI API Client**: Powers the underlying reasoning engine using modern `responses` schema definitions (`gpt-5.4-mini`).
- **`python-dotenv`**: Secure API key configuration management.

---

## 🔧 Installation & Setup

1. **Clone the Repository:**
   ```bash
   git clone [https://github.com/your-username/your-repo-name.git](https://github.com/your-username/your-repo-name.git)
   cd your-repo-name
   ```

2. **Install Dependencies:**
   It is highly recommended to manage the environment using `uv` or standard `pip`:
   ```bash
   uv add requests minsearch openai jupyter python-dotenv toyaikit
   # OR
   pip install requests minsearch openai jupyter python-dotenv toyaikit
   ```

3. **Configure Environment Variables:**
   Create a `.env` file in the root directory to store your API credentials securely:
   ```bash
   OPENAI_API_KEY=sk-your-actual-api-key-here
   ```

4. **Launch Jupyter Notebook:**
   ```bash
   uv run jupyter notebook
   ```
   Open `sandbox.ipynb` to execute the cells.

---

## 💡 How it Works: RAG vs. Agentic Loop

### 1. Traditional/Plain RAG

The application searches the FAQ dataset exactly once using the user's raw query, constructs a structured prompt template containing the retrieved context block, and forwards it to the LLM.

* **Limitation:** If a student types a keyword typo, the lexical index returns empty results, causing the pipeline to break or generate an unhelpful `"I don't know"` response.

### 2. The Agentic Loop

By registering the `search()` function as a valid JSON-schema tool within `toyaikit.tools`, the LLM is placed in the driver's seat:

* It autonomously determines whether it needs to use the search tool.
* It reformulates the raw input question into cleaner search tokens.
* It inspects search outputs and loops back to execute new searches with distinct keywords if initial results are insufficient.

```text
Student Question ──> LLM reasons ──> Calls Search Tool ──> Evaluates Results ──> Final Answer
```

---

## 📈 Notebook Walkthrough

The `sandbox.ipynb` notebook implements the entire pipeline step by step:

1. **Get Data:** Pulls the multi-course FAQ source files.
2. **Indexing:** Configures text fields (`question`, `section`, `answer`) and keyword filtering (`course`).
3. **Tool Schema Generation:** Uses Python type-hints and docstrings to let `toyaikit` automatically derive production-ready tool definitions.
4. **Agent Execution:** Instantiates an `OpenAIResponsesRunner` driven by standard developer instructions, guiding it to evaluate search trajectory before concluding the loop.
