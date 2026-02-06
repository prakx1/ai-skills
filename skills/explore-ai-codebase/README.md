# explore-ai-codebase

Systematically explore and explain any modern AI or agentic codebase with ASCII diagrams.

## How It Works

Drop into any AI/agentic project and ask your agent to "explain this codebase." The skill follows a 4-phase methodology:

1. **Identify** — Fingerprint the codebase type using dependency and pattern matching
2. **Discover** — Read files in priority order (deps → config → entry points → core → tests)
3. **Map** — Analyze across 6 dimensions (control flow, tools, state, security, integrations, observability)
4. **Explain** — Produce structured output with ASCII diagrams, component deep-dives, and observations

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Core exploration methodology — the main instructions the agent follows |
| `FRAMEWORK-PATTERNS.md` | Framework-specific guides for 9+ codebase types (MCP, LangGraph, CrewAI, AutoGen, OpenAI SDK, RAG, A2A, config-driven, hybrid) |
| `DIAGRAM-TEMPLATES.md` | Ready-to-use ASCII diagram templates for architecture, flows, security, deployment, and more |

## Supported Codebase Types

| Type | Frameworks / Indicators |
|------|------------------------|
| MCP Servers | FastMCP, `@modelcontextprotocol/sdk`, config-driven tool registration |
| LangGraph Agents | `StateGraph`, nodes, edges, checkpointers |
| CrewAI Systems | Agents, Tasks, Crews, YAML role definitions |
| AutoGen Multi-Agent | `GroupChat`, `AssistantAgent`, teams, handoffs |
| OpenAI Agents SDK | `Agent`, `Handoff`, `Guardrail`, `Runner` |
| RAG Pipelines | ChromaDB, Pinecone, Qdrant, LlamaIndex, embeddings |
| A2A Protocol | Agent Cards, `/.well-known/agent.json`, task lifecycle |
| Custom Frameworks | FastAPI + LLM tools, YAML-driven configs, middleware chains |
| Hybrid Systems | Any combination of the above |

## Example Output

When you ask "explain this codebase," you get structured output like:

```
# MyProject - Codebase Overview

**Type**: Config-Driven MCP Server
**Stack**: Python 3.11 + FastMCP + FastAPI + Pydantic
**Key Dependencies**: fastmcp, pydantic-settings, pyyaml, uvicorn

## Architecture

┌─────────────────────────────────────────┐
│          MCP Composition Server         │
│                                         │
│  Config YAML ──> Loader ──> Servers     │
│                              │          │
│                     ┌────────┼────────┐ │
│                     ▼        ▼        ▼ │
│                  Tool A   Tool B   Tool C│
└─────────────────────────────────────────┘

## How It Works
1. main.py reads CLI args (--transport http|stdio)
2. ConfigLoader loads YAML → Pydantic models
3. ServerManager creates FastMCP servers per config
4. Tools registered from agent configs with RBAC filtering
5. Middleware chain: Auth → RBAC → Composition → Handler

## Key Components
...

## Observations
**Strengths**: Config-driven, fail-closed security, dual-transport
**Potential Issues**: In-memory state only, no retry on external APIs
```

## Usage Examples

Once installed, just ask your agent:

- "Explain this codebase"
- "How does this agent system work?"
- "Explore the architecture and draw diagrams"
- "What tools does this MCP server expose?"
- "Trace a request through the system end-to-end"
- "Give me a quick overview" or "Give me a comprehensive analysis"
