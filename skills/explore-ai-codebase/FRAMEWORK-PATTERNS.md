# Framework-Specific Exploration Patterns

Quick-reference for recognizing and exploring codebases built with specific AI/agent frameworks.

---

## MCP Servers (Model Context Protocol)

### Fingerprint
- Deps: `fastmcp`, `@modelcontextprotocol/sdk`, `mcp`
- Files: `mcp.json`, `mcp_composition_config.yaml`, `server.ts`, `server.py`
- Patterns: `FastMCP()`, `Server()`, `server.tool()`, `server.resource()`

### What to Look For
- **Transport**: stdio (local/Claude Desktop) vs HTTP/SSE (remote/production)
- **Tool definitions**: Code-defined (`@mcp.tool()`) vs config-driven (YAML + proxy)
- **Resource handlers**: Data providers exposed as `resource://` URIs
- **Prompt templates**: Pre-built instruction templates
- **Multi-server**: Multiple MCP servers on one port with path-based routing
- **Composition**: Tools proxied/composed from other servers

### Key Architecture
```
Client ──stdio/HTTP──> MCP Server
                         ├── Tools (LLM-invoked functions)
                         ├── Resources (read-only data)
                         └── Prompts (instruction templates)
```

### Exploration Priority
1. Server initialization (transport, capabilities)
2. Tool definitions (names, schemas, implementations)
3. Config file (YAML/JSON for declarative tool registration)
4. Middleware chain (auth, RBAC, validation, composition)
5. Tool composition/proxying logic
6. Authentication mechanism (token validation, per-server access control)

---

## LangGraph

### Fingerprint
- Deps: `langgraph`, `langchain-core`, `langchain-*`
- Files: `langgraph.json`, graph definitions with `StateGraph`
- Patterns: `StateGraph()`, `add_node()`, `add_edge()`, `add_conditional_edges()`, `compile()`

### What to Look For
- **State schema**: Pydantic model or TypedDict defining the shared state
- **Nodes**: Functions that read/write state (each is `State -> Partial<State>`)
- **Edges**: Static connections between nodes
- **Conditional edges**: Branching logic based on state values
- **Reducers**: How state keys aggregate values from multiple nodes
- **Checkpointers**: State persistence (Postgres, Redis, SQLite, memory)
- **Human-in-the-loop**: `interrupt_before` / `interrupt_after` on nodes
- **Subgraphs**: Nested graphs for modular workflows

### Key Architecture
```
START
  │
  ▼
┌──────────┐     ┌──────────┐
│  Node A  │────>│  Node B  │
└──────────┘     └────┬─────┘
                      │
              ┌───────┴───────┐
              │ conditional   │
              │    edge       │
              ▼               ▼
         ┌────────┐     ┌────────┐
         │ Node C │     │ Node D │
         └────┬───┘     └────┬───┘
              │              │
              └──────┬───────┘
                     ▼
                    END
```

### Exploration Priority
1. State schema (defines data flow between all nodes)
2. Graph definition (nodes, edges, conditional logic)
3. Individual node implementations
4. Tool bindings on LLM nodes
5. Checkpointer configuration (persistence)
6. Entry point and graph compilation

---

## CrewAI

### Fingerprint
- Deps: `crewai`, `crewai-tools`
- Files: `agents.yaml`, `tasks.yaml`, crew definitions
- Patterns: `Agent()`, `Task()`, `Crew()`, `@crew`, `@agent`, `@task`

### What to Look For
- **Agents**: Role, goal, backstory, tools, LLM assignment
- **Tasks**: Description, expected output, assigned agent
- **Crews**: Agent grouping with process type (sequential, hierarchical)
- **Tools**: Custom tools via `@tool` decorator or built-in tools
- **Knowledge**: Files in `knowledge/` directory for RAG
- **YAML config**: Agents and tasks defined declaratively in YAML
- **Memory**: Short-term, long-term, entity memory
- **Delegation**: Whether agents can delegate to each other

### Key Architecture
```
Crew
 ├── Agent A (role: researcher, tools: [search, read])
 │     └── Task 1: "Research topic X"
 ├── Agent B (role: writer, tools: [write])
 │     └── Task 2: "Write article based on research"
 └── Agent C (role: editor, tools: [edit])
       └── Task 3: "Review and polish article"

Process: sequential (A → B → C)
```

### Exploration Priority
1. Crew definition (agents, process type)
2. Agent definitions (roles, goals, tools) -- check both code and YAML
3. Task definitions (descriptions, dependencies)
4. Custom tool implementations
5. Knowledge/RAG configuration
6. Output/callback handling

---

## AutoGen

