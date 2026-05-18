# Semantic Indexer

This app will enable you to pull in a set of documents (in .pdf, .odt, .docx) and create a vector space for RAG search.

The application features a web-based user interface accessible via any browser at http://localhost:<port_number>.

Ollama is used as the LLM to format and summarize your search results into a Markdown (.md) like display, highlighting the most important text.

The LLM does not add any external information beyond what is found in the retrieved documents.

In addition to the summarized chat results, a list of the documents and page numbers from where the information was retreived will be provided in a drop down.

## Instructions

After cloning this app from GitHub onto your machine . . .

Install the Docker Desktop Application on your machine and start it

Create a /docs folder in the root of your app folder

Make your .env file with the following keys

    HF_TOKEN=your_huggingface_token_here
    DB_USER=user
    DB_PASSWORD=password
    DB_NAME=rag_db
    FRONTEND_PORT=3003
    BACKEND_PORT=8080

Put your .env file in the root of your app folder

Run the following terminal command in the root of your app folder . . .

    ```bash
    docker compose up -d

> [!TIP]
> If you are a MAC user with GPUs, and you want to utilize the full computing power of your machine,
> 1. Install Ollama
> 2. Modify the provided docker-compose.yaml file as in the following snippet . . .

```yaml
services:
  # ollama: # COMMENT THESE LINES OUT WHEN USING NATIVE HOST MAC-MINI GPUs
  #   image: ollama/ollama:latest
  #   ports:
  #     - "11434:11434"
  #   volumes:
  #     - ollama_models:/root/.ollama
    ...

  backend:
    image: d0kkm96s7stm/rag-backend:latest 
    environment:
      - DATABASE_URL=postgresql://${DB_USER:-user}:${DB_PASSWORD:-password}@db:5432/${DB_NAME:-rag_db}
      - OLLAMA_HOST=http://host.docker.internal:11434 # CHANGE THIS LINE WHEN USING NATIVE HOST MAC-MINI GPUs
      - PYTHONUNBUFFERED=1
      - HF_TOKEN=${HF_TOKEN}
    ...

    depends_on:
      # - ollama # COMMENT THIS OUT WHEN USING NATIVE HOST MAC-MINI GPUs
      - db
```


