# Local MCP Client with LlamaIndex and Ollama

This project demonstrates how to build a local Model Context Protocol (MCP) client using LlamaIndex, Ollama, and a SQLite database. It shows how to create an AI agent that can interact with tools to perform database operations.

## 🎯 Learning Objectives

- Understanding Model Context Protocol (MCP) architecture
- Setting up local LLM inference with Ollama
- Integrating LlamaIndex with MCP tools
- Building database interaction capabilities
- Creating a conversational AI agent with tool calling

## 📋 Prerequisites

- Python 3.8+
- Homebrew (for Ollama installation)

## 🚀 Step-by-Step Setup

### 1. Install Ollama

First, install Ollama using Homebrew:

```bash
brew install ollama
```

### 2. Start Ollama Service

Start the Ollama service in the background:

```bash
ollama serve
```

### 3. Download Required Model

Download the Llama 3.2 model that will be used by the client:

```bash
ollama pull llama3.2
```

### 4. Verify Ollama Installation

Check that Ollama is working correctly:

```bash
ollama list
ollama run llama3.2 "Hello, are you working?"
```

### 5. Set Up Python Environment

Create and activate a virtual environment:

```bash
python3 -m venv venv
source venv/bin/activate
```

### 6. Install Dependencies

Install the required Python packages:

```bash
pip install -r requirements.txt
```

## 🏗️ Project Architecture

### Components

1. **Server (`server.py`)**: MCP server that provides database tools
2. **Client (`client.py`)**: LlamaIndex-based client that uses the MCP tools
3. **Database (`demo.db`)**: SQLite database for storing people data

### How It Works: The Complete Flow

The system follows this orchestration pattern:

```
User Input → Ollama LLM → MCP Client → MCP Server → Database
```

**Step-by-step breakdown:**

1. **User Input**: Natural language query (e.g., "delete all data")

2. **Ollama LLM** (in LlamaIndex Agent):
   - Interprets the natural language query
   - Understands user intent and context
   - Decides which tool to call based on available tools
   - Generates appropriate SQL query (e.g., `DELETE FROM people`)

3. **MCP Client**:
   - Receives tool call request from the LLM
   - Sends HTTP/SSE request to MCP Server
   - Example: `add_data(query="DELETE FROM people")`

4. **MCP Server**:
   - Receives the tool call
   - Executes the SQL directly (no LLM on server side)
   - Returns result to client

5. **Response flows back**: Server → Client → LLM → User

**Key Insight**: The LLM (Ollama) is only on the client side and acts as the "brain" that interprets natural language and decides what tools to call. The MCP server is just a tool executor - it doesn't have its own LLM.

### Database Schema

```sql
CREATE TABLE people (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    age INTEGER NOT NULL,
    profession TEXT NOT NULL
);
```

### Available Tools

1. **add_data**: Adds new records to the people table
2. **read_data**: Reads data from the people table

## 🔧 Configuration

### System Prompt

The client uses a carefully crafted system prompt to ensure proper SQL query construction:

```python
SYSTEM_PROMPT = """\
You are an AI assistant for Tool Calling that works with a SQLite database.

When users ask to add data to the database, you must:
1. Parse the user's input to extract name, age, and profession
2. Construct a COMPLETE SQL INSERT query in this exact format:
   INSERT INTO people (name, age, profession) VALUES ('Name', age, 'Profession')
3. Use the add_data tool with the complete query

When users ask to read data, use the read_data tool with appropriate SELECT queries.

Always ensure SQL queries are complete and properly formatted with correct syntax.
"""
```

## 🚀 Running the Application

### 1. Start the MCP Server

In one terminal, start the MCP server:

```bash
python server.py --server-type sse
```

### 2. Start the Client

In another terminal, start the client:

```bash
python client.py
```

### 3. Interact with the Agent

Once both are running, you can interact with the agent using natural language:

**Adding data:**
```
add data to db: Michael 23 tennis player
```

**Reading data:**
```
show all people in the database
```

## 🔍 Troubleshooting

### Common Issues

1. **Ollama Connection Error**
   - Ensure Ollama is running: `ollama serve`
   - Verify model is downloaded: `ollama list`
   - Test model: `ollama run llama3.2 "test"`

2. **Import Errors**
   - Activate virtual environment: `source venv/bin/activate`
   - Install dependencies: `pip install -r requirements.txt`

3. **Incomplete SQL Queries**
   - The system prompt was improved to ensure complete query construction
   - Ensure the client is using the updated system prompt

4. **Database Connection Issues**
   - Verify the server is running on the correct port
   - Check that `demo.db` file exists and is writable

### Debug Mode

Run the client with verbose output to see tool calls:

```python
response = await handle_user_message(user_input, agent, agent_context, verbose=True)
```

## 📚 Key Learnings

### 1. MCP Architecture
- MCP provides a standardized way for AI models to interact with tools
- Tools are exposed as functions that can be called by the AI agent
- The protocol handles serialization and communication between client and server

### 2. LlamaIndex Integration
- LlamaIndex provides a high-level interface for building AI applications
- FunctionAgent enables tool calling capabilities
- Context management helps maintain conversation state

### 3. Local LLM Setup
- Ollama provides easy local LLM inference
- Model downloading and management is straightforward
- GPU acceleration works out of the box on Apple Silicon

### 4. Database Operations
- SQL queries must be complete and properly formatted
- Error handling is crucial for robust database operations
- Parameter extraction from natural language requires careful prompting

### 5. System Prompt Engineering
- Clear, specific instructions are essential for reliable tool usage
- Providing examples helps the model understand expected behavior
- Structured prompts improve consistency and accuracy

## 🔄 Development Workflow

1. **Design the tools** in `server.py`
2. **Update the system prompt** in `client.py` for better tool usage
3. **Test with various inputs** to ensure robustness
4. **Iterate on prompts** based on observed behavior
5. **Add error handling** for edge cases

## 🎉 Success Criteria

The project is successful when:
- ✅ Ollama runs locally with Llama 3.2 model
- ✅ MCP server provides database tools
- ✅ Client can add data using natural language
- ✅ Client can read data from the database
- ✅ SQL queries are constructed correctly
- ✅ Error handling works for edge cases

## 🔮 Future Enhancements

- Add more database operations (update, delete)
- Implement user authentication
- Add data validation and sanitization
- Create a web interface
- Support for multiple database types
- Add conversation history persistence

## 📖 Resources

- [Model Context Protocol Documentation](https://modelcontextprotocol.io/)
- [LlamaIndex Documentation](https://docs.llamaindex.ai/)
- [Ollama Documentation](https://ollama.ai/docs)
- [SQLite Documentation](https://www.sqlite.org/docs.html)
