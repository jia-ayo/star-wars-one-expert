# Star Wars Movie Script Expert 🌟

An intelligent chatbot that answers questions about the original Star Wars trilogy using Retrieval-Augmented Generation (RAG) and vector search.

## Overview

This project creates an AI-powered expert that can answer questions about the original Star Wars movies (A New Hope, The Empire Strikes Back, and Return of the Jedi) by searching through the actual movie scripts. It uses modern LangChain technology combined with OpenAI's language models and Qdrant vector database for accurate, context-aware responses.

## Features

- **Script Retrieval**: Automatically downloads and processes the original Star Wars trilogy scripts from IMSDB
- **Intelligent Chunking**: Splits scripts into semantically meaningful chunks based on scene boundaries (INT./EXT. markers)
- **Vector Search**: Uses embeddings and Qdrant for fast, semantic search across all scripts
- **RAG Pipeline**: Combines retrieval with GPT-3.5-turbo to provide accurate, grounded answers
- **Interactive Chat**: Command-line interface for asking questions about Star Wars movies
- **Persistent Storage**: Stores processed scripts locally to avoid re-downloading on subsequent runs

## How It Works

### Architecture

```
User Question → Retriever (Qdrant) → Context Retrieval → LLM (GPT-3.5) → Answer
                     ↑
                Embeddings DB
                (Star Wars Scripts)
```

1. **Data Loading**: Fetches Star Wars scripts from IMSDB using web scraping (BeautifulSoup)
2. **Text Splitting**: Divides scripts into chunks (2500 chars with 250 char overlap) using scene markers
3. **Embedding**: Converts text chunks into vector embeddings using OpenAI's `text-embedding-3-small`
4. **Vector Storage**: Stores embeddings in Qdrant vector database for efficient retrieval
5. **Question Answering**: When you ask a question:
   - Converts your question into an embedding
   - Retrieves the 15 most relevant script chunks
   - Sends context + question to GPT-3.5-turbo
   - Returns an answer based only on the script content

## Prerequisites

- Python 3.13 or higher
- OpenAI API key
- Internet connection (for initial script download)

## Setup

### 1. Clone the Repository

```bash
cd star-wars-one-expert
```

### 2. Install Dependencies

This project uses `uv` for dependency management. If you don't have it installed:

```bash
# Install uv
pip install uv
```

Then install project dependencies:

```bash
uv sync
```

Alternatively, if you prefer using pip:

```bash
pip install beautifulsoup4 langchain langchain-openai langchain-qdrant qdrant-client langchain-text-splitters
```

### 3. Set Up OpenAI API Key

You need an OpenAI API key to use the embeddings and LLM. Set it as an environment variable:

**On Windows (PowerShell):**
```powershell
$env:OPENAI_API_KEY="your-api-key-here"
```

**On Windows (Command Prompt):**
```cmd
set OPENAI_API_KEY=your-api-key-here
```

**On Linux/Mac:**
```bash
export OPENAI_API_KEY="your-api-key-here"
```

Or create a `.env` file in the project root:
```
OPENAI_API_KEY=your-api-key-here
```

## Usage

### Running the Application

```bash
python main.py
```

### First Run

The first time you run the application, it will:
1. Download all three Star Wars movie scripts
2. Split them into chunks
3. Generate embeddings
4. Store everything in the `./qdrant` directory

This process takes a few minutes but only happens once.

### Subsequent Runs

On subsequent runs, the app will load the existing vector database and start immediately.

### Asking Questions

Once started, you'll see:
```
--- The Star Wars Movie expert is ready to answer your questions: ----
```

You can now ask questions about the movies:

**Example Questions:**
```
You: What does Yoda say about fear?

You: How does Luke destroy the Death Star?

You: What is Princess Leia's message to Obi-Wan?

You: Who is Darth Vader's son?
```

The bot will search the scripts and provide answers based on actual dialogue and scenes.

### Exiting

Type `exit` or `quit` to stop the program.

## Project Structure

```
star-wars-one-expert/
├── main.py              # Main application code
├── pyproject.toml       # Project dependencies and metadata
├── uv.lock             # Locked dependency versions
├── README.md           # This file
├── .python-version     # Python version specification
├── .gitignore          # Git ignore rules
├── .venv/              # Virtual environment (created by uv)
└── qdrant/             # Vector database storage (created on first run)
```

## Code Explanation

### Key Components

**`load_star_wars_script(url, movie_title)`**
- Downloads and parses movie scripts from IMSDB
- Uses BeautifulSoup to extract script text
- Returns a LangChain Document with metadata

**`main()`**
- Sets up Qdrant client and checks for existing collection
- If no collection exists:
  - Downloads and chunks all three movie scripts
  - Creates embeddings and stores in Qdrant
- Sets up RAG chain with retriever, prompt template, and LLM
- Runs interactive chat loop

**RAG Chain**
```python
rag_chain = (
    {"context": retriever, "question": RunnablePassthrough()}
    | prompt
    | llm
    | StrOutputParser()
)
```
This chain:
1. Retrieves relevant context from vector store
2. Formats context + question using prompt template
3. Sends to LLM for answer generation
4. Parses and returns the string response

## Known Issues

> [!NOTE]
> There are two typos in the code that need to be fixed:
> - Line 31: `emdeddings` should be `embeddings`
> - Line 62: `chuncks` should be `chunks`
> - Line 75: `search_kwags` should be `search_kwargs`

## Technologies Used

- **LangChain**: Framework for building LLM applications
- **OpenAI**: GPT-3.5-turbo for chat and text-embedding-3-small for embeddings
- **Qdrant**: Vector database for similarity search
- **BeautifulSoup**: Web scraping for script retrieval
- **Python 3.13**: Programming language

## Future Enhancements

- Add support for the prequel trilogy
- Implement a web interface (Streamlit or Gradio)
- Add citation/source tracking to show which movie/scene answers come from
- Support follow-up questions with conversation memory
- Add filtering by specific movies

## License

This is a learning project. Movie scripts are sourced from IMSDB for educational purposes.

## Acknowledgments

- Movie scripts from [The Internet Movie Script Database (IMSDB)](https://www.imsdb.com/)
- Built with [LangChain](https://python.langchain.com/)
- Powered by [OpenAI](https://openai.com/)
