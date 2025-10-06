# Hello World Agent

The simplest possible AgentCore agent. Perfect for learning the basics.

## What It Does

This agent accepts text prompts and returns AI-generated responses using Claude Sonnet 4.0. No tools, no memory, just basic conversation.

## Use Case

Learning AgentCore fundamentals:
- How to structure an agent
- How to handle invocations
- How to test locally before deploying
- How to deploy to AWS

## Quick Start

### 1. Setup Environment

```bash
# Copy environment template
cp ../../.env.example .env

# Edit .env and add your AWS credentials
# AWS_ACCESS_KEY_ID=your_key_here
# AWS_SECRET_ACCESS_KEY=your_secret_here
# AWS_REGION=us-west-2
```

### 2. Run Locally

```bash
# Build and start the agent
docker-compose up --build

# The agent will start on http://localhost:8080
```

### 3. Test It

In another terminal:

```bash
# Basic test
curl -X POST http://localhost:8080/invocations \
  -H "Content-Type: application/json" \
  -d '{"prompt": "Hello!"}'

# Ask a question
curl -X POST http://localhost:8080/invocations \
  -H "Content-Type: application/json" \
  -d '{"prompt": "What is AWS Bedrock?"}'

# Get creative
curl -X POST http://localhost:8080/invocations \
  -H "Content-Type: application/json" \
  -d '{"prompt": "Tell me a joke about cloud computing"}'
```

### 4. Stop the Agent

```bash
docker-compose down
```

## Deploy to AWS

Once you're happy with local testing, deploy to AgentCore Runtime:

```bash
# Enter the container
docker-compose exec hello-world-agent bash

# Configure for deployment
agentcore configure -e agent.py

# Deploy to AWS
agentcore launch

# Test the deployed agent
agentcore invoke '{"prompt": "Hello from the cloud!"}'

# Exit the container
exit
```

## How It Works

### agent.py
- Imports AgentCore and Strands libraries
- Creates an app and agent instance
- Defines an `invoke` function decorated with `@app.entrypoint`
- Extracts the prompt from the payload
- Passes it to the agent for processing
- Returns the result

### Docker Setup
- Uses ARM64 architecture (required by AgentCore Runtime)
- Installs Python 3.11 and required packages
- Exposes port 8080 for invocations
- Loads AWS credentials from `.env`

## Expected Response

```json
{
  "result": "Hello! I'm Claude, an AI assistant. How can I help you today?"
}
```

## Troubleshooting

### Port 8080 in use
```bash
# Kill process on port 8080
lsof -ti:8080 | xargs kill -9
```

### AWS Credentials Error
```bash
# Verify credentials
docker-compose exec hello-world-agent aws sts get-caller-identity
```

### Model Access Denied
Enable Claude Sonnet 4.0 in AWS Console:
1. Go to Amazon Bedrock
2. Click "Model access"
3. Enable Anthropic Claude models

## Next Steps

- Try **02-chatbot** to add conversation memory
- Explore **03-faq-bot** to learn about knowledge bases
- Move to **intermediate/** for custom tools and integrations

## Architecture

```
User Request
    ↓
Docker Container (port 8080)
    ↓
agent.py (invoke function)
    ↓
Strands Agent
    ↓
AWS Bedrock (Claude Sonnet 4.0)
    ↓
Response back to user
```