# Installation Guide

This guide will help you install and set up Awesome MCP FastAPI for your project.

## System Requirements

- Python 3.10 or higher
- FastAPI 0.95.0 or higher
- Pydantic v2.0 or higher

## Installation Options

### Option 1: Using uv (Recommended)

[uv](https://github.com/astral-sh/uv) is a fast Python package installer and resolver. If you don't have it installed, visit the [uv installation page](https://github.com/astral-sh/uv#installation).

```bash
# Clone the repository
git clone https://github.com/yourusername/awesome-mcp-fastapi.git
cd awesome-mcp-fastapi

# Install with uv
uv venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
uv pip install -e .
```

### Option 2: Using pip with requirements.txt

The project includes a requirements.txt file for easy installation:

```bash
# Clone the repository
git clone https://github.com/yourusername/awesome-mcp-fastapi.git
cd awesome-mcp-fastapi

# Install dependencies from requirements.txt
pip install -r requirements.txt
```

### Option 3: Direct Installation

Install directly from the cloned repository:

```bash
git clone https://github.com/yourusername/awesome-mcp-fastapi.git
cd awesome-mcp-fastapi
pip install -e .
```

## Development Installation

For development work, install with development dependencies:

```bash
# Using uv
uv pip install -e ".[dev,test,doc]"

# Using pip
pip install -e ".[dev,test,doc]"
```

## Docker Installation

To use Awesome MCP FastAPI in a Docker container:

```bash
git clone https://github.com/yourusername/awesome-mcp-fastapi.git
cd awesome-mcp-fastapi
docker build -t awesome-mcp-fastapi .
docker run -p 8000:8000 awesome-mcp-fastapi
```

## Quick Verification

After installation, verify that everything is working correctly:

```python
from fastapi import FastAPI
from awesome_mcp_fastapi import auto_tool, bind_app_tools

# This should run without errors
app = FastAPI()
bind_app_tools(app)

@auto_tool(name="hello", description="Say hello")
@app.get("/hello")
def hello(name: str = "World"):
    return {"message": f"Hello, {name}!"}
```

## Configuration

### Environment Variables

Awesome MCP FastAPI supports the following environment variables:

| Variable | Description | Default |
|----------|-------------|---------|
| `AWESOME_MCP_LOG_LEVEL` | Logging level | `INFO` |
| `AWESOME_MCP_SCAN_ON_STARTUP` | Scan for tools on startup | `TRUE` |
| `AWESOME_MCP_CACHE_TTL` | Schema cache TTL in seconds | `60` |
| `AWESOME_MCP_API_PREFIX` | Custom API prefix | `/tools` |

You can set these in your environment or `.env` file.