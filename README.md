# Awesome MCP FastAPI

[![smithery badge](https://smithery.ai/badge/@MR-GREEN1337/awesome-mcp-fastapi)](https://smithery.ai/server/@MR-GREEN1337/awesome-mcp-fastapi)

A powerful FastAPI-based implementation of the Model Context Protocol (MCP) with enhanced tool registry capabilities, leveraging the mature FastAPI ecosystem.

## Overview

Awesome MCP FastAPI is a production-ready implementation of the Model Context Protocol that enhances and extends the standard MCP functionality by integrating it with FastAPI's robust ecosystem. This project provides an improved tool registry system that makes it easier to create, manage, and document AI tools for Large Language Models (LLMs).

## Why This Is Better Than Standard MCP

While the Model Context Protocol provides a solid foundation for connecting AI models with tools and data sources, our implementation offers several significant advantages:

### FastAPI's Mature Ecosystem

- **Production-Ready Web Framework**: Built on FastAPI, a high-performance, modern web framework with automatic OpenAPI documentation generation.
- **Dependency Injection**: Leverage FastAPI's powerful dependency injection system for more maintainable and testable code.
- **Middleware Support**: Easy integration with authentication, monitoring, and other middleware components.
- **Built-in Validation**: Pydantic integration for robust request/response validation and data modeling.
- **Async Support**: First-class support for async/await patterns for high-concurrency applications.

### Enhanced Tool Registry

Our implementation improves upon the standard MCP tool registry by:

- **Automatic Documentation Generation**: Tools are automatically documented in both MCP format and OpenAPI specification.
- **Improved Type Hints**: Enhanced type information extraction for better tooling and IDE support.
- **Richer Schema Definitions**: More expressive JSON Schema definitions for tool inputs and outputs.
- **Better Error Handling**: Structured error responses with detailed information.
- **Enhanced Docstring Support**: Better extraction of documentation from Python docstrings.

### Additional Features

- **CORS Support**: Ready for cross-origin requests, making it easy to integrate with web applications.
- **Lifespan Management**: Proper resource initialization and cleanup through FastAPI's lifespan API.

## Getting Started

### Prerequisites

- Python 3.10+

### Installation

#### Installing via Smithery

To install Awesome MCP FastAPI for Claude Desktop automatically via [Smithery](https://smithery.ai/server/@MR-GREEN1337/awesome-mcp-fastapi):

```bash
npx -y @smithery/cli install @MR-GREEN1337/awesome-mcp-fastapi --client claude
```

#### Manual Installation
```bash
# Clone the repository
git clone https://github.com/yourusername/awesome-mcp-fastapi.git
cd awesome-mcp-fastapi

# Create a virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install dependencies
pip install -e .
```

### Running the Server

```bash
uvicorn src.main:app --reload
```

Visit http://localhost:8000/docs to see the OpenAPI documentation.

## Usage

### Creating a Tool

```python
from fastapi import FastAPI
from src.utils.tools import auto_tool, bind_app_tools

app = FastAPI()
bind_app_tools(app)

@auto_tool(
    name="calculator",
    description="Perform basic arithmetic operations",
    tags=["math"]
)
@app.post("/api/calculator")
async def calculator(operation: str, a: float, b: float):
    """
    Perform basic arithmetic operations.
    
    Parameters:
    - operation: The operation to perform (add, subtract, multiply, divide)
    - a: First number
    - b: Second number
    
    Returns:
    The result of the operation
    """
    if operation == "add":
        return {"result": a + b}
    elif operation == "subtract":
        return {"result": a - b}
    elif operation == "multiply":
        return {"result": a * b}
    elif operation == "divide":
        if b == 0:
            return {"error": "Cannot divide by zero"}
        return {"result": a / b}
    else:
        return {"error": f"Unknown operation: {operation}"}
```

### Accessing Tools Through MCP

LLMs can discover and use your tools through the Model Context Protocol. Example using Claude:

```
You can perform calculations using the calculator tool. Try calculating 42 * 13.
```

Claude will automatically find and use your calculator tool to perform the calculation.

## Architecture

Our application follows a modular architecture:

```
src/
├── api/              # API endpoints
│   └── v1/           # API version 1
├── core/             # Core functionality
│   └── settings.py   # Application settings
├── db/               # Database connections
│   └── models/       # Database models
├── main.py           # Application entry point
└── utils/            # Utility functions
    └── tools.py      # Enhanced tool registry
```

## Docker Support

Build and run with Docker:

```bash
docker build -t awesome-mcp-fastapi .
docker run -p 8000:8000 --env-file .env awesome-mcp-fastapi
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
