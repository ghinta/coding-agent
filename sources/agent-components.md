# AI Agent Components

*Handbook of the five core components of an autonomous AI agent: brain (LLM), orchestrator, tools, memory, and supervisor.*

## Introduction: From LLMs to Agentic AI

- Transition from pure LLMs to agentic AI = paradigm shift
  - Traditional LLM apps: sophisticated text generators
  - AI agent: autonomous actor; pursues higher-level goals via proactive planning and external tools
- Key to scaling business processes: minimal human guidance; agents structure and execute complex workflows themselves → large efficiency gains

### Comparison: Architectural Approaches

| Criterion | Traditional LLM apps | AI agent systems |
|---|---|---|
| Goal specification | Step-by-step instructions (prompts) | High-level goals (e.g., "maximize ROI") |
| Capability to act | Static text output | Active use of APIs and tools |
| Human guidance | Continuous supervision required | Autonomous decision-making; minimal input |

## 1. The Brain (LLM): Center for Reasoning and Planning

- Cognitive backbone of the agent; responsible for:
  - **Reasoning:** logical inference
  - **Planning:** strategic decomposition of goals
- **Knowledge cutoff:** even models like GPT-4 only have knowledge up to January 1, 2024
  - Without further components → cannot handle current events or specific internal data
- Three core tasks in the agentic context:
  - **Goal interpretation:** turn vague user requests into precise technical requirements
  - **Task decomposition:** create a sequential or parallel plan to reach the goal
  - **Tool-use decisions:** judge when internal knowledge ends and external tools must be used
- **Takeaway:** a brain without coordination only produces isolated thoughts → needs an orchestrator

## 2. The Orchestrator (Framework): The System's Connective Tissue

- Framework connecting the LLM's cognitive abilities to the outside world
- Manages logic loops, task sequencing, conditional routing
- LLM "thinks"; orchestrator routes results to the right place in the system flow
- Framework choice shapes agent architecture:
  - **LangGraph:** state machine approach; precise control over cycles and complex decision trees
  - **AutoGen:** actor model; focus on communication between multiple specialized agents acting as autonomous actors
  - **CrewAI:** role-based collaboration; agents get fixed roles and hierarchies within a team
  - **n8n:** visual, node-based workflow automation orchestrator; ideal for no-code integrations

### Aside: Data Integrity via Pydantic

- Unpredictable LLM responses = risk in production
- Pydantic: Python data validation library; forces unstructured LLM output into a strictly defined schema (type hints)
- **Takeaway:** turns "probable text" into "reliable data structures"

## 3. Tools: The Agent's Hands

- External interfaces (APIs) and functions that lift LLM limitations, especially the knowledge cutoff
- Via tool calling, the agent decides autonomously when to use an external resource to validate an observation or perform an action
- Categories:
  - **Search tools:** e.g., Tavily for real-time internet search (data after January 1, 2024)
  - **Analysis tools:** code interpreters run Python scripts for math problems or visualizations
  - **Productivity tools:** Google Calendar, Slack, CRM integrations for direct interaction with business processes
- Tools provide valuable but stateless data → agent needs memory to track progress over time

## 4. Memory: Context and Continuity

- Enables context awareness; without it, the agent forgets prior findings each iteration
- Two strictly separated levels:
  1. **Short-term memory (state):** current status of the running process (e.g., which plan sub-tasks are done)
  2. **Long-term memory (persistence):** information persisted across sessions, often via vector databases or checkpoints
- Process-control metadata often stored in structured formats:

```json
{
  "agent_status": "in_progress",
  "current_task": "Analyze market data",
  "knowledge_state": {
    "cutoff_surpassed": true,
    "last_tool_output": "Tavily: oil price currently at 82.40 USD"
  },
  "history": ["Goal defined", "Web search executed"]
}
```

## 5. The Supervisor (Human-in-the-Loop & Guardrails)

- Regulatory instance ensuring quality, budget adherence, and safety
- **Guardrails:** define ethical and operational boundaries
- **HITL:** human gives final approval on critical decisions

### Escalation Policy: When Human Intervention Is Required

| Scenario | Reason for HITL intervention |
|---|---|
| Financial transactions | Prevent uncontrolled budget overruns (e.g., ad spend) |
| Legal contracts | Liability safety when drafting offers or employment contracts |
| Logical conflicts | Resolve ambiguity from contradictory tool results |

## Conclusion: Synergy of the Five

- Agent = dynamic system operating in the ReAct cycle (reasoning and acting)
  - Observation (tool result or environment) drives the next thought of the brain
  - This feedback loop enables true autonomy
- Shift from building static chat interfaces to designing robust architectures for digital autonomy
- **Takeaway:** an AI agent is a coherent system that autonomously achieves complex goals through reasoning (brain), orchestration (structure), tools (hands), memory (continuity), and supervision (safety)
