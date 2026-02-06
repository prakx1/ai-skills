# ASCII Diagram Templates

Ready-to-use templates for common AI/agentic architecture diagrams.

## Box Drawing Characters

```
Single:  ─ │ ┌ ┐ └ ┘ ├ ┤ ┬ ┴ ┼
Double:  ═ ║ ╔ ╗ ╚ ╝ ╠ ╣ ╦ ╩ ╬
Rounded: ╭ ╮ ╰ ╯
Arrows:  → ← ↑ ↓ ▶ ◀ ▲ ▼ ──> <──
```

---

## System Architecture Templates

### Single-Server

```
┌─────────────────────────────────────┐
│            Server Process           │
│                                     │
│  ┌─────────────────────────────┐    │
│  │       Request Handler       │    │
│  └──────────┬──────────────────┘    │
│             │                       │
│  ┌──────────▼──────────────────┐    │
│  │      Middleware Chain        │    │
│  │  [Auth] → [RBAC] → [Log]   │    │
│  └──────────┬──────────────────┘    │
│             │                       │
│  ┌──────────▼──────────────────┐    │
│  │     Tool/Agent Router       │    │
│  └──┬────────┬────────┬───────┘    │
│     ▼        ▼        ▼            │
│  ┌─────┐ ┌─────┐ ┌─────┐          │
│  │Tool │ │Tool │ │Tool │          │
│  │  A  │ │  B  │ │  C  │          │
│  └──┬──┘ └──┬──┘ └──┬──┘          │
└─────┼───────┼───────┼──────────────┘
      │       │       │
      ▼       ▼       ▼
   External APIs / Services
```

### Multi-Server on Single Port

```
┌──────────────────────────────────────────┐
│          FastAPI Application (port 9001) │
│                                          │
│  ┌──────────────────┐ ┌───────────────┐  │
│  │  /internal/*     │ │ /external/*   │  │
│  │  ┌────────────┐  │ │ ┌──────────┐  │  │
│  │  │  Server A  │  │ │ │ Server B │  │  │
│  │  │  (auth:yes)│  │ │ │(auth:no) │  │  │
│  │  │  5 tools   │  │ │ │ 3 tools  │  │  │
│  │  └────────────┘  │ │ └──────────┘  │  │
│  └──────────────────┘ └───────────────┘  │
│                                          │
│  /health ──> Global health check         │
└──────────────────────────────────────────┘
```

### Dual-Transport System

```
┌──────────────── HTTP Mode ──────────────┐
│  Client ──HTTP──> FastAPI ──> Middleware │
│                              ──> Handler│
└─────────────────────────────────────────┘

┌──────────────── STDIO Mode ─────────────┐
│  Claude/Cursor ──stdio──> FastMCP       │
│     (auth at startup, tools pre-filtered)│
└─────────────────────────────────────────┘

Same codebase, different entry paths in main.py
```

---

## Agent Architecture Templates

### ReAct Loop (LangGraph / Custom)

```
        ┌──────────────┐
        │   Observe    │ <── User input / tool output
        └──────┬───────┘
               │
               ▼
        ┌──────────────┐
        │    Think     │ <── LLM reasons about state
        └──────┬───────┘
               │
          ┌────┴────┐
          │         │
     need tool?   done?
          │         │
          ▼         ▼
   ┌──────────┐  Return
   │   Act    │  Answer
   │(tool call)│
   └──────┬───┘
          │
          └──── back to Observe
```

### Graph-Based Workflow (LangGraph)

```
       START
         │
         ▼
    ┌─────────┐
    │ Classify │
    └────┬────┘
         │
    ┌────┴────┐
    │ cond.   │
    │ edge    │
    ▼         ▼
┌───────┐ ┌───────┐
│ Path A│ │ Path B│
└───┬───┘ └───┬───┘
    │         │
    └────┬────┘
         │
         ▼
    ┌─────────┐
    │Summarize│
    └────┬────┘
         │
         ▼
        END

State: { messages, classification, result }
Checkpointer: PostgresSaver / MemorySaver
```

### Role-Based Crew (CrewAI)

```
┌─────────────────── Crew ───────────────────┐
│  Process: sequential                        │
│                                             │
│  ┌──────────┐    ┌──────────┐    ┌───────┐ │
│  │Researcher│───>│  Writer  │───>│Editor │ │
│  │          │    │          │    │       │ │
│  │Tools:    │    │Tools:    │    │Tools: │ │
│  │ - search │    │ - write  │    │ - edit│ │
│  │ - scrape │    │          │    │       │ │
│  └──────────┘    └──────────┘    └───────┘ │
│                                             │
│  Task 1 ──────> Task 2 ──────> Task 3      │
└─────────────────────────────────────────────┘
```

