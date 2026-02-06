---
name: explore-ai-codebase
description: Explore and explain any modern AI or agentic codebase with ASCII diagrams. Covers MCP servers, LangGraph agents, CrewAI crews, AutoGen teams, OpenAI Agents SDK, RAG pipelines, A2A protocol, multi-agent systems, and any agentic architecture. Use when the user asks to explain, explore, or understand an AI codebase, agent architecture, or agentic system.
license: MIT
metadata:
  author: prakx1
  version: "1.0"
---

# AI Codebase Explorer

Systematically explore and explain any modern AI or agentic codebase. Produces structured explanations with ASCII diagrams.

## When to Use

- User asks to "explain this codebase", "how does this work?", or "explore the architecture"
- Codebase involves: agents, MCP servers, LLM tools, RAG pipelines, multi-agent orchestration, AI workflows
- User mentions: agents, MCP, LangGraph, CrewAI, AutoGen, OpenAI SDK, A2A, tools, chains, graphs, crews, teams, retrieval, embeddings

## Phase 1: Identify the Codebase Type

Before exploring, determine **what kind** of AI codebase this is. Read the dependency file and entry points first.

### Fingerprinting Table

| Indicator | Codebase Type |
|---|---|
| `@modelcontextprotocol/sdk`, `fastmcp`, `mcp` in deps | MCP Server |
| `langgraph`, `StateGraph`, `add_node`, `add_edge` | LangGraph Agent |
| `crewai`, `Agent()`, `Task()`, `Crew()`, yaml agent defs | CrewAI System |
| `autogen`, `AssistantAgent`, `GroupChat`, `RoundRobinGroupChat` | AutoGen Multi-Agent |
| `openai-agents`, `Agent`, `Handoff`, `Guardrail` | OpenAI Agents SDK |
| `langchain`, `AgentExecutor`, `create_react_agent` | LangChain Agent (legacy) |
| `semantic-kernel` | Microsoft Semantic Kernel |
| `llamaindex`, `VectorStoreIndex`, `QueryEngine` | LlamaIndex RAG |
| `chromadb`, `pinecone`, `qdrant`, `weaviate`, `pgvector` | RAG Pipeline |
| `/.well-known/agent.json`, `A2AServer`, `TaskManager` | A2A Protocol Agent |
| `fastapi` + LLM calls + tool definitions | Custom Agent Framework |
| YAML-driven tool/agent configs + HTTP proxy | Config-Driven MCP/Agent Server |

**Action**: Once identified, use the matching exploration strategy from [FRAMEWORK-PATTERNS.md](references/FRAMEWORK-PATTERNS.md).

## Phase 2: Structural Discovery

Read files in this priority order. Stop early if you already understand the architecture.

### Priority 1: Identity Files (read all of these)
```
pyproject.toml / package.json / go.mod / requirements.txt  → Stack and dependencies
README.md / docs/                                           → Architecture overview
main.py / src/index.* / app.py / __main__.py               → Entry point and startup flow
```

### Priority 2: Configuration Files (read what exists)
```
*.yaml / *.yml config files     → Declarative definitions (tools, agents, servers, workflows)
.env / .env.example / env.*     → Environment variables (reveal integrations and secrets)
langgraph.json                  → LangGraph deployment config
agents.yaml / tasks.yaml        → CrewAI definitions
Dockerfile* / docker-compose*   → Deployment topology
```

### Priority 3: Core Architecture (explore directories)
```
Look for these directory patterns and read key files in each:
  tools/          → Tool/function definitions
  agents/         → Agent definitions or classes
  resources/      → MCP resources or data providers
  handlers/       → Request handlers
  middleware/     → Middleware chain (auth, RBAC, validation)
  core/           → Core orchestration logic
  config/         → Configuration loading and schemas
  prompts/        → Prompt templates
  chains/ graphs/ → Workflow definitions
  crews/ teams/   → Multi-agent groupings
  auth/ authz/    → Authentication and authorization
  utils/          → Shared utilities
  integrations/   → External API clients
```

### Priority 4: Supporting Files
```
tests/           → Test structure reveals architecture assumptions
schemas/         → Data models and validation
models/          → Domain models or LLM model configs
```

## Phase 3: Architecture Mapping

