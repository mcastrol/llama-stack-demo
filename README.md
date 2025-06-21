# Llama Stack Demo with Ollama Template

This project demonstrates how to use Llama Stack using the Ollama template for building AI agents with various capabilities including web search, RAG (Retrieval-Augmented Generation), and inference using open-source LLMs. The Ollama template provides a complete stack configuration that integrates local LLM inference with advanced agent capabilities.

The project is structured around three main components: an inference server (Ollama), a Llama Stack server with the Ollama template, and a client environment for running interactive notebooks and applications.

---

## 1. Start Inference Server

The inference server uses Ollama to serve the LLM model locally. This provides the foundation for all AI operations in the stack.

**Purpose**: Start Ollama with the Llama 3.2 model and keep it loaded for optimal performance.

**Command**:
```bash
ollama run llama3.2:3b-instruct-fp16 --keepalive 360m
```

**What this does**:
- **Downloads the model**: If `llama3.2:3b-instruct-fp16` is not present, Ollama will automatically download it (~2GB)
- **Starts the model**: Loads the model into memory and starts serving it on `http://localhost:11434`
- **Keeps model loaded**: The `--keepalive 360m` parameter keeps the model in memory for 6 hours, avoiding reload delays
- **Resource requirements**: Requires approximately 4GB of RAM for the 3B parameter model

**Verification**: 
- Check if Ollama is running: `ollama list`
- Test model availability: `curl http://localhost:11434/api/generate -d '{"model":"llama3.2:3b-instruct-fp16","prompt":"Hello"}'`

---

## 2. Server: Llama Stack with Ollama Template

The server component creates and runs the complete Llama Stack using the Ollama template, which includes agents, tools, vector storage, and safety features.

### 2.1 Create Conda Environment

**Purpose**: Create a dedicated conda environment for the Llama Stack server.

**Commands**:
```bash
conda create --name llama-stack-server-demo python=3.11.8 -y
conda activate llama-stack-server-demo
pip install llama-stack
```

**What this does**:
- **Creates environment**: Sets up `llama-stack-server-demo` with Python 3.11.8
- **Installs Llama Stack**: Installs the core `llama-stack` package with all dependencies
- **Prepares for template usage**: Provides the foundation for building and running Ollama templates

### 2.2 Build Ollama Template

**Purpose**: Build the Ollama template configuration for Llama Stack.

**Commands**:
```bash
conda activate llama-stack-server-demo
export INFERENCE_MODEL="llama3.2:3b-instruct-fp16"
llama stack build --template ollama --image-type conda
```

**What this does**:
- **Sets model variable**: Defines which model to use for inference
- **Builds template**: Creates the complete Ollama template with all providers and configurations
- **Generates configs**: Creates `build.yaml` and `run.yaml` files in the `ollama/` directory
- **Conda integration**: Uses conda environment for all dependencies

**Template includes**:
- **Inference**: Ollama provider for LLM inference
- **Vector IO**: FAISS for embeddings and vector search
- **Safety**: Llama Guard for content filtering
- **Agents**: Meta-reference implementation
- **Tools**: Web search (Tavily), RAG runtime, Wolfram Alpha
- **Telemetry**: SQLite-based logging and tracing

### 2.3 Run Llama Stack Server

**Purpose**: Run the complete Llama Stack server with the Ollama template.

**Commands**:
```bash
conda activate llama-stack-server-demo
export INFERENCE_MODEL="llama3.2:3b-instruct-fp16"
llama stack run ./ollama/run.yaml
```

**What this does**:
- **Sets model**: Defines the inference model to use
- **Starts server**: Launches Llama Stack server on `http://localhost:8321`
- **Loads configuration**: Uses the Ollama template configuration
- **Enables all APIs**: Provides endpoints for agents, inference, tools, vector operations

**Server capabilities when running**:
- **Inference API**: `/v1/inference/chat_completion`
- **Agents API**: `/v1/agents` for creating and managing agents
- **Tools API**: `/v1/tools` for web search, RAG, etc.
- **Vector API**: `/v1/vector_io` for embeddings and search
- **Safety API**: `/v1/safety` for content filtering

---

## 3. Client: Environment and Notebooks

The client component provides the environment for running interactive notebooks and testing the Llama Stack capabilities.

### 3.1 Create Client Conda Environment

**Purpose**: Set up a separate environment for client applications and notebooks.

**Commands**:
```bash
# Create environment with Python 3.11.8
conda create --name llama-stack-client-demo python=3.11.8 -y

# Activate the environment
conda activate llama-stack-client-demo
```

