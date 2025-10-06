# Supports ARM64 architecture required by AgentCore Runtime
FROM --platform=linux/arm64 python:3.11-slim

WORKDIR /app

# Install system dependencies needed for Python package compilation
RUN apt-get update && apt-get install -y \
    gcc \
    && rm -rf /var/lib/apt/lists/*

# Install Python dependencies first to leverage Docker layer caching
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy agent code
COPY agent.py .

# Expose the port AgentCore uses for invocations
EXPOSE 8080

# Run the agent
CMD ["python", "agent.py"]