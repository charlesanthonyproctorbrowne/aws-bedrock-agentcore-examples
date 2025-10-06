<div align="center">

# AWS Bedrock AgentCore Examples

### Production Infrastructure for AI Agents That Actually Ship

A repository focused on the engineering reality of production AI agents: the security, scalability, and integration work that determines whether agents deliver business value or remain prototypes.

[Quick Start](#quick-start) •
[Why This Matters](#why-this-matters) •
[Platform Services](#platform-services) •
[Examples](#examples-by-complexity)

---

</div>

## Why This Matters

Building a basic AI agent takes an afternoon. LLM generates output, that output determines the next action, the cycle repeats. With Python and a few hundred lines of code, you can demonstrate the concept.

But between that afternoon prototype and a production system serving real customers lies months of engineering work that has nothing to do with AI.

> **The engineering overhead matters more than the AI because that's where you actually spend time and money.**

This repository exists because the hard part of production AI is not the AI. The LLM is a commodity. The infrastructure is expensive.

AWS Bedrock AgentCore provides the scaffolding that otherwise takes months to build, letting you focus on agent behavior, business integration, and organizational adoption.

### What You Avoid Building

<table>
<tr>
<td width="50%" valign="top">

**Security and Compliance**

PII detection, content filtering, access controls, audit logging. Every system that touches customer data needs comprehensive compliance coverage.

Building from scratch means:
- Pattern matching for credit cards
- Regex for addresses
- Entity recognition for medical terms
- Validation against known PII patterns

This code is tedious and required everywhere users input text.

</td>
<td width="50%" valign="top">

**Cost Management**

Token counting before expensive LLM calls, caching for repeated queries, intelligent model selection based on task complexity.

Production systems need:
- Pre-processing token analysis
- Query optimization
- Model selection logic
- Spend monitoring

Without careful engineering, token costs accumulate quickly.

</td>
</tr>
<tr>
<td width="50%" valign="top">

**Scalability Engineering**

Queuing, async processing, concurrency controls, connection pooling, rate limiting, circuit breakers.

Moving from 50 requests daily to 500 exposes scaling problems immediately. These are distributed systems challenges that any production system eventually faces.

</td>
<td width="50%" valign="top">

**Integration Framework**

Error handling, retry logic, data transformation, and testing for every external system.

Production agents connect to:
- CRMs and email platforms
- Databases and internal APIs  
- Third-party services

Building reliable connective tissue between AI and existing infrastructure is not simple.

</td>
</tr>
</table>

**Observability Infrastructure**

When you are going back and forth between different services and systems constantly, observability becomes essential. Structured logging, performance metrics, trace visualization. Understanding what your agent is doing and why it made specific decisions requires comprehensive instrumentation.

---

**AgentCore handles this engineering scaffolding.** The foundation models are accessible through APIs. The orchestration infrastructure is managed. You build business solutions, not research infrastructure.

---

## Platform Services

AWS Bedrock AgentCore provides composable services that work together or independently. Each service addresses specific production challenges that otherwise require months of custom development.

<details>
<summary><strong>AgentCore Runtime</strong> • Serverless deployment and scaling</summary>

<br/>

Runtime is purpose-built infrastructure for agentic workloads with extended runtime support, fast cold starts, true session isolation, and built-in identity management. Deploy agents using any framework (LangGraph, CrewAI, Strands Agents) without managing servers, containers, or scaling infrastructure.

**Key capabilities:**
- Session isolation using microVMs prevents cross-session data contamination
- Consumption-based pricing charges only for active processing time
- Built-in authentication integrates with corporate identity providers
- Specialized tracing captures agent reasoning steps and tool invocations

```python
from bedrock_agentcore import BedrockAgentCoreApp

app = BedrockAgentCoreApp()

@app.entrypoint
def production_agent(request):
    return agent.process(request['query'])

# Your function is now a production-ready API server
# with health monitoring, streaming support, and AWS integration
```

</details>

<details>
<summary><strong>AgentCore Memory</strong> • Persistent knowledge management</summary>

<br/>

Memory eliminates complex infrastructure management while providing full control over what agents remember. Supports both short-term memory for multi-turn conversations and long-term memory shared across agents and sessions.

Built-in policies for user preferences, summarization, and semantic extraction. Custom policies for specialized needs. Data stored encrypted with namespace-based segmentation.

</details>

<details>
<summary><strong>AgentCore Identity</strong> • Secure authentication and access management</summary>

<br/>

Identity provides workload identity management compatible with existing identity providers. Eliminates user migration or rebuilding authentication flows. Secure token vault minimizes consent fatigue while maintaining robust access controls.

Supports just-enough access and secure permission delegation for agents accessing AWS resources and third-party tools. Handles OAuth 2.0 flows (both client credentials and authorization code grants) with automatic token refresh.

**Key features:**
- Agent identity directory centralizes all workload identities
- Token vault securely stores OAuth tokens and API keys
- Dual authentication model for inbound and outbound connections
- Integration with Okta, Microsoft Entra ID, Amazon Cognito

</details>

<details>
<summary><strong>AgentCore Gateway</strong> • Transform APIs into agent-accessible tools</summary>

<br/>

Gateway provides secure discovery and connection to tools at scale. Convert APIs, Lambda functions, and existing services into Model Context Protocol (MCP) compatible tools with minimal code.

Supports OpenAPI, Smithy, and Lambda as input types. One-click integration with Salesforce, Slack, Jira, Asana, Zendesk. Comprehensive ingress and egress authentication in a fully-managed service.

```python
from bedrock_agentcore_starter_toolkit.operations.gateway.client import GatewayClient

client = GatewayClient(region_name="us-west-2")

gateway = client.create_mcp_gateway(
    name="customer-data-gateway",
    enable_semantic_search=True,
    exception_level="DEBUG"
)
```

**Gateway handles:**
- Semantic search for contextual tool discovery
- Authentication, authorization, throttling
- Custom request/response transformation
- Multitenancy and tool selection

</details>

<details>
<summary><strong>AgentCore Code Interpreter</strong> • Secure code execution</summary>

<br/>

Code Interpreter enables agents to execute code for complex workflows and data analysis while meeting enterprise security requirements. Advanced configuration support with seamless framework integration.

Use cases include calculations, data processing, chart generation, and programmatic analysis within agent workflows.

</details>

<details>
<summary><strong>AgentCore Browser</strong> • Cloud-based browser runtime</summary>

<br/>

Browser provides fast, secure, cloud-based browsing to enable agents to interact with websites at scale. Enterprise-grade security, comprehensive observability, automatic scaling without infrastructure management.

Enables agents to navigate websites, fill forms, extract data, and interact with web-only interfaces that lack programmatic APIs.

</details>

<details>
<summary><strong>AgentCore Observability</strong> • Real-time monitoring and tracing</summary>

<br/>

Observability provides unified operational dashboards with OpenTelemetry compatible telemetry. Detailed visualizations of each agent workflow step enable debugging and quality monitoring at scale.

Built-in dashboards reveal performance bottlenecks and interaction failures. Token usage tracking, cost per operation, latency metrics, error rates, and session analytics.

</details>

---

## Architecture

### Infrastructure Model

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

> **Four Foundational Patterns**
>
> Every agent in this repository follows consistent architectural principles that enable independent development, shared infrastructure, and progressive learning.

<table>
<tr>
<td width="50%" valign="top">

**Self-Contained Agents**

Each agent is a complete, independent unit. Own Dockerfile, dependencies, configuration.

Build, test, and deploy independently without affecting other agents. Complete isolation enables parallel development and reduces deployment risk.

</td>
<td width="50%" valign="top">

**Shared Infrastructure**

Common utilities centralized in shared-infrastructure to prevent code duplication. Agents import modules as needed.

When you copy-paste the same AWS connection logic to three agents, that logic belongs in shared infrastructure.

</td>
</tr>
<tr>
<td width="50%" valign="top">

**Progressive Complexity**

Examples organized by complexity level provide a clear learning path.

Start with foundational concepts. Progress through business use cases. Advance to multi-agent systems. Study production patterns.

</td>
<td width="50%" valign="top">

**Framework Agnostic**

All examples work with any AI framework. Replace the agent logic while keeping infrastructure patterns.

The orchestration, security, and integration work remains constant regardless of which framework generates decisions.

</td>
</tr>
</table>

---

## Examples by Complexity

### Basic: Foundational Concepts

Single-agent, single-purpose implementations for learning AgentCore fundamentals.

| Example | Purpose | Infrastructure Focus |
|---------|---------|---------------------|
| **hello-world** | Minimal viable agent | Entry points, invocations, deployment basics |
| **chatbot** | Conversational agent | State management, conversation history |
| **faq-bot** | Knowledge base Q&A | Data integration, retrieval patterns |

> These examples demonstrate core AgentCore concepts without business complexity. Focus on understanding how agents deploy, how invocations work, and how state persists across interactions.

---

### Intermediate: Business Integration

Agents with custom tools, API integrations, and domain-specific business logic.

| Example | Business Value | Engineering Challenges |
|---------|---------------|----------------------|
| **crm-assistant** | Contact and deal management | Custom tools, CRM integration patterns |
| **marketing-content** | SEO-optimized content generation | Content tools, analysis capabilities |
| **email-responder** | Automated email handling | Email parsing, response generation |

> These examples show how agents integrate with existing business systems. The engineering work focuses on building reliable connective tissue between AI and enterprise infrastructure.

---

### Advanced: Multi-Agent Systems

Complex workflows with orchestration, specialized agents, and sophisticated capabilities.

| Example | System Architecture | Orchestration Patterns |
|---------|-------------------|----------------------|
| **multi-agent-sales** | Lead qualification pipeline | Agent specialization, workflow design |
| **customer-support-router** | Intelligent ticket routing | Dynamic routing, agent coordination |
| **data-analyst** | SQL querying and reporting | Database integration, visualization |

> These examples demonstrate how multiple specialized agents work together. Orchestration logic determines which agent handles each task. Inter-agent communication patterns become critical.

---

### Expert: Production Patterns

Enterprise-grade autonomous systems demonstrating operational excellence.

| Example | Production Capabilities | Operational Excellence |
|---------|----------------------|----------------------|
| **autonomous-business-analyst** | End-to-end business intelligence | Complex workflows, ML integration, monitoring |
| **devops-assistant** | Infrastructure monitoring and deployment | AWS integration, automation patterns, reliability |

> These examples show production-ready patterns for agents that operate autonomously. Comprehensive error handling, observability, security controls, and operational runbooks.

---

## Quick Start

> **Prerequisites:** AWS Account with Bedrock access • Claude Sonnet 4.0 model enabled • Docker and Docker Compose installed • AWS credentials

<table>
<tr>
<td width="33%" valign="top">

### Setup

```bash
git clone <repository-url>
cd bedrock-agentcore-examples

cp .env.example .env
```

Edit `.env` with your AWS credentials:
```
AWS_ACCESS_KEY_ID=your_key
AWS_SECRET_ACCESS_KEY=your_secret
AWS_REGION=us-west-2
```

</td>
<td width="33%" valign="top">

### Run First Agent

```bash
cd basic/01-hello-world

cp ../../.env.example .env
# Add AWS credentials

docker-compose up --build
```

Test locally (in another terminal):
```bash
curl -X POST \
  http://localhost:8080/invocations \
  -H "Content-Type: application/json" \
  -d '{"prompt": "Hello!"}'
```

</td>
<td width="33%" valign="top">

### Deploy to AWS

```bash
docker-compose exec \
  hello-world-agent bash

agentcore configure \
  -e agent.py
  
agentcore launch
```

Test deployed agent:
```bash
agentcore invoke \
  '{"prompt": "Hello from production!"}'
```

</td>
</tr>
</table>

AWS Account with Bedrock access enabled. Claude Sonnet 4.0 model access in Amazon Bedrock. Docker and Docker Compose installed locally. AWS credentials (Access Key ID and Secret Access Key).

### Setup

```bash
git clone <repository-url>
cd bedrock-agentcore-examples

cp .env.example .env
# Edit .env with your AWS credentials:
# AWS_ACCESS_KEY_ID=your_key_here
# AWS_SECRET_ACCESS_KEY=your_secret_here
# AWS_REGION=us-west-2
```

### Run First Agent

```bash
cd basic/01-hello-world

cp ../../.env.example .env
# Add your AWS credentials to .env

docker-compose up --build

# Test locally (in another terminal)
curl -X POST http://localhost:8080/invocations \
  -H "Content-Type: application/json" \
  -d '{"prompt": "Hello!"}'
```

### Deploy to AWS

```bash
docker-compose exec hello-world-agent bash

agentcore configure -e agent.py
agentcore launch

agentcore invoke '{"prompt": "Hello from production!"}'
```

---

## Repository Structure

```
bedrock-agentcore-examples/
├── basic/                          # Foundational concepts
│   ├── 01-hello-world/
│   ├── 02-chatbot/
│   └── 03-faq-bot/
│
├── intermediate/                   # Business integration
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
│   ├── aws-config/                # Bedrock client configuration
│   ├── logging/                   # Structured logging
│   └── agent-tools/               # Common capabilities
│
└── scripts/                        # Development utilities
    ├── create-agent.sh
    ├── test-all-agents.sh
    └── deploy-agent.sh
```

Each agent follows consistent structure:

```
agent-name/
├── agent.py              # Main implementation
├── Dockerfile            # Container configuration
├── docker-compose.yml    # Local development
├── requirements.txt      # Dependencies
└── README.md            # Documentation
```

---

## Shared Infrastructure Components

The shared-infrastructure directory contains reusable Python modules that prevent code duplication across agents.

<table>
<tr>
<td width="33%" valign="top">

### aws-config/

Bedrock client configuration, AWS credential handling, connection patterns.

Every agent needs to connect to Bedrock. This module centralizes that logic so you write it once.

```python
from shared_infrastructure.aws_config \
  import get_bedrock_client

client = get_bedrock_client()
```

</td>
<td width="33%" valign="top">

### logging/

Structured logging with JSON formatting for CloudWatch compatibility.

Consistent log format across all agents enables cross-agent analysis and operational insights.

```python
from shared_infrastructure.logging \
  import setup_logger

logger = setup_logger('my-agent')
logger.info('Processing request', 
  extra={'user_id': user_id})
```

</td>
<td width="33%" valign="top">

### agent-tools/

Common capabilities like web search, email integration, database access.

When multiple agents need the same tool, it belongs here.

```python
from shared_infrastructure.agent_tools \
  import search_web

results = search_web(
  'current market trends')
```

</td>
</tr>
</table>

---

## Development Workflow

<table>
<tr>
<td width="33%" valign="top">

### Create New Agent

```bash
./scripts/create-agent.sh \
  intermediate \
  my-agent-name

cd intermediate/my-agent-name
```

Edit `agent.py` to implement your logic. Update `README.md` with agent description.

</td>
<td width="33%" valign="top">

### Test Locally

```bash
cp ../../.env.example .env
# Add AWS credentials

docker-compose up --build
```

```bash
# Test the agent
curl -X POST \
  http://localhost:8080/invocations \
  -H "Content-Type: application/json" \
  -d '{"prompt": "test query"}'
```

</td>
<td width="33%" valign="top">

### Deploy to Production

```bash
docker-compose exec agent bash

agentcore configure -e agent.py
agentcore launch
```

```bash
# Monitor deployment
agentcore invoke \
  '{"prompt": "production test"}'
```

</td>
</tr>
</table>

---

## Learning Path

The examples provide a structured progression from fundamentals to production patterns.

<table>
<tr>
<td width="20%" align="center" valign="top">

**Step 1**

Understanding Runtime

</td>
<td width="80%" valign="top">

Start with `basic/hello-world` to understand AgentCore entry points, invocation patterns, and deployment mechanics. See how a simple function becomes a production API.

</td>
</tr>
<tr>
<td width="20%" align="center" valign="top">

**Step 2**

State Management

</td>
<td width="80%" valign="top">

Progress to `basic/chatbot` to learn conversation memory and state persistence. Understand how agents maintain context across multiple interactions.

</td>
</tr>
<tr>
<td width="20%" align="center" valign="top">

**Step 3**

Custom Tools

</td>
<td width="80%" valign="top">

Explore `intermediate/crm-assistant` to build and integrate custom tools. Learn how agents access business systems and external services.

</td>
</tr>
<tr>
<td width="20%" align="center" valign="top">

**Step 4**

Orchestration

</td>
<td width="80%" valign="top">

Study `advanced/multi-agent-sales` to understand multi-agent systems. See how specialized agents coordinate to handle complex workflows.

</td>
</tr>
<tr>
<td width="20%" align="center" valign="top">

**Step 5**

Production Operations

</td>
<td width="80%" valign="top">

Examine `expert/autonomous-business-analyst` for enterprise patterns. Learn observability, error handling, security controls, and operational excellence.

</td>
</tr>
</table>

---

## Key Technical Concepts

> **Critical Infrastructure Capabilities**
>
> AgentCore Runtime provides four foundational capabilities that determine whether agents can operate reliably in production environments.

<table>
<tr>
<td width="50%" valign="top">

**Session Isolation**

AgentCore Runtime provides complete session isolation using microVMs. Each user session runs in its own protected environment.

After session completion, the entire microVM terminates and memory is sanitized. This prevents cross-session data contamination and ensures security for multi-tenant deployments.

</td>
<td width="50%" valign="top">

**Consumption-Based Pricing**

Pay only for resources actually consumed. AgentCore dynamically provisions what is needed without requiring resource pre-selection.

CPU billing aligns with actual processing time, typically excluding I/O wait periods when agents wait for LLM responses while maintaining session state.

</td>
</tr>
<tr>
<td width="50%" valign="top">

**Built-in Authentication**

Runtime assigns distinct identities to AI agents. Integrates with corporate identity providers (Okta, Microsoft Entra ID, Amazon Cognito).

Enables end users to authenticate into only the agents they have access to. Supports both user-delegated and autonomous access patterns using OAuth or API keys.

</td>
<td width="50%" valign="top">

**Agent Observability**

Specialized built-in tracing captures agent reasoning steps, tool invocations, and model interactions.

Provides visibility into decision-making processes. Critical capability for debugging and auditing AI agent behaviors in production.

</td>
</tr>
</table>

---

## Security Best Practices

<details>
<summary><strong>View Security Guidelines</strong></summary>

<br/>

**Credential Management**

Never commit .env files to version control. Use IAM roles in production instead of long-term access keys. Rotate credentials regularly. Apply least-privilege permissions.

**Network Isolation**

Use VPC configurations for agents accessing internal resources. Implement proper security group rules and network ACLs. Enable AWS PrivateLink for private connectivity.

**Access Control**

Leverage AgentCore Identity for fine-grained access control. Implement OAuth 2.0 flows for user-delegated access. Use workload identities for agent-to-agent communication.

**Monitoring and Auditing**

Enable CloudWatch logging and AgentCore Observability for all production agents. Set up alerts for anomalous behavior and error rates. Maintain audit trails for compliance requirements.

</details>

---

## Why Speed Matters

> **Behavioral Economics of AI Adoption**
>
> When you deploy working systems in weeks instead of months, you prove value before organizational skepticism sets in.

You avoid sunk cost traps because initial investment is low. You learn from real users quickly, iterating based on actual behavior rather than specifications.

Quick wins change the organizational calculus. They establish trust that makes subsequent AI initiatives easier to approve. Fast iteration with managed services lets you redirect engineering investment into business-value work immediately rather than spending months on infrastructure that delivers nothing until complete.

**The behavioral trap:** building feels like progress and innovation, buying feels like admitting defeat.

**The calculation:** If managed services cost £50K annually but save £400K in engineering time and six months of calendar time, the decision should be obvious.

---

## When Building Makes Sense

There are genuine cases where building agent infrastructure yourself creates value:

| Scenario | When It Applies | Typical Scale |
|----------|----------------|---------------|
| **Unique Requirements** | Specialized hardware, custom model architectures, or integration patterns AWS does not provide | ~5% of use cases |
| **Scale Economics** | Hundreds of agents processing millions of requests daily with existing platform team | Extreme scale operations |
| **Strategic Differentiation** | Agent infrastructure itself represents competitive advantage, not just using agents | AI-focused product companies |

For everyone else, the default should be leveraging managed services. Focus engineering effort where it creates value: prompt engineering and agent behavior, business system integration, and organizational adoption.

---

## Contributing

Contributions focused on real-world business applications are welcome. Each agent should follow these guidelines:

Self-contained structure with complete documentation. Clear README explaining the use case and architecture. Working docker-compose setup for local testing. Comments explaining why not what. Adherence to DRY and SOLID principles.

---

## Resources

[AWS Bedrock AgentCore Documentation](https://aws.github.io/bedrock-agentcore-starter-toolkit/)

[Strands Agents SDK Documentation](https://docs.anthropic.com/strands)

[Amazon Bedrock Documentation](https://docs.aws.amazon.com/bedrock/)

[AgentCore Code Samples](https://github.com/awslabs/amazon-bedrock-agentcore-samples/)

---

<div align="center">

**The engineering overhead matters more than the AI**

Production infrastructure for agents that actually ship

</div>