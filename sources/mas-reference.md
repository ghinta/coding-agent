# Multi-Agent Systems (MAS) Reference

*Architectural reference sheet on designing and implementing multi-agent systems: agent anatomy, workflows vs. agents, supervisor vs. swarm, MCP, evaluation, and operating rules.*

## 1. Evolution of Software Architecture: From Serial Rules to Parallel Agentics

- Paradigm shift redefining the architect's role:
  - **Software 1.0:** explicit, rule-based logic
  - **Software 2.0:** classic ML; models learn rules from data
  - **Software 3.0:** "natural language programming"; LLMs act as central control units, not just text generators
- Enabler: shift from serial sequence models (RNNs, LSTMs) to parallelizable Transformer architecture (landmark 2017 paper)
  - Parallel training/inference → scaling to trillions of parameters → "emergent reasoning"
- Architects no longer design rigid process steps; they design systems that determine their own execution paths probabilistically at runtime
- Requires strict definition of core agentic components to contain the variability of token-to-token generation

## 2. The Four Pillars of Agent Anatomy: Modularity as Guardrail

- An AI agent is a compound system, not a monolithic LLM call
- Modular decomposition = only defense against technical debt and poor scalability

### Core Components

- **Purpose (system prompt):** identity, persona, task
  - In production: complex instruction set constraining the agent's solution space, not a single sentence
- **Reasoning & planning (the brain):** LLM decomposes complex user requests into atomic sub-steps
  - Model choice (e.g., Claude 4.5 or GPT-4o, as of January 2026) = architectural trade-off: reasoning depth vs. latency
- **Tools & actions (capabilities):** APIs, Python functions, browser actions
  - Overcome the model's knowledge cutoff and statelessness
- **Memory:** state management

| Memory type | Description | Architectural challenge |
|---|---|---|
| Intrinsic memory | Model parameters | Fixed knowledge (training/fine-tuning) |
| Short-term memory | Context window | Context management: compressing or dropping irrelevant info |
| Long-term memory | External storage/RAG | Persistence across sessions via vector databases or SQL |

### Analysis: Context Window Management as "Art and Science"

- Short-term memory ≠ just appending chat history
- Architects decide what to summarize or remove
- Clogging the context window with too many tools or redundant instructions → performance degradation, loss of the thread
- **Takeaway:** efficient context control decides agent success or failure

## 3. Deterministic Workflows vs. Dynamic Agentic Systems

- Trade-off: control vs. flexibility

| Attribute | Workflows (static graphs) | Agents (dynamic control flow) |
|---|---|---|
| Control flow | Predefined, hard-coded | Determined at runtime by the LLM |
| Predictability | High (deterministic) | Variable (probabilistic) |
| Fault tolerance | Minimal required | High (via iterative loops) |
| Latency | Low & predictable | Higher due to multiple LLM calls |

- **Mission-critical processes** (financial transactions, medical dosing) → model as workflows; human control over every path mandatory
- **Exploratory tasks** (R&D analysis, complex multi-interface travel booking) where the solution path is hard to pre-code → benefit from agentic autonomy

## 4. Hierarchical vs. Decentralized Architectures: Supervisor vs. Swarm

- Single agents hit limits as complexity grows; two dominant orchestration patterns

### Technical Comparison (Metrics-Based)

- **Supervisor pattern (hierarchical):** central supervisor delegates to specialized sub-agents ("managerial middleman")
  - Performance: ~16 hops on complex tasks
  - Token usage: ~8,000 input tokens due to overhead (back-and-forth between supervisor and specialist)
  - Advantage: excellent debugging control; supervisor validates intermediate results
- **Swarm pattern (decentralized):** agents hand off tasks directly to each other (handoffs)
  - Performance: reduced to ~8 hops
  - Token usage: reduced to ~5,000 input tokens (no central-instance overhead)
  - Advantage: highest efficiency for agile, fast handoffs
- **Takeaway:** supervisor for logic-heavy tasks needing validation; swarm for process-optimized flows to minimize cost and latency

## 5. Interoperability via Standardization: Model Context Protocol (MCP)

- As of January 2026, "interface hell" = biggest obstacle for enterprise MAS
- Solution: standardize communication between agents, tools, and data sources
- **MCP:** standard initiated by Anthropic, supported by OpenAI and the Linux Foundation; "USB/HDMI port" for agentics
- **Strategic advantage:** true plug-and-play
  - Tool built for one system immediately usable by other agents
  - Greatly reduces vendor lock-in
  - Faster development through interoperability & reuse

## 6. Evaluation and Quality Assurance: Validating Probabilistic Systems

- Three-level framework:
  1. **LLM level:** instruction adherence, hallucination rate, toxicity
  2. **System level:** correct tool selection, efficiency of task decomposition
  3. **Application level:** classic IT metrics (latency, error rate, cost per task, UX)
- **Golden rule:**
  - Use code-based evals (unit tests for agents) whenever ground truth or a deterministic result exists → cheaper, more consistent
  - Use LLM-as-a-judge only for qualitative, open-ended nuances without binary ground truth

## 7. Strategic Conclusion: The "Junior Assistant" Approach

- AI agents (as of early 2026) = capable but fallible "junior assistants", not autonomous experts
- Based on greedy token generation; no real world model or understanding of physics and logic

### Golden Rules for Systems Engineers

1. **Read-only first:** tool access starts read-only; write access to production systems = final maturity stage
2. **Human-in-the-loop (HITL):** mandatory for all non-read-only actions; prevents catastrophic compounding errors
3. **Comprehensive tracing & logging:** log every decision chain transparently for forensic analysis of incidents such as the "Replit incident" (agent deletes production) or wrong chatbot answers (Air Canada ruling)
4. **Avoid infinite loops:** hard termination conditions (max hops) for agentic loops

- **Takeaway:** agents amplify architects but don't replace systems thinking; mastery of databases, identity management, and networking fundamentals enables successful orchestration; blind reliance on probabilistic generation builds unstable houses of cards