### Fingerprint
- Deps: `autogen-agentchat`, `autogen-core`, `autogen-ext`
- Patterns: `AssistantAgent`, `UserProxyAgent`, `GroupChat`, `RoundRobinGroupChat`, `SelectorGroupChat`, `Swarm`

### What to Look For
- **Agent types**: AssistantAgent, UserProxyAgent, custom agents
- **Team presets**: RoundRobinGroupChat, SelectorGroupChat, MagenticOne, Swarm
- **Handoffs**: `HandoffMessage` for agent-to-agent delegation
- **Termination conditions**: `MaxMessageTermination`, `TextMentionTermination`, custom
- **Tool use**: Functions registered to agents
- **Model clients**: `OpenAIChatCompletionClient`, `AzureOpenAI`, etc.
- **Code execution**: `DockerCommandLineCodeExecutor`, `LocalCommandLineCodeExecutor`

### Key Architecture
```
Team (RoundRobinGroupChat / SelectorGroupChat)
 ├── Agent A (AssistantAgent, model=gpt-4o, tools=[...])
 ├── Agent B (AssistantAgent, model=gpt-4o, tools=[...])
 └── Termination: MaxMessageTermination(10)

Messages flow: A → B → A → B → ... → Termination
```

### Exploration Priority
1. Team/group chat definition
2. Agent definitions (type, model, tools, system message)
3. Termination conditions
4. Handoff logic (if Swarm pattern)
5. Code execution setup (if present)
6. Custom agent implementations

---

## OpenAI Agents SDK

### Fingerprint
- Deps: `openai-agents`
- Patterns: `Agent()`, `Handoff()`, `InputGuardrail`, `OutputGuardrail`, `Runner.run()`

### What to Look For
- **Agents**: Name, instructions, model, tools, handoffs
- **Handoffs**: Agent-to-agent delegation with optional input filtering
- **Guardrails**: Input/output validation running in parallel with agent
- **Tools**: Python functions with automatic schema generation
- **Runner**: `Runner.run()` for executing agent loop
- **Tracing**: Built-in tracing for debugging
- **MCP integration**: Agents can call MCP server tools

### Key Architecture
```
Runner.run(agent, input)
       │
       ▼
┌──────────────┐
│  Triage Agent│
└──────┬───────┘
       │ handoff
  ┌────┴────┐
  ▼         ▼
Agent A   Agent B
(tools)   (tools)
```

### Exploration Priority
1. Agent definitions (instructions, tools, handoffs)
2. Handoff graph (which agents delegate to which)
3. Guardrail definitions
4. Tool implementations
5. Runner invocation (entry point)
6. MCP server connections (if any)

---

## RAG Pipelines

### Fingerprint
- Deps: `chromadb`, `pinecone`, `qdrant-client`, `weaviate`, `pgvector`, `llamaindex`, `langchain` with retrieval chains
- Patterns: `VectorStoreIndex`, `QueryEngine`, `Retriever`, `embed()`, `similarity_search()`

### What to Look For
- **Document ingestion**: How documents are loaded and preprocessed
- **Chunking strategy**: Fixed-size, semantic, recursive, sentence-based
- **Embedding model**: OpenAI, Cohere, HuggingFace, local models
- **Vector store**: Which database, index type, distance metric
- **Retrieval strategy**: Similarity search, hybrid (vector + keyword), MMR
- **Reranking**: Cross-encoder reranking stage
- **Prompt construction**: How retrieved context is injected into prompts
- **Evaluation**: Retrieval metrics, answer quality checks

### Key Architecture
```
Documents ──> Chunker ──> Embedder ──> Vector Store
                                           │
Query ──> Embedder ──> Similarity Search ──┘
                              │
                              ▼
                         Reranker (optional)
                              │
                              ▼
                    Context + Query ──> LLM ──> Answer
```

### Exploration Priority
1. Ingestion pipeline (document loading, chunking, embedding)
2. Vector store configuration (database, index, dimensions)
3. Retrieval logic (search type, top-k, filters)
4. Reranking stage (if present)
5. Prompt template (how context is formatted)
6. Evaluation/quality metrics

---

## A2A Protocol (Agent2Agent)

### Fingerprint
- Deps: `a2a-sdk`, custom HTTP server
- Files: `/.well-known/agent.json` (Agent Card)
- Patterns: `AgentCard`, `A2AServer`, `TaskManager`, `A2AClient`

### What to Look For
- **Agent Card**: Identity, capabilities, authentication, endpoint URL
- **Task lifecycle**: submitted → working → input-required → completed / failed
- **Messages and Parts**: TextPart, FilePart, DataPart
- **Artifacts**: Output files/data produced by task execution
- **Streaming**: SSE for real-time task updates
- **Discovery**: How agents find each other (well-known URL, registry)

