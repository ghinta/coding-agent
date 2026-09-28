# Anatomy of an AI Agent

*From chatbot to digital worker: what defines an agent, its four pillars (LLM, system prompt, memory, tools), the agentic loop, and when (not) to use agents.*

## 1. Introduction: The Rise of Acting AI

- Generative AI boom democratized access to AI; underlying paradigm shift → **Software 3.0**
  - **Software 1.0:** rigid rules
  - **Software 2.0:** machine learning, learning from data
  - **Software 3.0:** natural language as programming medium
- Shift from pure chatbots to AI agents
  - Agent = software entity that uses language as instruction for autonomous action, not just as conversation
- **Definition – AI agent:** system that perceives its environment, makes decisions independently, and takes targeted actions to reach a given goal
- Key moment: AI completes tasks for us instead of only generating text

## 2. The Key Difference: Static Workflows vs. Dynamic Agents

- Example: planning a trip to Montreal
  - **Classic workflow (Software 1.0/2.0):** all steps hardcoded in advance (find activity A → check date B → if free, book C); breaks if a variable changes or a website is unreachable
  - **AI agent:** only receives the goal ("Plan and book a three-day cultural program in Montreal"); decides path at runtime

| Criterion | Classic Workflows | AI Agents |
|---|---|---|
| Path definition | Predefined, rigid (coded graphs) | Dynamic, created at runtime |
| Flexibility | Low; fails on deviations | High; reacts to the unexpected |
| Control | Full control with the programmer | Control lies with the model (LLM) |
| Logic | If-then logic (rule-based) | Language-based instruction (Software 3.0) |

- **Takeaway:** agents solve problems where the solution path isn't known in advance or the environment keeps changing

## 3. The Four Pillars of an AI Agent

### 3.1 The Brain: Large Language Model (LLM)

- LLM (e.g. Claude 3.5, GPT-4) = planning center; decomposes complex tasks into sub-steps
- **Reasoning capability:** not every model can reason logically; high reasoning capability essential for agents
- **Context window:** how much information the agent can actively process at once
- **Cost of iteration:** agents work in loops → significantly more tokens (and cost) than a single chat request

### 3.2 The Purpose: Identity and System Prompt

- System prompt defines persona and instructions (like a job brief for a new intern)
  - e.g. "eloquent financial advisor" vs. "hip teen advisor"
- Without it, the agent lacks orientation for its decisions

### 3.3 Memory

- LLMs are inherently **stateless** → need an artificial memory layer
- **Intrinsic memory:** knowledge from training (like a university degree) – firmly anchored but static after training
- **Short-term memory:** context window (like notes on the desk) – instantly available but limited space
- **Long-term memory:** external databases (RAG) (like a filing cabinet in the basement) – persisted across sessions, retrieved on demand

### 3.4 Tools: Capabilities Beyond Text

- Tools let AI overcome knowledge limits (cut-off dates)
- Via function calls / tool calling: calculators, weather data, calendars
- **Model Context Protocol (MCP):** established industry standard unifying interfaces between agents, tools, and data sources ("USB port for AI capabilities")

## 4. The Agentic Loop: Plan – Act – Observe

- Agent works in an iterative cycle, not linearly:
  1. **Plan:** LLM decomposes the task ("first check the calendar")
  2. **Act:** agent executes an action (e.g. tool call)
  3. **Observe:** perceives the result ("slot taken") and re-evaluates
- Two patterns in complex (multi-agent) systems:
  - **Hierarchical (supervisor):** main agent delegates tasks to specialized sub-agents; sub-agents don't communicate with each other
  - **Swarm:** decentralized network; agents hand off tasks to each other (more efficient, harder to control)

## 5. Reality Check: Opportunities and Limits

- **Moravec's paradox:** tasks hard for humans (complex logic, chess, data analysis) are easy for AI; tasks trivial for humans (walking, jumping, empathy) are huge challenges for AI
- Risks:
  - **Replit incident:** agent accidentally deleted production data, later claimed it had "panicked" → systems mirror human language without real emotions or sense of responsibility
  - **Air Canada ruling:** companies are liable for (mis)information given by their digital agents

### Decision Guide

| When an Agent Is the Best Choice | When to Stay with Workflows |
|---|---|
| ✅ Open solution path / high flexibility | ❌ Mission-critical |
| ✅ Errors are tolerable (draft phase) | ❌ Strictly regulated environment |
| ✅ Human oversight is available | ❌ Absolute predictability required |

## 6. Conclusion: The Human as Conductor of AI

- Human role shifts from executor to architect and conductor
- Need to become a **polymath**: understand the big picture, steer AI assistants precisely
- Treat AI as "junior assistant":
  - AI: syntax and tedious implementation
  - Human: problem definition, solution design, final ethical control

### Key Takeaways for Beginners

- **Start safe:** give agents read-only access first; add human-in-the-loop (HITL) approval for critical steps
- **Use standards:** prefer protocols like MCP when choosing tools → interoperability
- **Stay a generalist:** deepen domain knowledge; only domain experts can judge whether the agent is "panicking" or delivering a brilliant solution
