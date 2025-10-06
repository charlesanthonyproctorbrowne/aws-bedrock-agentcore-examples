<div align="center">

# AWS Bedrock AgentCore Examples

### Production-Ready AI Agent Infrastructure with Progressive Complexity

A comprehensive repository demonstrating real-world AI agent implementations using AWS Bedrock AgentCore, organized from foundational concepts to advanced autonomous systems.

[Getting Started](#quick-start) •
[Architecture](#architecture) •
[Examples](#examples) •
[Documentation](#documentation) •
[Contributing](#contributing)

---

</div>

## Overview

AWS Bedrock AgentCore is a comprehensive platform for deploying and operating AI agents securely at scale. This repository provides a structured learning path through progressively complex examples, each demonstrating production-ready patterns and best practices.

### Platform Capabilities

**AgentCore Runtime** <br/>
Serverless deployment and scaling for dynamic AI agents using any framework including LangGraph, CrewAI, and Strands Agents.

**AgentCore Memory** <br/>
Persistent knowledge management with event and semantic memory, enabling context-aware agents.

**AgentCore Identity** <br/>
Secure authentication and access management with OAuth 2.0 support and credential vault.

**AgentCore Tools** <br/>
Built-in code interpreter and browser automation capabilities for complex workflows.

**AgentCore Gateway** <br/>
Transform existing APIs into agent-accessible tools with automatic schema generation.

**AgentCore Observability** <br/>
Real-time monitoring, tracing, and metrics for production agent operations.

---

## Architecture

### Agent Infrastructure Model

```
┌─────────────────────────┐       ┌─────────────────────────┐
│   Agent A (CRM)         │       │   Agent B (Marketing)   │
│                         │       │                         │
│  ├── agent.py           │       │  ├── agent.py           │
│  │   imports:           │       │  │   imports:           │
│  │   • bedrock_client ──┼───┐   │  │   • bedrock_client ──┼───┐
│  │   • agent_logger ────┼───┤   │  │   • agent_logger ────┼───┤
│  │   • web_search ──────┼───┤   │  │   • web_search ──────┼───┤
│  ├── Dockerfile         │   │   │  ├── Dockerfile         │   │
│  └── docker-compose.yml │   │   │  └── docker-compose.yml │   │
└─────────────────────────┘   │   └─────────────────────────┘   │
                              │                                 │
                              └─────────────┬───────────────────┘
                                            │
                                            ▼
                            ┌───────────────────────────┐
                            │ shared-infrastructure/    │
                            │                           │
                            │ ├── aws-config/           │
                            │ │   └── bedrock_client.py │
                            │ ├── logging/              │
                            │ │   └── agent_logger.py   │
                            │ └── agent-tools/          │
                            │     └── web_search.py     │
                            └───────────────────────────┘
```

### Design Principles

**Self-Contained Agents** <br/>
Each agent is a complete, independent unit with its own Dockerfile, dependencies, and configuration. Agents can be built, tested, and deployed independently without affecting others.

**Shared Infrastructure** <br/>
Common utilities and tools are centralized in `shared-infrastructure/` to prevent code duplication. Agents import these modules as needed, following the DRY principle.

**Progressive Complexity** <br/>
Examples are organized by complexity level, providing a clear learning path from basic concepts to production-grade autonomous systems.

**Framework Agnostic** <br/>
All examples work with any AI framework. Replace the agent logic while keeping the same infrastructure patterns.

---

## Examples

### Basic

Single-agent, single-purpose implementations for learning AgentCore fundamentals.

| Example | Description | Key Concepts |
|---------|-------------|--------------|
| **01-hello-world** | Minimal viable agent | Entry points, invocations, basic setup |
| **02-chatbot** | Conversational agent with memory | State management, conversation history |
| **03-faq-bot** | Knowledge base Q&A agent | Data integration, retrieval patterns |

### Intermediate

Agents with custom tools, API integrations, and domain-specific business logic.

| Example | Description | Key Concepts |
|---------|-------------|--------------|
| **01-crm-assistant** | Contact and deal management | Custom tools, CRM integration patterns |
| **02-marketing-content** | SEO-optimized content generation | Content tools, analysis capabilities |
| **03-email-responder** | Automated email handling | Email parsing, response generation |

### Advanced

Multi-agent systems with orchestration, complex workflows, and specialized capabilities.

| Example | Description | Key Concepts |
|---------|-------------|--------------|
| **01-multi-agent-sales** | Lead qualification pipeline | Multi-agent orchestration, workflow design |
| **02-customer-support-router** | Intelligent ticket routing | Agent specialization, dynamic routing |
| **03-data-analyst** | SQL querying and reporting | Database integration, visualization |

### Expert

Production-grade autonomous systems demonstrating enterprise deployment patterns.

| Example | Description | Key Concepts |
|---------|-------------|--------------|
| **01-autonomous-business-analyst** | End-to-end business intelligence | Complex workflows, ML integration |
| **02-devops-assistant** | Infrastructure monitoring and deployment | AWS integration, automation patterns |

---

## Quick Start

### Prerequisites

* Docker and Docker Compose installed
* AWS Account with Bedrock access enabled
* Claude Sonnet 4.0 model access in Amazon Bedrock
* AWS credentials (Access Key ID and Secret Access Key)

### Initial Setup

```bash
# Clone the repository
git clone <repository-url>
cd ai-agent-examples

# Create environment configuration
cp .env.example .env

# Edit .env with your AWS credentials
# AWS_ACCESS_KEY_ID=your_key_here
# AWS_SECRET_ACCESS_KEY=your_secret_here
# AWS_REGION=us-west-2
```

### Run Your First Agent

```bash
# Navigate to hello-world example
cd basic/01-hello-world

# Copy environment file
cp ../../.env.example .env
# Add your AWS credentials to .env

# Build and start the agent
docker-compose up --build

# Test the agent (in another terminal)
curl -X POST http://localhost:8080/invocations \
  -H "Content-Type: application/json" \
  -d '{"prompt": "Hello!"}'
```

### Deploy to AWS

```bash
# Enter the running container
docker-compose exec hello-world-agent bash

# Configure for AWS deployment
agentcore configure -e agent.py

# Deploy to AgentCore Runtime
agentcore launch

# Test deployed agent
agentcore invoke '{"prompt": "Hello from the cloud!"}'
```

---

## Repository Structure

```
ai-agent-examples/
├── basic/                          # Foundational examples
│   ├── 01-hello-world/
│   ├── 02-chatbot/
│   └── 03-faq-bot/
│
├── intermediate/                   # Business use cases
│   ├── 01-crm-assistant/
│   ├── 02-marketing-content/
│   └── 03-email-responder/
│
├── advanced/                       # Multi-agent systems
│   ├── 01-multi-agent-sales/
│   ├── 02-customer-support-router/
│   └── 03-data-analyst/
│
├── expert/                         # Production patterns
│   ├── 01-autonomous-business-analyst/
│   └── 02-devops-assistant/
│
├── shared-infrastructure/          # Reusable components
│   ├── aws-config/                # AWS and Bedrock configuration
│   ├── logging/                   # Structured logging utilities
│   └── agent-tools/               # Common agent capabilities
│
└── scripts/                        # Development utilities
    ├── create-agent.sh            # Scaffold new agents
    ├── test-all-agents.sh         # Automated testing
    └── deploy-agent.sh            # Deployment automation
```

---

## Documentation

### Agent Structure

Each agent follows a consistent structure for maintainability and clarity:

```
agent-name/
├── agent.py              # Main agent implementation
├── Dockerfile            # Container configuration
├── docker-compose.yml    # Local development setup
├── requirements.txt      # Python dependencies
└── README.md            # Agent-specific documentation
```

### Shared Infrastructure

The `shared-infrastructure/` directory contains reusable Python modules:

**aws-config/** <br/>
Bedrock client configuration, AWS credential handling, and connection patterns used across all agents.

**logging/** <br/>
Structured logging with JSON formatting for CloudWatch compatibility and cross-agent analysis.

**agent-tools/** <br/>
Common capabilities like web search, email integration, and database access patterns.

### Development Workflow

**Create New Agent**
```bash
./scripts/create-agent.sh intermediate my-agent-name
```

**Test Locally**
```bash
cd intermediate/my-agent-name
docker-compose up --build
```

**Deploy to AWS**
```bash
docker-compose exec agent bash
agentcore configure -e agent.py
agentcore launch
```

---

## Key Concepts

### Session Isolation

AgentCore Runtime provides complete session isolation using microVMs. Each user session runs in its own protected environment, preventing cross-session data contamination and ensuring security for multi-tenant deployments.

### Consumption-Based Pricing

Pay only for resources actually consumed. AgentCore dynamically provisions what's needed without requiring resource pre-selection or right-sizing. CPU billing aligns with actual processing, typically excluding I/O wait periods.

### Built-in Authentication

Runtime assigns distinct identities to AI agents and integrates with corporate identity providers including Okta, Microsoft Entra ID, and Amazon Cognito. Supports both user-delegated and autonomous access patterns.

### Agent Observability

Specialized built-in tracing captures agent reasoning steps, tool invocations, and model interactions. Provides visibility into decision-making processes for debugging and auditing.

---

## Security Best Practices

**Credential Management** <br/>
Never commit `.env` files to version control. Use IAM roles in production instead of long-term access keys. Rotate credentials regularly and apply least-privilege permissions.

**Network Isolation** <br/>
Use VPC configurations for agents that access internal resources. Implement proper security group rules and network ACLs.

**Access Control** <br/>
Leverage AgentCore Identity for fine-grained access control. Implement OAuth 2.0 flows for user-delegated access patterns.

**Monitoring** <br/>
Enable CloudWatch logging and AgentCore Observability for all production agents. Set up alerts for anomalous behavior and error rates.

---

## Learning Path

**Step 1: Fundamentals** <br/>
Start with `basic/01-hello-world` to understand AgentCore basics, entry points, and invocation patterns.

**Step 2: State Management** <br/>
Progress to `basic/02-chatbot` to learn conversation memory and state management techniques.

**Step 3: Custom Tools** <br/>
Explore `intermediate/01-crm-assistant` to understand how to build and integrate custom tools.

**Step 4: Multi-Agent Systems** <br/>
Study `advanced/01-multi-agent-sales` to learn orchestration patterns and agent specialization.

**Step 5: Production Patterns** <br/>
Examine `expert/01-autonomous-business-analyst` for enterprise deployment and operational patterns.

---

## Contributing

Contributions are welcome. Each agent should follow these guidelines:

* Self-contained structure with complete documentation
* Clear README explaining the use case and architecture
* Working docker-compose setup for local testing
* Comments explaining "why" not "what"
* Adherence to DRY and SOLID principles
* Focus on real-world business applications

---

## Resources

**Official Documentation** <br/>
[AWS Bedrock AgentCore Documentation](https://aws.github.io/bedrock-agentcore-starter-toolkit/)

**Strands Agents SDK** <br/>
[Strands Documentation](https://docs.anthropic.com/strands)

**AWS Bedrock** <br/>
[Amazon Bedrock Documentation](https://docs.aws.amazon.com/bedrock/)

---

## License

This project is provided for educational and experimental purposes. See LICENSE file for details.

---

<div align="center">

**Built with AWS Bedrock AgentCore**

Production-ready AI agent infrastructure for modern applications

</div>