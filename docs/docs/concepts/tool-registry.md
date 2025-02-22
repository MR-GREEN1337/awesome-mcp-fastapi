# Enhanced Tool Registry

One of the core advantages of Awesome MCP FastAPI is its enhanced tool registry system, which automatically discovers, documents, and exposes your FastAPI endpoints as MCP tools.

## How It Works

<div class="feature-diagram">
  <img src="/assets/tool-registry-flow.svg" alt="Tool Registry Flow Diagram" />
</div>

The tool registry works through several key components:

1. **Decorator System**: The `@auto_tool` decorator marks FastAPI endpoints as MCP tools
2. **Route Scanner**: Automatically discovers all decorated endpoints in your application
3. **Schema Generator**: Creates rich JSON schemas from Python type annotations and docstrings
4. **Registry API**: Exposes tools through both FastAPI endpoints and MCP protocol interfaces

## Key Benefits

### 1. Automatic Documentation Generation

The tool registry automatically generates documentation in two formats:

- **MCP Tool Schemas**: Compatible with all MCP clients (like Claude)
- **OpenAPI/Swagger**: For human developers to explore and test

This dual documentation approach ensures both AI models and humans can discover and understand your tools.

### 2. Enhanced Type Information

Unlike standard MCP, our tool registry extracts rich type information from:

- **Function signatures**: Parameter types and return types
- **Pydantic models**: All field types, constraints, and validations
- **Field extra information**: Examples, descriptions, and constraints

This results in more accurate schema generation and better client experiences.

### 3. Docstring Processing

The registry intelligently processes Python docstrings to:

- Extract parameter descriptions
- Generate example values
- Document return values
- Provide usage notes

### 4. Production-Ready Features

- **Caching**: Tool schemas are cached for performance
- **Hot Reloading**: New tools are discovered when added
- **Error Handling**: Robust error handling for schema generation
- **Logging**: Detailed logging for debugging

## Using the Tool Registry

### Binding to Your App

First, bind the tool registry to your FastAPI app:

```python
from fastapi import FastAPI
from awesome_mcp_fastapi import bind_app_tools

app = FastAPI()
bind_app_tools(app)  # This registers the tool registry
```

This creates the following endpoints:

- `/tools/all`: Lists all registered tools with their schemas
- `/tools/scan`: Manually triggers a scan for new tools

### Marking Endpoints as Tools

Use the `@auto_tool` decorator to mark FastAPI endpoints as MCP tools:

```python
from awesome_mcp_fastapi import auto_tool

@auto_tool(
    name="tool-name",             # Unique tool identifier
    description="Tool description", # Optional, falls back to docstring
    tags=["category1", "category2"], # Optional, for organization
    example_input={"param": "value"}, # Optional example input
    example_output={"result": "value"} # Optional example output
)
@app.post("/api/endpoint")        # Regular FastAPI route decorator
async def my_endpoint(param1: str, param2: int):
    """
    Endpoint docstring - will be used for documentation.
    
    Will be parsed to extract parameter descriptions and examples.
    """
    # Implementation
    return {"result": "value"}
```

### Tool Discovery Process

<div class="sequence-diagram">
  <img src="/assets/tool-discovery-sequence.svg" alt="Tool Discovery Sequence Diagram" />
</div>

The tool registry:

1. Scans all routes in your FastAPI application
2. Identifies routes decorated with `@auto_tool`
3. Analyzes function signatures and docstrings
4. Generates input and output schemas
5. Registers tools in the MCP protocol
6. Exposes tool listing through the API

### Advanced Schema Generation

The schema generator has several advanced capabilities:

```python
from pydantic import BaseModel, Field
from enum import Enum
from typing import List, Optional

class Status(str, Enum):
    """Processing status enum"""
    PENDING = "pending"
    PROCESSING = "processing"
    COMPLETED = "completed"
    FAILED = "failed"

class JobResult(BaseModel):
    """Result of a background job"""
    id: str = Field(..., description="Unique job identifier")
    status: Status = Field(..., description="Current job status")
    progress: float = Field(0.0, description="Progress percentage", ge=0.0, le=100.0)
    results: Optional[List[str]] = Field(None, description="Job results if completed")
    
    class Config:
        schema_extra = {
            "example": {
                "id": "job_12345",
                "status": "completed",
                "progress": 100.0,
                "results": ["result1", "result2"]
            }
        }

@auto_tool(
    name="get-job-status",
    description="Check the status of a background job",
    tags=["jobs"]
)
@app.get("/api/jobs/{job_id}", response_model=JobResult)
async def get_job_status(job_id: str):
    """
    Get the current status of a background job.
    
    Parameters:
    - job_id: The unique identifier of the job
    
    Returns:
    The current job status and results if completed.
    """
    # Implementation
    return JobResult(
        id=job_id,
        status=Status.COMPLETED,
        progress=100.0,
        results=["example result"]
    )
```

From this code, the registry will automatically generate:

- A rich input schema with the job_id parameter
- A complete output schema with all JobResult fields
- Proper Enum value handling
- Examples from the Pydantic Config
- Descriptions from Field definitions and docstrings

## Customizing the Registry

You can customize the tool registry behavior with options:

```python
bind_app_tools(
    app,
    prefix="/custom-path",   # Custom endpoint prefix (default: /tools)
    scan_on_startup=True,    # Auto-scan on startup
    enable_api=True,         # Enable API endpoints
    cache_ttl=60             # Schema cache TTL in seconds
)
```

## Internals: Schema Processing

The tool registry employs sophisticated techniques to generate schemas:

### Input Schema Generation

1. Analyzes function parameters
2. Identifies query, path, and body parameters
3. Extracts types, defaults, and constraints
4. Processes Pydantic models recursively
5. Adds examples and descriptions

### Output Schema Generation

1. Identifies response_model from route decorators
2. Falls back to return type annotations
3. Processes Pydantic response models
4. Extracts example responses
5. Adds rich field descriptions

## Performance Considerations

The tool registry is designed for production performance:

- **Lazy Loading**: Tools are scanned only when needed
- **Caching**: Schema generation results are cached
- **Minimal Overhead**: Negligible impact on request handling
- **Optimized Processing**: Efficient schema generation

## Next Steps

- Learn about [advanced tool patterns](/guides/advanced-tools)
- Understand [schema generation details](/api-reference/schema-generation)
- Explore [integration with MCP clients](/guides/client-integration)