**Why separate environment**:
- **Isolation**: Keeps client dependencies separate from server
- **Flexibility**: Allows different Python packages for development
- **Clean testing**: Ensures client works independently

### 3.2 Install Requirements

**Purpose**: Install all necessary packages for running notebooks and client applications.

**Requirements file content** (`requirements.txt`):
```
llama-stack-client
geocoder
python-dotenv
fire
```

**Commands**:
```bash
# Ensure you're in the client environment
conda activate llama-stack-client-demo

# Install from requirements file
pip install -r requirements.txt

# Additional notebook dependencies
pip install jupyter ipykernel termcolor
```

**Package purposes**:
- **llama-stack-client**: Client library for communicating with Llama Stack server
- **geocoder**: For location-based features in demos
- **python-dotenv**: For loading environment variables from `.env` files
- **fire**: For creating command-line interfaces
- **jupyter**: For running interactive notebooks
- **termcolor**: For colored output in notebooks

### 3.3 Test Models Connection

**Purpose**: Verify client can connect to the server and list available models.

**Command**:
```bash
conda activate llama-stack-client-demo
llama-stack-client models list
```

**What this does**:
- **Activates environment**: Ensures correct Python environment
- **Tests connection**: Connects to Llama Stack server (default: localhost:8321)
- **Lists models**: Shows all available models configured in the server
- **Verifies setup**: Confirms client-server communication is working

**Expected output**:
```
Model ID: llama3.2:3b-instruct-fp16
Model Type: llm
Provider: ollama
```

### 3.4 Environment Configuration

**Purpose**: Configure environment variables for notebooks and applications.

**Create `.env` file in project root**:
```bash
# Server Configuration
REMOTE_BASE_URL=http://localhost:8321
MODEL_NAME=llama3.2:3b-instruct-fp16

# Inference Parameters
TEMPERATURE=0.0
TOP_P=0.95
MAX_TOKENS=4096
STREAM=True

# API Keys for Tools (obtain from respective services)
TAVILY_SEARCH_API_KEY=your_tavily_api_key_here
BRAVE_SEARCH_API_KEY=your_brave_api_key_here
WOLFRAM_ALPHA_API_KEY=your_wolfram_alpha_key_here

# Ollama Configuration
OLLAMA_URL=http://localhost:11434
INFERENCE_MODEL=llama3.2:3b-instruct-fp16
```

### 3.5 Run Notebooks

**Purpose**: Execute interactive demonstrations of Llama Stack capabilities.

**Commands**:
```bash
# Ensure client environment is active
conda activate llama-stack-client-demo

# Navigate to notebooks directory
cd notebooks-demo

# Start Jupyter server
jupyter notebook
```

**Available notebooks**:

1. **`1.- Getting_started_with_Llama_Stack.ipynb`**
   - Basic Llama Stack usage
   - Simple inference examples
   - Client-server communication

2. **`2.- Simple_RAG.ipynb`** 
   - Retrieval-Augmented Generation
   - Vector database usage
   - Document embedding and search

3. **`3.- Simple_agent_with_websearch.ipynb`**
   - Agent with web search tool
   - Tavily API integration
   - Real-time information retrieval

4. **`4.- RAG_agent.ipynb`**
   - Advanced RAG with agents
   - Combined retrieval and reasoning
   - Complex multi-step workflows

**Notebook execution tips**:
- **Run in order**: Execute notebooks sequentially for learning progression
- **Check environment**: Ensure `.env` file is configured before running
- **API keys**: Web search notebooks require Tavily API key
- **Restart kernel**: If issues occur, restart Jupyter kernel and re-run

---

## Complete Setup Sequence

**Quick start (execute in order)**:

```bash
# 1. Start Ollama inference
ollama run llama3.2:3b-instruct-fp16 --keepalive 360m

# 2. Create and setup server (new terminal)
conda create --name llama-stack-server-demo python=3.11.8 -y
conda activate llama-stack-server-demo
pip install llama-stack
export INFERENCE_MODEL="llama3.2:3b-instruct-fp16"
llama stack build --template ollama --image-type conda
llama stack run ./ollama/run.yaml

# 3. Setup client (new terminal)
conda create --name llama-stack-client-demo python=3.11.8 -y
conda activate llama-stack-client-demo
pip install -r requirements.txt
llama-stack-client models list

# 4. Run notebooks
cd notebooks-demo
jupyter notebook
```

## System Requirements

- **RAM**: 8GB minimum (4GB for model + 4GB for system)
- **Storage**: 10GB free space (for models and environments)
- **CPU**: Multi-core recommended for better performance
- **OS**: Linux, macOS, or Windows with WSL2