# AI Skills

A collection of agent skills for AI and agentic codebases. Skills are reusable capabilities that extend what your AI coding agent can do.

Skills follow the [Agent Skills](https://agentskills.io/) format.

## Available Skills

### explore-ai-codebase

Systematically explore and explain any modern AI or agentic codebase with ASCII diagrams. Supports 9+ framework types with a 4-phase methodology.

**Use when:**

- "Explain this codebase"
- "How does this agent system work?"
- "Explore the architecture and draw diagrams"
- "Trace a request through the system end-to-end"
- Working with any AI/agentic project

**Supported codebase types:**

- MCP Servers (FastMCP, `@modelcontextprotocol/sdk`)
- LangGraph Agents (`StateGraph`, nodes, edges, checkpointers)
- CrewAI Systems (Agents, Tasks, Crews, YAML roles)
- AutoGen Multi-Agent (`GroupChat`, `AssistantAgent`, teams)
- OpenAI Agents SDK (`Agent`, `Handoff`, `Guardrail`)
- RAG Pipelines (ChromaDB, Pinecone, Qdrant, LlamaIndex)
- A2A Protocol (Agent Cards, task lifecycle)
- Config-Driven Frameworks (YAML tools, middleware chains)
- Hybrid / Mixed Systems

## Installation

```shell
npx skills add prakx1/ai-skills
```

## Usage

Skills are automatically available once installed. Your agent will use them when relevant tasks are detected.

**Examples:**

```
Explain this codebase
```

```
How does the agent architecture work?
```

```
What tools does this MCP server expose?
```

## Skill Structure

Each skill contains:

- `SKILL.md` — Core instructions for the agent
- Supporting reference files (framework patterns, diagram templates, etc.)

## License

MIT