### Key Architecture
```
A2A Client                    A2A Server
    │                             │
    │──discover (Agent Card)─────>│
    │<─capabilities───────────────│
    │                             │
    │──create task───────────────>│
    │<─task ID────────────────────│
    │                             │
    │──poll / stream─────────────>│
    │<─status updates─────────────│
    │<─artifacts──────────────────│
```

### Exploration Priority
1. Agent Card (`/.well-known/agent.json`)
2. Task manager implementation
3. Message/artifact handling
4. Streaming/SSE implementation
5. Authentication and security
6. Integration with internal agent logic

---

## Config-Driven / Custom Frameworks

### Fingerprint
- Large YAML/JSON config defining agents, tools, workflows
- Custom server code (FastAPI/Express/Go) with MCP or custom protocol
- Tool definitions as HTTP API wrappers, not code implementations
- Dynamic function generation from config schemas

### What to Look For
- **Config schema**: The Pydantic/Zod models that validate the config -- this IS the architecture
- **Config loader**: How config is loaded, validated, environment-variable-substituted
- **Tool registration**: How config entries become runtime tools
- **Dynamic code generation**: Functions created from schema at runtime
- **Multi-server management**: Multiple servers from one config
- **Middleware chain**: Execution order matters -- read it sequentially
- **Access control matrix**: Which clients/users can access which tools
- **Deployment modes**: Same codebase may run as HTTP server, stdio client, or worker

### Key Architecture
```
YAML Config
    │
    ▼
Config Loader + Pydantic Validation
    │
    ├──> Server Manager ──> Server A (port/path)
    │                   └──> Server B (port/path)
    │
    ├──> Tool Factory ──> Tool 1 (HTTP proxy)
    │                 └──> Tool 2 (HTTP proxy)
    │
    └──> Middleware Chain ──> Auth ──> RBAC ──> Handler
```

### Exploration Priority
1. **Config schema models** (Pydantic/Zod) -- read these FIRST
2. Config file (YAML/JSON) -- see the actual values
3. Config loader (validation, env var substitution)
4. Server/app initialization
5. Tool factory / dynamic registration
6. Middleware chain (in execution order)
7. Access control and authorization
8. Deployment configurations (env files, Docker, K8s)

---

## Mixed / Hybrid Systems

Many production systems combine multiple patterns. Common combinations:

- **MCP Server + LangGraph**: MCP tools that internally use LangGraph for complex workflows
- **FastAPI + CrewAI**: HTTP API that triggers CrewAI crew execution
- **RAG + Agent**: Agent that uses RAG as one of its tools
- **MCP + RBAC + Config-Driven**: Enterprise MCP servers with YAML tool definitions and database-backed authorization
- **A2A + MCP**: A2A protocol wrapping MCP servers for inter-agent communication

**Exploration strategy for hybrids:**
1. Identify the primary framework (the one that handles the main request lifecycle)
2. Map how secondary frameworks are integrated (as tools, subgraphs, middleware, etc.)
3. Trace a request through ALL layers, not just the primary framework
4. Check for dual-mode operation (HTTP + stdio, sync + async, etc.)

---

## Cross-Framework Patterns to Always Check

Regardless of framework, always investigate these:

### 1. Error Handling Philosophy
- **Fail-open**: Errors allow the request to proceed (dangerous for security)
- **Fail-closed**: Errors deny the request (safe default for auth/RBAC)
- **Retry with backoff**: Transient errors are retried
- **Circuit breaker**: Repeated failures disable the integration

### 2. Authentication Patterns
- **Token-based**: API key, JWT, pubtoken in headers
- **Per-request validation**: Each request validated against auth service
- **Cached validation**: Token validity cached with TTL
- **Per-server/per-tool ACLs**: Different auth requirements per endpoint

### 3. Configuration Hierarchy
- **Code defaults** → **Config file** → **Environment variables** → **Runtime overrides**
- Check which level takes precedence (usually env vars override config files)

### 4. Observability Stack
- **Logging**: Structured (JSON) vs unstructured, component prefixes, request-scoped IDs
- **Tracing**: Langfuse, LangSmith, OpenTelemetry, custom spans
- **Health checks**: HTTP endpoints, liveness/readiness probes
- **Metrics**: Prometheus, custom counters

### 5. Deployment Patterns
- **Single binary / process**: One entry point, one deployment
- **Multi-service**: Separate services communicating over network
- **Dual-transport**: Same code runs as HTTP server or stdio client
- **Config-per-environment**: dev/staging/prod env files with different settings
