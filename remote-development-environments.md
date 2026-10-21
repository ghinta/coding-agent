# Remote Development Environments — Evaluation Plan

**Status:** Architecture planning only. **No implementation and no vendor selection.**

## Goal and scope

- Provide reproducible remote developer workspaces on **company-managed Kubernetes / OpenShift**, on-premises or in company-controlled Kubernetes infrastructure.
- Developers connect with **VS Code / SSH** to a workspace defined by the Dev Container specification.
- Evaluate **Coder and DevPod equally**. Do not select a workspace platform until both have been tested with the same criteria.
- Retain the existing architecture: agent orchestration, RAG / memory, MCP tooling, GitLab, CI and sensitive state remain under company control; only approved LLM inference uses external providers.
- **Out of scope:** Devcontainers hosted directly on a third-party PaaS; implementing a custom CLI; production deployment in this planning phase.

## Proposed architecture (C4-style deployment view)

~~~mermaid
flowchart TB
    dev["Developer<br/>VS Code / SSH / existing CLI"]
    choice{"Workspace option<br/>Coder OR DevPod<br/>decision pending"}
    subgraph company["Company-managed Kubernetes / OpenShift"]
        workspace["Developer Workspace Pod<br/>Devcontainer / repo / tooling / PVC"]
        agentApi["Agent API / Orchestrator<br/>existing platform concept"]
        osGateway["OpenShell Gateway"]
        agent["Separate OpenShell Agent Sandbox Pod<br/>restricted untrusted execution"]
        mcp["Internal MCP Gateway<br/>authorization / audit"]
        kb["Knowledge Base / RAG / Agent Memory APIs"]
        systems["GitLab / Package Registry / CI / Test Tools"]
        llmGateway["Approved LLM Gateway<br/>egress / redaction / budgets"]
    end
    llm["External LLM inference<br/>approved enterprise endpoint"]
    dev <-->|"secure IDE tunnel"| workspace
    dev -->|"choose workspace"| choice
    choice -->|"provision / connect"| workspace
    workspace -->|"agent task"| agentApi
    agentApi -->|"start or access sandbox"| osGateway
    osGateway --> agent
    workspace -->|"scoped tool calls"| mcp
    agent -->|"scoped tool calls"| mcp
    mcp --> kb
    mcp --> systems
    agent --> llmGateway
    llmGateway -->|"minimized prompts"| llm
~~~

**Important boundary:** The developer workspace is not the OpenShell sandbox. A developer may need an interactive shell and build tools, while the agent executes as an untrusted process in a separately restricted environment. Exact OpenShell integration is an evaluation item, not an existing integration.

## Components and access model

| Component | Planned role | Access principle |
|---|---|---|
| Workspace manager | Coder **or** DevPod; provisioning, connect, start/stop | Evaluated, not selected |
| Devcontainer | Source checkout, dependencies, IDE backend, test tooling | Per-user/project workspace; no cluster-admin |
| Workspace storage | Persistent home / working tree where needed | Scoped PVC; lifecycle and backup policy TBD |
| Agent API / OpenShell | Launch sandboxed coding tasks | Separate agent identity and sandbox policy |
| Knowledge Base / RAG | Search documents, code context, conventions | Reach through internal API, project-scoped |
| Agent memory | State, checkpoints, history | Access via Agent API; no direct database credentials |
| MCP Gateway | Approved GitLab, CI, registry, test, DB and internal API tools | Per-user/agent authorization, typed calls and audit |
| Inference gateway | Approved external model access | Only selected context leaves the company boundary |

**Full tooling availability** means all required internal capabilities are reachable through authorized APIs. It does **not** mean unrestricted database, production, secret, or network access.

## Coder vs. DevPod — open evaluation

| Criterion | Coder | DevPod | How to validate |
|---|---|---|---|
| Architecture | Self-hosted workspace control plane and templates | Client-driven CLI with Kubernetes provider | Compare lifecycle, operator effort and governance |
| Dev Containers | Devcontainer integration; container build strategy requires review | Dev Container-native workflow | Use the same example project and devcontainer.json |
| VS Code / SSH | Available | Available | Measure connect, reconnect and debugging UX |
| Kubernetes / OpenShift | Evaluate templates, security constraints and storage | Evaluate Kubernetes provider, RBAC and storage | Test restricted namespaces and non-root execution |
| Identity / governance | Assess OIDC, team permissions, audit and policies | Assess client credentials, Kubernetes RBAC, central audit | Enforce equivalent least-privilege outcomes |
| OpenShell integration | Design an adapter/task handoff | Design an adapter/task handoff | Same sandbox and policy test for both |
| Operations / licensing | Evaluate server operations and licensing | Evaluate client/provider operations and support | Compare TCO and maintenance burden |

