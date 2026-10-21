# Coding Agent

**Project website / GitHub Page:** [Coding Agent — Architecture Overview](https://ghinta.github.io/coding-agent/)

Architecture evaluation for an **agentic coding platform at a Company** with maximum data sovereignty: agents work on confidential code and internal systems, while LLM inference is consumed as an external service (no own GPUs, no own model hosting).

## Prompt-to-Result Workflow

**The prompt starts with the developer, not the provider.** The company agent runtime promptly calls the external LLM through the approved gateway. The LLM plans, requests tools or proposes code; the on-prem runtime validates and executes permitted actions through MCP and an isolated sandbox. Relevant results can be sent back to the model for the next decision, repeating until verification or a stop condition.

```mermaid
sequenceDiagram
    autonumber
    actor Dev as Developer (VS Code)
    participant RT as Company Agent Runtime
    participant GW as Company LLM Gateway
    participant LLM as External LLM
    participant MCP as Company MCP + Policy
    participant SYS as Knowledge Base / GitLab / CI
    participant SB as Company Sandbox

    Dev->>RT: Prompt - investigate and fix failing tests
    opt Authorized local prefetch
        RT->>MCP: Retrieve limited metadata
        MCP->>SYS: Scoped search
        SYS-->>MCP: Matching context
        MCP-->>RT: Filtered results
    end
    loop Until verified or stopped by policy / budget
        RT->>GW: Goal + selected context / observations
        GW->>LLM: Approved minimized inference request
        LLM-->>GW: Plan, tool request or code proposal
        GW-->>RT: Model response
        alt Need internal evidence
            RT->>MCP: Typed, authorized tool call
            MCP->>SYS: Search docs / inspect repo / query CI
            SYS-->>MCP: Tool result
            MCP-->>RT: Filtered evidence
        else Code change or verification
            RT->>SB: Run permitted edit / tests
            SB-->>RT: Diff, logs and test evidence
        else Enough evidence
            RT->>RT: Validate completion criteria
        end
    end
    opt Sensitive write action
        RT-->>Dev: Request approval
        Dev-->>RT: Approve or reject
    end
    RT-->>Dev: Verified result, diff and test report
```

**Data boundary:** source repositories, RAG indexes, persistent memory, traces, tool credentials and execution remain company-controlled. **Selected code snippets, tool results and prompts may leave the company during inference**; minimize/redact them and secure provider contracts. The external LLM never connects directly to internal systems.

**Reading guide:** This sequence shows **execution order**. C4 and deployment diagrams below show **placement and interfaces**, not a one-way processing pipeline.

## System Overview (static component relationships)

This structural view is **not execution order**: the agent calls the LLM repeatedly while using local tools between model decisions.

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
    platform <-->|Inference requests and model replies| llm
    platform <-->|Authorized MCP tools and results| gitlab
    platform <-->|Authorized MCP tools and results| internal

    classDef person fill:#08427b,stroke:#052e56,color:#fff
    classDef system fill:#1168bd,stroke:#0b4884,color:#fff
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

## Remote workspace architecture (under evaluation)

Workspace patterns A/B/C are **independent of inference deployment choices**. All share the same prompt-to-result loop; only the workspace and sandbox deployment changes.

| Variant | Developer workspace | Agent security |
|---|---|---|
| **A – Integrated** | OpenShell workspaces | OpenShell |
| **B – Modular** | **Remote Development Platform** (Coder / OpenShift Dev Spaces / Eclipse Che) | Optional OpenShell sandbox |
| **C – Kubernetes-native** | **Remote Development Platform** (same candidates) | Restricted Kubernetes workloads, optionally Kata Containers |

**Independent layers:** workspace lifecycle, agent orchestration, sandbox/security, environment definitions (Dev Containers / Devfiles / optional Devbox), Knowledge Base/MCP, and inference routing. See the [full evaluation plan](remote-development-environments.md).

## Project Structure

```text
coding-agent/
├── README.md
├── agentic-workflows.md
├── remote-inference-agent-on-prem.md
├── remote-development-environments.md
├── cloud-agent-cloud-llm
└── sources/
    └── README.md
```

## Documents

| Document | Content |
|---|---|
| [Agentic Workflows](agentic-workflows.md) | Workflows that benefit from agentic architecture, including state, branching, parallelism and human-in-the-loop. |
| [Remote Inference – Agent On-Prem](remote-inference-agent-on-prem.md) | Agent runtime on-premises with external LLM inference. |
| [Cloud Agent + Cloud LLM](cloud-agent-cloud-llm) | Agent runtime and LLM hosted externally, with controlled access to internal systems. |
| [Remote Development Environments (evaluation)](remote-development-environments.md) | Three patterns: integrated OpenShell; Coder / OpenShift Dev Spaces / Eclipse Che with optional OpenShell; or Kubernetes-native agent isolation. Devbox remains optional. |

## Sources

The [`sources/`](sources/) folder contains supporting documents, vendor material, research notes and other source material used during the architecture evaluation.

## Status

Architecture evaluation – no implementation yet. Current focus: **Variant A – Remote Inference with the agent runtime on-premises**.

**Remote development workspaces are a proposed extension, not an implementation.** The **Remote Development Platform** category includes Coder, Red Hat OpenShift Dev Spaces and Eclipse Che (Dev Spaces is based on Che). Evaluate three deployment patterns: **A: OpenShell integrated**, **B: Remote Development Platform + OpenShell**, and **C: Remote Development Platform + Kubernetes-native agent isolation**. OpenShell and Devbox are optional; no product has been chosen. Third-party PaaS-hosted devcontainers remain out of scope.