After reading files, map the architecture across these dimensions:

### Dimension 1: Control Flow
How do requests/tasks move through the system?

```
Questions to answer:
- What is the entry point? (HTTP server, CLI, stdio, event listener)
- How are requests routed? (router, graph, state machine, role assignment)
- What is the execution model? (sequential, parallel, graph-based, event-driven)
- Are there loops or cycles? (ReAct loops, retry logic, graph cycles)
```

### Dimension 2: Tool/Capability Layer
What can the system do?

```
Questions to answer:
- How are tools/functions defined? (code, YAML, JSON schema, decorators)
- How are tools registered? (auto-discovery, manual, config-driven)
- How are tools invoked? (LLM tool calling, direct dispatch, proxy/composition)
- Are tools composed or chained? (pipelines, multi-step, proxied)
```

### Dimension 3: State and Memory
What does the system remember?

```
Questions to answer:
- Is the system stateless or stateful?
- Where does state live? (in-memory, database, checkpoints, vector store)
- What is the state scope? (per-request, session, global, per-agent)
- Is there conversation memory or long-term memory?
```

### Dimension 4: Security and Access Control
Who can do what?

```
Questions to answer:
- How do clients authenticate? (API keys, tokens, OAuth, mTLS)
- How is authorization enforced? (RBAC, per-tool ACLs, middleware, fail-closed)
- Is input validated? (schemas, sanitization, injection protection)
- How are secrets managed? (env vars, vaults, config files)
```

### Dimension 5: Integrations and I/O
What does the system connect to?

```
Questions to answer:
- What external APIs or services does it call?
- What databases or stores does it use?
- What transport protocols? (HTTP, stdio, SSE, WebSocket, gRPC)
- How does it handle errors from external systems? (retries, circuit breakers, fallbacks)
```

### Dimension 6: Observability
How do you know what's happening?

```
Questions to answer:
- What logging framework? Structured or unstructured?
- Any tracing? (OpenTelemetry, Langfuse, LangSmith, custom)
- Health check endpoints?
- Metrics or monitoring?
```

## Phase 4: Produce the Explanation

### Output Structure

```markdown
# [Project Name] - Codebase Overview

**Type**: [MCP Server | LangGraph Agent | CrewAI System | RAG Pipeline | Multi-Agent System | Custom Framework]
**Stack**: [Python 3.x + FastMCP + FastAPI | TypeScript + LangGraph | etc.]
**Key Dependencies**: [list 3-7 most important]

## Architecture

[ASCII architecture diagram - see Diagram Guidelines below]

## How It Works (Startup → Request → Response)

[Step-by-step flow of a typical request through the system]
[Include file:function references for each step]

## Key Components

### [Component Name]
**Location**: `path/to/files`
**Purpose**: [1-2 sentences]
**Interactions**: [what it talks to]
[Optional: component-specific diagram]

### [Next Component]
...

## Tool/Capability Inventory

[Table or list of all tools/capabilities with purpose and implementation location]

## Security Model

[Authentication, authorization, input validation, secrets management]

## Configuration

[How the system is configured: env vars, YAML, code, etc.]

## Data Flows

[1-2 sequence diagrams of key flows]

## Observations

**Strengths**: [architectural strengths]
**Potential Issues**: [things to watch out for]
**Suggestions**: [improvements to consider]
```

## Diagram Guidelines

### Architecture Diagrams

Show the system boundary, major components, and data flow:

```
┌─────────────────────────────────────────┐
│              System Boundary            │
│                                         │
│  ┌──────────┐     ┌──────────────────┐  │
│  │ Component│────>│   Component B    │  │
│  │    A     │     │  ┌────┐ ┌────┐   │  │
│  └──────────┘     │  │Sub1│ │Sub2│   │  │
│                   │  └────┘ └────┘   │  │
│                   └────────┬─────────┘  │
│                            │            │
└────────────────────────────┼────────────┘
                             │
                             ▼
                      External Service
```

### Request Flow Diagrams

Show numbered steps with file references:

```
Client          Middleware      Handler         External
  │                │              │                │
  │──1. request──>│              │                │
  │               │──2. auth────>│                │
  │               │              │──3. API call──>│
  │               │              │<─4. response───│
  │<──5. result───│              │                │
```

### Data Flow Diagrams