### Multi-Agent Handoff (OpenAI SDK / Swarm)

```
┌──────────────┐
│ Triage Agent │
└──────┬───────┘
       │ handoff
  ┌────┴────┐
  ▼         ▼
┌─────┐  ┌─────┐
│Sales│  │ Tech│
│Agent│  │ Supp│
└──┬──┘  └──┬──┘
   │        │ handoff
   │        ▼
   │     ┌──────┐
   │     │Refund│
   │     │Agent │
   │     └──┬───┘
   │        │
   └───┬────┘
       ▼
   Response
```

### Team Chat (AutoGen)

```
┌──────── RoundRobinGroupChat ────────┐
│                                      │
│  Agent A ──msg──> Agent B            │
│     ▲                │               │
│     │             msg│               │
│     │                ▼               │
│  Agent D <──msg── Agent C            │
│                                      │
│  Termination: MaxMessages(10)        │
│  or TextMention("TERMINATE")         │
└──────────────────────────────────────┘
```

---

## Request / Sequence Flow Templates

### Tool Call with Auth + RBAC

```
Client       Auth MW     RBAC MW     Handler      Ext API
  │            │           │           │             │
  │──request──>│           │           │             │
  │            │──verify──>│           │             │
  │            │ token     │           │             │
  │            │           │──check────>│             │
  │            │           │ permission │             │
  │            │           │           │──API call──>│
  │            │           │           │<─response───│
  │<───result──│           │           │             │
```

### Config-Driven Tool Registration

```
YAML Config
    │
    ▼
ConfigLoader.load_config()
    │
    ▼
Pydantic Validation
    │
    ├── ServerConfig[]
    ├── AgentConfig[]
    ├── ToolAssignment[]
    └── ClientConfig[]
          │
          ▼
    ServerManager
          │
          ├── create_server("internal", port=9001, path="/internal")
          ├── create_server("external", port=9001, path="/external")
          │
          └── for each tool_assignment:
                ├── find matching agent config
                ├── check client access rules
                ├── check RBAC permissions
                └── register tool on target server(s)
```

### RAG Pipeline Flow

```
    ┌───── Ingestion (offline) ─────┐
    │                                │
    │ Docs ──> Chunker ──> Embedder │
    │                        │       │
    │                        ▼       │
    │                  Vector Store  │
    └────────────────────────────────┘

    ┌───── Query (online) ──────────┐
    │                                │
    │ Query ──> Embedder             │
    │              │                 │
    │              ▼                 │
    │        Similarity Search       │
    │              │                 │
    │              ▼                 │
    │         Reranker (optional)    │
    │              │                 │
    │              ▼                 │
    │     Context + Query ──> LLM   │
    │                          │    │
    │                          ▼    │
    │                       Answer  │
    └────────────────────────────────┘
```

### A2A Task Lifecycle

```
Client                          A2A Server
  │                                │
  │──GET /.well-known/agent.json──>│
  │<──Agent Card (capabilities)────│
  │                                │
  │──POST /tasks (create)─────────>│
  │<──{id, status: "submitted"}────│
  │                                │
  │──GET /tasks/{id} (poll)───────>│
  │<──{status: "working"}──────────│
  │                                │
  │──GET /tasks/{id} (poll)───────>│
  │<──{status: "completed",        │
  │    artifacts: [...]}───────────│
```

---

## Data / State Diagrams

### State Machine

```
     ┌──────────┐
     │  IDLE    │
     └────┬─────┘
          │ start
          ▼
     ┌──────────┐
  ┌─>│ RUNNING  │──error──> FAILED
  │  └────┬─────┘
  │       │ complete
  │       ▼
  │  ┌──────────┐
  │  │ REVIEW   │──reject──┘
  │  └────┬─────┘
  │       │ approve
  │       ▼
  │  ┌──────────┐
  └──│COMPLETED │
     └──────────┘
```

### Middleware Chain (execution order)

```
Request ──> [1. Request Smuggling Protection]
                      │
                      ▼
            [2. Authentication]
                      │
                      ▼
            [3. RBAC / Authorization]
                      │
                      ▼
            [4. Input Validation]
                      │
                      ▼
            [5. Rate Limiting]
                      │
                      ▼
            [6. Tool Composition]
                      │
                      ▼
              Tool Handler
                      │
                      ▼
Response <── [reverse order for response transforms]
```

