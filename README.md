# explore-ai-codebase

An agent skill that systematically explores and explains any modern AI or agentic codebase with ASCII diagrams.

## What It Does

Drop into any AI/agentic project and ask your agent to "explain this codebase." The skill will:

1. **Fingerprint** the codebase type (MCP server, LangGraph, CrewAI, AutoGen, RAG pipeline, etc.)
2. **Explore** files in priority order (deps, config, entry points, core logic)
3. **Map** the architecture across 6 dimensions (control flow, tools, state, security, integrations, observability)
4. **Explain** with structured output and ASCII diagrams

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

## Install

```bash
npx skills add prakx1/explore-ai-codebase
```

This installs the skill for whichever AI agents you have (Cursor, Claude Code, Codex, Copilot, Cline, and [35+ more](https://skills.sh)).

## Usage

Once installed, just ask your agent:

- "Explain this codebase"
- "How does this agent system work?"
- "Explore the architecture and draw diagrams"
- "What tools does this MCP server expose?"
- "Trace a request through the system end-to-end"

The skill automatically activates when it detects you're working with an AI/agentic codebase.

## What's Inside

```
skills/explore-ai-codebase/
├── SKILL.md               # Core exploration methodology (390 lines)
├── FRAMEWORK-PATTERNS.md  # Framework-specific guides for 9+ codebase types
└── DIAGRAM-TEMPLATES.md   # ASCII diagram templates for architecture, flows, security
```

### SKILL.md — The Core Methodology

A 4-phase systematic approach:

1. **Identify** — Fingerprint the codebase type using dependency/pattern matching
2. **Discover** — Read files in priority order (identity → config → core → tests)
3. **Map** — Analyze across 6 dimensions (control flow, tools, state, security, I/O, observability)
4. **Explain** — Produce structured output with diagrams, component deep-dives, and observations

### FRAMEWORK-PATTERNS.md — Framework-Specific Guides

Detailed exploration strategies for each codebase type, including:
- What to look for (key patterns, files, concepts)
- Architecture diagrams
- Exploration priority order
- Cross-framework patterns (error handling, auth, config hierarchy, observability)

### DIAGRAM-TEMPLATES.md — Ready-to-Use Diagram Templates

ASCII diagram templates for:
- System architecture (single-server, multi-server, dual-transport)
- Agent patterns (ReAct loops, graph workflows, crews, handoffs, team chats)
- Request flows (auth + RBAC, config-driven registration, RAG pipelines, A2A lifecycle)
- State machines, middleware chains, configuration hierarchies
- Security flows (token auth, RBAC decisions, access control matrices)
- Deployment (Kubernetes, multi-environment)
- Class/entity relationships

## Example Output

When you ask "explain this codebase," you get:

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

## Contributing

PRs welcome! If you find a codebase pattern the skill doesn't handle well, open an issue or submit a PR with the new pattern.

## License

MIT