Show transformations:

```
Config YAML ──> Loader ──> Pydantic Models ──> Server Manager
                              │
                              ▼
                        Tool Registration
```

### Decision Flow Diagrams

Show branching logic:

```
Request arrives
      │
      ├─ auth required? ──yes──> Validate token
      │                              │
      │                         ┌────┴────┐
      │                       valid     invalid
      │                         │         │
      │                         ▼         ▼
      │                      Continue   401 Deny
      │
      └─ no ──> Continue
```

### Tips
1. One concept per diagram; multiple simple > one complex
2. Label every arrow and box
3. Top-to-bottom or left-to-right flow
4. Use `──` for connections, `│` for vertical, `▼▲◄►` for direction
5. Group related items with box borders `┌┐└┘`

## Deep-Dive Techniques

### Trace a Request End-to-End

The most effective way to explain a codebase:

1. Pick a concrete scenario (e.g., "user calls a tool", "agent processes a task")
2. Start at the entry point and follow the code path
3. Note every file, function, middleware, and decision point
4. Document the return path
5. Draw the sequence diagram

### Read the Config Schema

In config-driven systems, the schema IS the architecture:

- Read Pydantic models / Zod schemas / TypeScript interfaces
- The fields and their types reveal what the system supports
- Enum values reveal the options/modes
- Optional vs required fields reveal what's configurable vs fixed

### Read the Middleware Chain

Middleware reveals cross-cutting concerns:

- Authentication / authorization
- Rate limiting
- Input validation / sanitization
- Logging / tracing
- Error handling
- Request transformation

**Read middleware in execution order**, not alphabetical order.

### Read the Tests

Tests reveal:
- What the system is supposed to do (happy paths)
- What can go wrong (error cases)
- How components are mocked (reveals boundaries)
- Integration vs unit tests (reveals architecture layers)

### Check the Deployment

Dockerfiles, CI pipelines, and K8s manifests reveal:
- How the system is packaged
- What environment variables it needs
- What ports/endpoints it exposes
- Health check mechanisms
- Multi-service topology

## Adaptive Detail Level

**Quick Overview** (user wants the gist):
- One architecture diagram
- Component list with one-line descriptions
- "How it works" in 5 bullets

**Standard Exploration** (default):
- Architecture diagram
- Component deep-dives with file references
- 1-2 flow diagrams
- Security and config summary
- Observations

**Comprehensive Analysis** (user wants everything):
- Multiple diagrams (architecture, flows, decisions, state)
- Every tool/capability documented
- Complete request traces
- Security analysis
- Config analysis
- Code quality observations
- Deployment analysis
- Suggestions for improvement

If unclear, default to Standard. Ask if the user wants more or less.

## Discovery Checklist

```
Exploration Progress:
- [ ] Codebase type identified (MCP / LangGraph / CrewAI / etc.)
- [ ] Dependencies and stack mapped
- [ ] Entry point and startup flow traced
- [ ] Configuration system understood
- [ ] All tools/capabilities cataloged
- [ ] Request flow traced end-to-end
- [ ] Security model documented
- [ ] State management understood
- [ ] External integrations identified
- [ ] Architecture diagram created
- [ ] Key flow diagrams created
- [ ] Observations and suggestions noted
```

## Pitfalls to Avoid

1. **Don't assume one framework**: Many production systems mix frameworks (e.g., FastMCP + LangGraph + custom middleware)
2. **Don't skip config files**: In declarative systems, YAML IS the architecture
3. **Don't ignore the middleware chain**: Security, auth, and validation live here
4. **Don't confuse tool definition with implementation**: Tools may be proxied to remote services
5. **Don't assume stateless**: Check for caches, sessions, checkpoints, databases
6. **Don't miss dual-mode systems**: Many codebases support both HTTP and stdio transport
7. **Don't overlook error handling**: Fail-open vs fail-closed reveals security posture
8. **Don't ignore environment management**: Different envs (dev/prod) may have wildly different configs

## Additional Resources

For framework-specific patterns, see [FRAMEWORK-PATTERNS.md](references/FRAMEWORK-PATTERNS.md).
For ASCII diagram templates, see [DIAGRAM-TEMPLATES.md](references/DIAGRAM-TEMPLATES.md).