**Do not assume** Docker-in-Docker, a mounted host Docker socket, privileged pods, or cluster-admin privileges are acceptable on OpenShift. Compare supported rootless/image-building patterns (including Coder Envbuilder where suitable) in the PoC.

## Security and trust boundaries (to evaluate)

- **Identity:** SSO/OIDC where supported, per-developer/project Kubernetes RBAC, short-lived tool credentials, separate agent identity.
- **Isolation:** Workspace pods and OpenShell sandbox pods use independently enforced boundaries; agent code does not gain workspace or Kubernetes privileges.
- **Network:** Default-deny where feasible; explicit GitLab, internal registry, KB, MCP and agent API endpoints. OpenShell sandbox namespaces require **enforced ingress and egress Kubernetes NetworkPolicy**, not just policy objects.
- **Tooling:** Typed project-scoped MCP tools; write actions and production access require explicit authorization / human approval.
- **Code and storage:** Source remains on company-managed infrastructure. Validate PVC isolation, retention, cleanup and protection from cross-user access.
- **Secrets:** Do not pass raw provider tokens or production credentials to the agent or devcontainer; broker narrow capabilities.
- **Supply chain:** Approved base images, image scanning/signing, controlled dependency registries, predictable devcontainer builds.
- **Audit:** Record workspace lifecycle, API/tool invocation and approvals without exporting sensitive prompts, logs or traces to SaaS telemetry.
- **OpenShift fit:** Confirm SCC, non-root UID, volume permissions, CNI behavior, namespace ownership and controller permissions in a real cluster.
- **Code changes:** Prefer agent changes via isolated branch / diff / review flow over writable sharing of the developer workspace with an agent.

## CLI / client strategy — not a commitment

- **First:** Evaluate existing Coder and DevPod CLIs and IDE integrations.
- **Later, only if needed:** Define a thin company CLI or portal facade over an abstract workspace lifecycle API: create, list, open, stop, delete.
- Never build a second Kubernetes provisioner merely to rename existing vendor commands.

## Evaluation plan (future work, not implemented)

1. **Architecture review:** Confirm threat model, trust zones, developer/agent identities, KB/MCP API contracts and OpenShift cluster prerequisites.
2. **Parallel PoCs:** Run the **same repository and devcontainer specification** with Coder and DevPod on company-managed Kubernetes/OpenShift.
3. **Integration checks:** VS Code access; start/stop/reconnect; persistent storage; GitLab clone; internal registry; KB search; MCP tooling; CI/test; OpenShell sandbox handoff.
4. **Negative security tests:** Deny unknown egress, cross-project KB requests, direct secrets, privilege escalation, bypass of the agent gateway and unauthorized write tools.
5. **Decision gate:** Compare security, functionality, user experience, maintainability, license/TCO and exit portability before choosing Coder, DevPod or another option.

**Acceptance target:** Either evaluated tool can provide a usable remote developer workspace without weakening the current on-prem security model. Selecting a tool requires documented evidence, not a predetermined preference.

## Reference documentation

- [Dev Containers specification](https://containers.dev/)
- [Coder: Dev Containers](https://coder.com/docs/user-guides/devcontainers)
- [Coder: Working with Dev Containers](https://coder.com/docs/user-guides/devcontainers/working-with-dev-containers)
- [DevPod: Kubernetes provider and other providers](https://devpod.sh/docs/managing-providers/add-provider)
- [DevPod: CLI Quickstart](https://devpod.sh/docs/getting-started/quickstart-devpod-cli)
- [OpenShell: Kubernetes setup](https://docs.nvidia.com/openshell/kubernetes/setup)
- [OpenShell: Sandbox policies](https://docs.nvidia.com/openshell/latest/how-it-works/policies/overview)
