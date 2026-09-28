# Coding Agent

Architecture evaluation for an **agentic coding platform at a Company** with maximum data sovereignty: agents work on confidential code and internal systems, while LLM inference is consumed as an external service (no own GPUs, no own model hosting).

## Overview

```mermaid
flowchart TB
    dev(("👤<br/>Developer"))
    llm[["Inference Provider<br/>[Mistral, Azure, Koyeb]"]]

    subgraph company ["Company On-Prem"]
        platform("Agent Platform")
        gitlab[["GitLab"]]
        internal[["Internal Systems<br/>[OpenShift, DBs, APIs]"]]
    end

    dev -->|Tasks| platform
    platform -->|Prompts| llm
    platform -->|MCP| gitlab
    platform -->|MCP| internal

    classDef person fill:#08427b,stroke:#052e56,color:#fff
    classDef system fill:#1168bd,stroke:#0b4884,color:#fff
    classDef container fill:#438dd5,stroke:#2e6295,color:#fff
    classDef component fill:#85bbf0,stroke:#5d82a8,color:#000
    classDef ext fill:#8a8a8a,stroke:#6b6b6b,color:#fff
    class dev person
    class platform system
    class llm,gitlab,internal ext
    style company fill:none,stroke:#888,stroke-dasharray:5 5
```

The central question is **where the agent runtime lives** – this defines the trust boundary:

| | Variant A – Remote Inference | Variant B – Cloud Agent Runtime |
|---|---|---|
| Agent, memory, state | On-prem | Cloud |
| Tools | On-prem | On-prem, via MCP Gateway |
| LLM | External | External |
| Crosses the boundary | Minimized prompts only | Prompts, agent state, tool results |
| Security boundary | LLM Gateway (egress) | MCP Gateway (ingress) |
| Data sovereignty | Maximum | Reduced |

## Documents

### Architecture

| Document | Content |
|---|---|
| [Variant A – Remote Inference](infrastructure/variant_a_remote_inference.md) | Agent on-prem, inference external. C4 views (context, container, component, deployment), principles, data classes. |
| [Variant B – Cloud Agent Runtime](infrastructure/variant_b_cloud_agent_runtime.md) | Agent + LLM in the cloud, internal systems via MCP Gateway. |

### Inference Providers

| Document | Content |
|---|---|
| [Mistral Enterprise](infrastructure/mistral_enterprise_confidential_coding_agent.md) | Contracts, zero data retention, GDPR/DPA, residency, recommended Company architecture. |
| [Azure AI Foundry](infrastructure/providers/azure_ai_foundry_confidential_coding_agent.md) | Data protection position, network setup, contract stack, review checklist. |

### Workflows

| Document | Content |
|---|---|
| [Agentic Workflows with LangGraph](workflows/summary.md) | When agentic workflows pay off; state, branching, parallelism, HITL. |

### Sources

[.sources/](.sources/) – background material (German) on agent anatomy, multi-agent patterns, LangGraph and production deployment. Input only, not curated.

## Status

Architecture evaluation – no implementation yet. Current focus: **Variant A**.
