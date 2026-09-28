# Multi-Agent System for Automated Code Reviews

*Architecture of a selective, multi-agent code review system (fan-out/fan-in, 3-layer memory, Postgres-consolidated infra, HITL) that frees senior-engineer attention for high-value review work.*

## 1. Introduction and Strategic Context

- Bottleneck in scaled engineering orgs: limited senior-developer capacity
- PR throughput rises; human review quality varies due to review fatigue and cognitive inconsistency (20th review of the day ≠ precision of the 1st)
- Positioning: not a replacement for human expertise but an **"Attention Reclaiming Engine"**
  - Automate the mechanical, repetitive part of reviews → reclaim senior attention for complex architectural questions
- Vision: maximize business value by shifting L0 checks to a high-performance agentic system
- Requires more than simple heuristics: real repository context understanding + agentic reasoning

## 2. Core Philosophy: Selectivity over Coverage

- Principle: **"Selectivity as the Seed"**; strict L0 principle
  - Optimize for findings truly worth a senior's attention
- **"Selectivity over Coverage"** = non-negotiable architectural invariant
- When in doubt: fail silently or escalate internally rather than burden developers with trivial noise

| Aspect | Traditional Review Automation (Linters/Static Analysis) | Agentic Selective Review (Multi-Agent System) |
|---|---|---|
| Precision | High for syntax, low for semantic context | Excellent via reasoning and multidimensional grounding |
| Context understanding | Near zero (local pattern matches) | Deep understanding of whole codebase, ADRs, and history |
| Noise suppression | Low (high false-positive rate on style) | High via focus on high-confidence/high-value findings |
| Auditability | Binary (pass/fail) | Transparent via rationale and mandatory evidence |

- Only high-confidence findings communicated → eliminates tool fatigue; focus on critical logic and security defects
- Greatly increases developer acceptance; system perceived as competent "virtual colleague"
- Selectivity implemented via strict separation of specialized agent personas

## 3. Multi-Agent Architecture: Fan-Out/Fan-In Pattern

- Complex reviews exceed reasoning capacity of a single monolithic LLM prompt
- Fan-out/fan-in pattern enforces clear separation of concerns

### Specialized Agent Personas

- Each agent: specific mindset + strict evidence requirement (line numbers + rationale)
- **Security Agent**
  - Mindset: maximum skepticism; doesn't assume the diff is correct
  - Actively hunts exploits (e.g. SQL injection, insecure data handling)
- **Quality Agent**
  - Mindset: guardian of architectural patterns
  - Checks changes against established design patterns and team conventions
- **Testing Agent**
  - Mindset: edge-case identification
  - Looks for test gaps, flaky assertions, missing coverage of critical paths
- **Documentation Agent**
  - Mindset: consistency keeper
  - Ensures docs, comments, and API descriptions stay in sync with the diff

### Synthesis Logic and Independent Verifier

- After parallel fan-out, **aggregator**: merges findings, removes duplicates, computes overall confidence
- **Independent Verifier:** separate agent checks aggregator output against original invariants before anything leaves the system
  - Prevents hallucinations or misattributions in the final review report
- Agent intelligence limited by access to global repository context

## 4. Three-Dimensional Memory Architecture (Grounding)

- Deep grounding to act as virtual colleague and prevent hallucinations:
  1. **Semantic memory (codebase):** vector embeddings identify relevant code slices; shows how changes in module A affect distant dependencies in module B
  2. **Episodic memory (history):** past reviews and corrections ("what we did"); learns from earlier mistakes, prevents recurrence of already-discussed defects
  3. **Procedural memory (conventions):** team rules and Architecture Decision Records (ADRs); defines how the team wants things done; bridges generic code and local best practice
- Drastically narrows the "stranger-to-colleague" gap
- Requires high-performance, consolidated infrastructure

## 5. Data Modeling and Infrastructure (Tiger Cloud/Postgres)

- Instead of three separate stores for memory (vectors), truth (review findings), and time (observability) → consolidate on **Tiger Cloud (Postgres)**
  - Less maintenance complexity; single source of truth
- Core components:
  - **pgvector, pgvector_scale & DiskANN:** scalable semantic search over millions of code chunks; DiskANN indexes on SSD → much lower RAM cost, supports enterprise-scale repos
  - **Hypertables (TimescaleDB):** form the "event spine"; logs every action precisely and persistently in time
  - **Continuous Aggregates:** real-time monitoring + real-time budget enforcement; block further LLM calls immediately when a PR token limit or daily budget is exceeded
- Single-store strategy: lower latency; metadata and review history always consistent with vector index

## 6. Production Readiness & Reliability Engineering

- Production AI systems must be robust against infrastructure latency and model instability → defensive agent architecture

### Stateless Ingress & Worker Pool

- Meets GitHub's 10-second acknowledgement limit by separating receipt from processing
  - **Stateless ingress:** validates signatures, acknowledges immediately
  - **Worker pool:** Redis-based ARQ workers process long-running LLM tasks asynchronously

### Framework Agnosticism

- MVP built on LangGraph, but behind an **Abstract Workflow Interface** (`Run` / `Resume` / `GetState`)
  - Enables switching to Temporal for complex enterprise orchestration without changing agent business logic
- Idempotency keys and circuit breakers protect against LLM-provider API outages

## 7. Human-in-the-Loop (HITL) and Escalation Paths

- **Automatic posting:** only if confidence > threshold and no critical security findings
- **Approval queue:** escalation to a senior on low confidence or critical findings; senior acts as teacher of the system
- **Feedback loop:** every human correction flows back into episodic memory; Independent Verifier ensures only quality feedback contributes to system evolution

## 8. Observability: Event Spine and Economics

- Economic transparency essential for scaling LLM systems; every action (retrieval → prompt → decision) captured in event spine
  1. **Traceability:** reconstruct every decision for audits and dispute resolution
  2. **Cost monitoring & circuit breaking:** token economics per PR; Continuous Aggregates halt system immediately on budget overrun
  3. **Performance tuning:** find latency bottlenecks in the chain of agent calls

## Conclusion

- Provides integrity and robustness for enterprise use
- Consolidation on Tiger Cloud + strict selectivity + framework-agnostic orchestration → system that scales senior capacity, not just checks code
- Foundation for evolving from MVP to autonomous engineering infrastructure
