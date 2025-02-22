# Quickstart Guide

Get up and running with Awesome MCP FastAPI in minutes.

## Installation

First, install the package using your preferred method:

```bash
# Using uv (recommended)
git clone https://github.com/yourusername/awesome-mcp-fastapi.git
cd awesome-mcp-fastapi
uv venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
uv pip install -e .

# Using pip with requirements.txt
git clone https://github.com/yourusername/awesome-mcp-fastapi.git
cd awesome-mcp-fastapi
pip install -r requirements.txt
```

## Basic Setup

Create a new FastAPI application with Awesome MCP FastAPI:

```python
from fastapi import FastAPI
from src.utils.tools import auto_tool, bind_app_tools

# Create FastAPI app
app = FastAPI(
    title="My AI Tools API",
    description="API with tools for AI models",
    version="0.1.0"
)

# Connect the tool registry to the app
bind_app_tools(app)

# Now you can create tools!
```

## Creating Your First Tool

Let's create a simple calculator tool:

```python
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

## Using Pydantic Models

For more complex tools, use Pydantic models for better validation and documentation:

```python
from pydantic import BaseModel, Field

class WeatherRequest(BaseModel):
    """Parameters for weather forecast request"""
    location: str = Field(..., description="City name or coordinates", example="New York")
    days: int = Field(5, description="Number of days to forecast", ge=1, le=10)
    include_hourly: bool = Field(False, description="Include hour-by-hour forecast")

class WeatherForecast(BaseModel):
    """Weather forecast response"""
    location: str = Field(..., description="Location of the forecast")
    current_temp: float = Field(..., description="Current temperature in Celsius")
    forecasts: list = Field(..., description="Daily forecasts")

@auto_tool(
    name="weather-forecast",
    description="Get weather forecast for a location",
    tags=["weather", "forecast"]
)
@app.post("/api/weather/forecast", response_model=WeatherForecast)
async def weather_forecast(request: WeatherRequest):
    """
    Get a weather forecast for the specified location.
    
    Returns current conditions and a daily forecast.
    """
    # In a real implementation, this would call a weather API
    return WeatherForecast(
        location=request.location,
        current_temp=22.5,
        forecasts=[
            {"day": "Monday", "temp": 23.0, "condition": "Sunny"},
            {"day": "Tuesday", "temp": 21.5, "condition": "Partly Cloudy"},
            # More forecasts...
        ]
    )
```

## Running Your Server

Run your server using Uvicorn:

```bash
uvicorn src.main:app --reload
```

Your FastAPI application will be available at http://localhost:8000, with the following endpoints:

- **OpenAPI Documentation**: http://localhost:8000/docs
- **ReDoc Documentation**: http://localhost:8000/redoc
- **MCP Tool List**: http://localhost:8000/tools/all

## Testing Your Tools

You can test your tools directly using the FastAPI Swagger UI at `/docs`, or using an MCP client like Claude.

### Testing with curl

```bash
curl -X POST "http://localhost:8000/api/calculator" \
     -H "Content-Type: application/json" \
     -d '{"operation": "add", "a": 5, "b": 3}'
```

### Testing with Python

```python
import requests

response = requests.post(
    "http://localhost:8000/api/calculator",
    json={"operation": "add", "a": 5, "b": 3}
)

print(response.json())  # {"result": 8}
```

## Adding Authentication

Secure your tools with FastAPI's authentication:

```python
from fastapi import Depends, HTTPException, status
from fastapi.security import APIKeyHeader

# Define API key scheme
API_KEY = "your-secret-key"
api_key_header = APIKeyHeader(name="X-API-Key")

def verify_api_key(api_key: str = Depends(api_key_header)):
    if api_key != API_KEY:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid API Key"
        )
    return api_key

@auto_tool(
    name="secured-tool",
    description="This tool requires authentication",
    tags=["secured"]
)
@app.post("/api/secured")
async def secured_endpoint(data: str, api_key: str = Depends(verify_api_key)):
    """A secured endpoint that requires authentication."""
    return {"message": f"Secured operation completed on: {data}"}
```