### Configuration Hierarchy

```
┌─────────────────────────────────────┐
│  Runtime Overrides (CLI args)       │  highest priority
├─────────────────────────────────────┤
│  Environment Variables (.env)       │
├─────────────────────────────────────┤
│  Config Files (YAML / JSON)        │
├─────────────────────────────────────┤
│  Code Defaults (Pydantic defaults) │  lowest priority
└─────────────────────────────────────┘
```

---

## Security / Access Control Diagrams

### Token Authentication Flow

```
Request + Token
       │
       ▼
  ┌──────────┐
  │  Cache   │──hit──> Token Valid ──> Continue
  └────┬─────┘
       │ miss
       ▼
  ┌──────────┐
  │Auth Service│──valid──> Cache + Continue
  └────┬─────┘
       │ invalid
       ▼
   401 Unauthorized
```

### RBAC Decision Flow

```
Tool Call Request
       │
       ├── extract resource_type
       ├── extract tool_name
       │
       ▼
  ┌──────────────────┐
  │ RBAC Service     │
  │                  │
  │ resource_type ──>│──> lookup allowed tools
  │                  │        │
  │ tool_name ──────>│──> check membership
  └──────────┬───────┘
             │
        ┌────┴────┐
      allowed   denied
        │         │
        ▼         ▼
     Execute    Error
      Tool    "Not authorized"
```

### Access Control Matrix

```
              Tool A    Tool B    Tool C
            ┌─────────┬─────────┬─────────┐
Internal    │   YES   │   YES   │   YES   │
Client      │         │         │         │
            ├─────────┼─────────┼─────────┤
External    │   YES   │   NO    │   NO    │
Client      │         │         │         │
            ├─────────┼─────────┼─────────┤
Admin       │   YES   │   YES   │   YES   │
Client      │         │         │         │
            └─────────┴─────────┴─────────┘
```

---

## Deployment Diagrams

### Kubernetes with Ingress

```
Internet
    │
    ▼
┌──────────┐
│  Ingress │  /mcpserver/*
└────┬─────┘
     │
     ▼
┌──────────────────────────┐
│  Service (port 9001)     │
└────┬─────────────────────┘
     │
     ▼
┌──────────────────────────┐
│  Pod                     │
│  ┌────────────────────┐  │
│  │  App Container     │  │
│  │  /internal/mcp     │  │
│  │  /external/mcp     │  │
│  │  /health           │  │
│  └────────────────────┘  │
└──────────────────────────┘
```

### Multi-Environment Config

```
┌────────────────────────────────────────┐
│          app-resources/                │
│                                        │
│  dev.env ──> local development         │
│  ci.env  ──> CI/CD pipeline            │
│  qaint.env ──> QA/integration testing  │
│  prod.env ──> production               │
│                                        │
│  Selected by: DEPLOY_ENV env var       │
└────────────────────────────────────────┘
```

---

## Class / Entity Relationship Templates

### Handler Inheritance

```
┌──────────────────┐
│  BaseHandler     │
├──────────────────┤
│+ config          │
│+ template_render │
├──────────────────┤
│+ create_tool()   │
│+ validate()      │
└────────▲─────────┘
         │ extends
    ┌────┴─────┐
    │          │
┌───┴────┐ ┌──┴──────┐
│HTTP API│ │Custom   │
│Handler │ │Handler  │
└────────┘ └─────────┘
```

### Config Schema Relationships

```
CompositionConfig
  │
  ├── servers: [ServerConfig]
  │     └── access_control: AccessControl
  │
  ├── agents: [AgentConfig]
  │     ├── input_schema: Dict (JSON Schema)
  │     └── request_body_template: Dict
  │
  ├── tool_assignments: [ToolAssignment]
  │     ├── target_servers: [str]
  │     ├── resource_type_allowed: [str]
  │     └── composition_rules: {server: CompositionRule}
  │
  └── clients: [ClientConfig]
        └── allowed_servers: [str]
```

---

## Tips for Clean Diagrams

1. **Alignment**: Keep boxes and text aligned on a grid
2. **Spacing**: Consistent spacing between elements (2+ chars)
3. **Labels**: Label all arrows with what flows across them
4. **Flow direction**: Top-to-bottom or left-to-right, never mix
5. **Grouping**: Use box borders to group related components
6. **Simplicity**: One concept per diagram; break up complex diagrams
7. **Context**: Add a one-line description above or below
