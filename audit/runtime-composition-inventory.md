# AIBOM and Runtime Composition Inventory

Version: 0.1  
Status: methodology supplement for ASAMM v0.5+

---

## Purpose

Traditional SBOM and AIBOM practices answer what components are present at build
or deployment time. Agentic systems also need to answer what capabilities can be
assembled at runtime, from which sources, and under whose authority.

This supplement defines an audit artifact for runtime composition: models,
prompts, tools, MCP servers, connectors, datasets, RAG sources, memory stores,
delegated agents, identity boundaries, and policy decisions that can influence
an agentic outcome.

Use this supplement when the Agent Environment Profile shows dynamic tools,
dynamic external context, externally extended agents, multi-agent workflows,
federated workflows, regulated use cases, or high-blast-radius actions.

Source note: this supplement is informed by OWASP GenAI Security Project,
*State of Agentic AI Security and Governance v2.01* (June 2026), CC BY-SA 4.0.

---

## Three Artifacts

| Artifact | Question answered | Minimum use |
|---|---|---|
| Static AIBOM | What AI-relevant components are part of the system baseline? | All custom or high-impact agentic systems |
| Runtime composition inventory | What components, tools, identities, and context sources can be composed during execution? | Dynamic tools, MCP/plugins, multi-agent, or external services |
| Decision trace | Which components contributed to this specific outcome? | High-impact, regulated, or incident-relevant workflows |

---

## Static AIBOM Fields

| Field | Examples |
|---|---|
| Models | provider, model name, version/date, deployment mode, fine-tune ID |
| Orchestrator/framework | LangGraph, AutoGen, OpenAI Agents SDK, Claude Code, custom |
| Prompts and policies | system prompt, policy prompt, project instructions, guardrails |
| Tools and connectors | MCP servers, APIs, shell, browser, repositories, cloud services |
| Data and retrieval | datasets, RAG indexes, document stores, external retrieval sources |
| Memory/state | persistent memory, scratchpads, cache, session store, project files |
| Identity | service accounts, API keys, OIDC clients, delegated user scopes |
| Runtime | container image, VM, local host, CI runner, sandbox, network policy |
| Logging | action logs, trace IDs, approval records, SIEM ingestion |

---

## Runtime Composition Inventory

| Runtime item | Source | Authority exercised | Trust grade | Dynamic? | Evidence |
|---|---|---|---|---|---|
| [MCP server/tool] | package/registry/repo | read/write/exec/network | F6-A1 | yes/no | |
| [RAG/context source] | internal/external | influences reasoning | F6-A1 | yes/no | |
| [Delegated agent] | orchestrator/peer | task/tool delegation | F6-A1 | yes/no | |
| [Credential] | vault/OIDC/static secret | system access | F6-A1 | yes/no | |
| [Policy decision] | hook/policy engine/human | allow/deny/escalate | F6-A1 | yes/no | |

Minimum required fields:
- component or capability name
- source and owner
- authority exercised
- trust grade
- whether it can change during runtime
- evidence tag and artifact reference

---

## Decision Trace

For high-impact workflows, reconstruct the path from input to outcome:

| Step | Component | Input/context | Action/decision | Identity/authority | Evidence |
|---|---|---|---|---|---|
| 1 | | | | | |
| 2 | | | | | |
| 3 | | | | | |

The decision trace should answer:
- What context influenced the decision?
- Which tools or agents contributed?
- Which identity or credential authorized each action?
- Which policy or human approval allowed the step?
- Which logs prove the sequence?

If this cannot be reconstructed, record a W2 Assurance Blindspot and cap AO-01
and AO-02 accordingly.

---

## Control Mapping

| ASAMM control | Runtime composition relevance |
|---|---|
| AG-01 | Agents and orchestrators appear as registered actors |
| AG-02 | Tools, MCP servers, connectors, and registries appear as governed entries |
| AG-04 | Delegated agents and inter-agent channels appear as trust boundaries |
| AD-01 | Context sources and retrieval inputs are classified |
| AD-02 | Highest-blast action path includes runtime-composed components |
| AD-03 | Tool and connector trust grades are recorded |
| AD-04 | Development and CI composition is included when agents modify code or pipelines |
| AI-01 | Prompts, schemas, manifests, and policy artifacts are reviewable components |
| AI-03 | Runtime tool scope and invocation authority are recorded |
| AI-06 | Identity, credential, and delegation chains are recorded |
| AO-01 | Logs can reconstruct the runtime composition path |
| AO-03 | New dynamic component classes trigger reassessment |

---

## Evidence Rules

- A static dependency list alone is not enough for dynamic agentic workflows.
- Vendor documentation is `[config]`, not `[empirical]`, unless the auditor directly observes behavior.
- A component that can be discovered at runtime but is not listed should be treated as `[unknown]`, not absent.
- Runtime-loaded components without provenance evidence start at F6 trust.
- For high-impact workflows, inability to reconstruct the decision trace is a finding even when no unsafe action was observed.

---

## Minimal Starter Inventory

Small teams can start with one YAML file:

```yaml
runtime_composition:
  system: ""
  owner: ""
  last_reviewed: ""
  components:
    - id: ""
      type: model|prompt|tool|mcp_server|connector|rag_source|memory|agent|credential|policy
      owner: ""
      source: ""
      authority: ""
      dynamic: false
      trust_rating: "F6"
      evidence:
        tag: config
        artifact: ""
  reassessment_triggers:
    - new MCP server
    - new external connector
    - model or orchestrator version change
    - new delegated agent path
    - credential scope expansion
```
