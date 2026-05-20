# Semantic Indexer

This app will enable you to pull in a set of documents (in .pdf, .odt, .docx) and create a vector space for RAG search.

The application features a web-based user interface accessible via any browser at http://localhost:<port_number>.

Ollama is used as the LLM to format and summarize your search results into a Markdown (.md) like display, highlighting the most important text.

The LLM does not add any external information beyond what is found in the retrieved documents.

In addition to the summarized chat results, a list of the documents and page numbers from where the information was retreived will be provided in a drop down.

## 🚀 Instructions

After cloning this app from GitHub onto your machine . . .

Install the Docker Desktop Application on your machine and start it

Create a /docs folder in the root of your app folder

Make your .env file with keys as in the following EXAMPLE .env file

NOTE: the FRONTEND_PORT key is limited to 3000 through 3009

    ```
    HF_TOKEN=your_huggingface_token_here
    DB_SUFFIX=one
    DB_USER=user
    DB_PASSWORD=password
    DB_NAME=rag_db
    FRONTEND_PORT=3000
    BACKEND_PORT=8080
    COMPOSE_PROJECT_NAME=project_one

Put your .env file in the root of your app folder

Run the following terminal command in the root of your app folder . . .

    ```bash
    docker compose up -d

Finally, run this command in the terminal

    ```bash
    docker compose exec ollama ollama run llama3.2:latest

Now, you can open up a browser and go to http://localhost:<FRONTEND_PORT as in your .env file>

## 🚀 Hardware Requirements
    Recommended Requirements (For a smooth experience)
    This ensures fast response times (tokens per second) and snappy vector search performance.

    RAM: 32 GB

    Why: Gives the backend plenty of breathing room for chunking documents, 
    embedding generation, and handling concurrent requests without bottlenecking the system.

    GPU (Highly Recommended for Ollama):

    NVIDIA: GTX 1660 / RTX 3060 or higher with at least 6 GB of VRAM.

    Apple Silicon: Mac with 16 GB or 32 GB of Unified Memory (M1/M2/M3 Pro/Max).

    Why: Running LLMs on CPU is notoriously slow. A GPU speeds up token generation by 5x to 10x.

    Storage: 25 GB+ of free NVMe SSD space.

## 🚀 Running Multiple Instances (Avoiding Name Clashes)

This application is designed to be fully isolated so you can run multiple instances of it side-by-side on the same machine. However, due to limitations in Docker Compose's configuration parsing, you must manually align your service naming if you change the database suffix.

### $\quad$ Steps to Deploy an Additional Instance:

1. **Update your `.env` file:**

   Change the `DB_SUFFIX` to uniquely identify this instance (e.g., `analytics`, `prod`, `testing`):

   DB_SUFFIX=analytics

2. **Update your `docker-compose.yaml` file:**

   Change the service name of the database service
   
   FROM db-home TO db-< whatever >

   EXAMPLE:

   db-analytics: # NOTE: This MUST match this pattern: db-DB_SUFFIX, where DB_SUFFIX is defined in .env file

   $\quad$ image: pgvector/pgvector:pg17

   $\quad$ . . .


> 🚀 [!TIP]
> If you are a MAC user with GPUs, and you want to utilize the full computing power of your machine,
> 1. Install Ollama
> 2. Modify the provided docker-compose.yaml file as in the following snippet . . .

```yaml
services:
  # ollama: # COMMENT THESE LINES OUT WHEN USING NATIVE HOST MAC-MINI GPUs
  #   image: ollama/ollama:latest
  #   volumes:
  #     - ollama_models:/root/.ollama
    ...

  backend:
    image: d0kkm96s7stm/rag-backend:latest 
    environment:
      - DATABASE_URL=postgresql://${DB_USER}:${DB_PASSWORD}@db-${DB_SUFFIX}:5432/${DB_NAME}
      - OLLAMA_HOST=http://host.docker.internal:11434 # CHANGE THIS LINE WHEN USING NATIVE HOST MAC-MINI GPUs
      - PYTHONUNBUFFERED=1
      - HF_TOKEN=${HF_TOKEN}
    ...

    depends_on:
      # - ollama # COMMENT THIS OUT WHEN USING NATIVE HOST MAC-MINI GPUs
      - db-${DB_SUFFIX}
```


