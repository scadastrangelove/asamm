# Agentic SAMM — An OWASP SAMM Extension for AI-Driven Development

> **Author:** Sergey Gordeychik · CyberOK · scadastrangelove@gmail.com · 2026
> **License:** [CC BY-SA 4.0](LICENSE.md) — cite as: Gordeychik, S. (2026). *Agentic SAMM*. CyberOK.

---

# Part 1 — Migration Path

*For teams with an existing SAMM-aligned security program*

The migration path is not a restart. Its purpose is to answer three questions for each area of an existing program:

> What you already have still works — and for what reason.
> What you already have needs extension — and what specifically must be added.
> What you already have may be actively misleading — and why.

The third category is the most important. The migration problem is not that existing controls disappear; it is that they continue to produce assurance signals for a system boundary that no longer matches the real one.

## 1.1 What Carries Over Structurally

The following SAMM practices retain their structural validity in an agentic context. They remain necessary, though their threat model expands at the margins.

**Dependency management and SCA.** Third-party library risk does not change because an agent is involved. The dependency surface grows — agent frameworks, connector libraries, and MCP client implementations must be included in SCA scope — but the practice itself is unchanged.

**Secret management.** Secrets in environment variables, config files, and CI/CD pipelines remain equally dangerous. The additional concern — that an agent might exfiltrate secrets it has legitimate read access to — is addressed in the Tool Abuse threat class, not in secret management practice itself.

**Authentication and authorization for human users and service accounts.** Classical identity controls remain fully valid. The agentic extension concerns what happens after an authorized agent begins acting, not whether the agent authenticated correctly.

**Incident response process.** IR playbooks, escalation paths, and communication processes are structurally unchanged. The agentic extension is in detection coverage and forensic capability, not in the response structure itself.

**Secure infrastructure configuration.** Hardening, patching, and network segmentation apply without modification.

## 1.2 What Needs Agentic Extension

**Threat Modeling → extend to include Context Threat Modeling.**
Existing threat models cover data flows, trust boundaries between services, authentication surfaces, and API exposure. For agentic systems, two additional surfaces must be modeled explicitly: context sources and sinks (where does content enter the agent's context window, and from what trust level), and tool invocation paths (what tools exist, what permissions they carry, and how they could be abused through the agent layer).

*Extension required:* add context flow diagrams alongside data flow diagrams; add tool registry to the trust boundary model; explicitly model Context Injection and Tool Abuse threat paths.

**Code Review → extend to include Prompt and Schema Review.**
System prompts, tool schemas, MCP server definitions, and agent configuration files are security-critical: a modified system prompt can change agent behavior as significantly as a code change. If these artifacts are not in review scope, the process has a structural blind spot.

*Extension required:* classify system prompts, tool schemas, agent configs, and MCP server definitions as security-critical artifacts subject to the same review requirements as application code.

**Governance → extend to include Agent and Tool Ownership.**
Classical governance assigns ownership to systems and services. Agentic governance must also assign ownership to agents, tool registries, MCP servers, and approval policies — and must classify agents by autonomy level.

*Extension required:* define owner for each agent, tool registry entry, MCP server, and approval policy; classify agents by autonomy tier; define which tools are allowed by default, conditionally allowed, or prohibited; define kill-switch and escalation ownership.

**Execution Boundary Control → extend to include Sandboxed Tool Execution.**
Agent tool invocations carry real execution consequences. A tool that runs on the host with broad filesystem and network access is not constrained by application-layer policy alone.

*Extension required:* define where tools execute; specify filesystem and network access per tool; require isolated execution by default for development-time tooling and high-blast-radius operations; treat sandbox policy result as part of action provenance.

**DAST → extend to include Behavioral Testing.**
Classical DAST validates that application endpoints behave correctly under adversarial input. For agentic systems, the equivalent is behavioral testing: does the agent behave safely when its context contains injection payloads, when tools return unexpected results, or when a sequence of operations produces unsafe composite effects?

*Extension required:* define behavioral test scenarios covering Context Injection and Tool Abuse threat paths; integrate adversarial context testing into the CI pipeline; treat behavioral test coverage as a verification metric.

**Logging and Monitoring → extend to include Action Provenance.**
Application logging captures events. For agentic systems the security-relevant question is not only what happened, but what caused it: what context source influenced the decision, what tool was selected, what approval event preceded the action, and what sandbox policy applied.

*Extension required:* define action provenance as a required log structure including context source, tool selected, approval event, scope exercised, and sandbox policy result; ensure agent action logs are ingested into the security monitoring pipeline.

**Penetration Testing → extend scope to include the agent layer.**
Penetration test scopes defined as "the application and its APIs" will routinely exclude the agent loop, MCP servers, connector layer, and development toolchain. This is a structural scope gap, not a finding gap.

*Extension required:* explicitly include agent loop, tool registry, MCP servers, connector layer, and development-time toolchain in penetration test scope; add prompt injection and tool abuse scenarios to test methodology.

## 1.3 Inherited False Positives

These are signals that will appear green after agentic components are introduced, while leaving real attack surface uncovered.

---

**"Threat model is complete."**

If the threat model was completed before agentic components were introduced, or without modeling context sources and tool invocation paths, it does not cover the primary agentic threat classes. The document exists and passed review, but the actual threat surface was not modeled.

*Signal to watch:* a threat model that contains no mention of context injection, tool abuse, MCP servers, or autonomy window is incomplete for an agentic system regardless of its completion status.

---

**"We have 100% pull request review coverage."**

PR review coverage measures whether human eyes reviewed code changes. It says nothing about whether system prompts, tool schemas, agent configs, or MCP server definitions are included in review scope. If these artifacts are deployed through separate pipelines or managed as configuration rather than code, they will not appear in PR metrics.

*Signal to watch:* determine whether any security-critical agentic artifact can be modified and deployed without appearing in the PR review queue.

---

**"DAST scan returned clean."**

A clean DAST report means application endpoints behaved correctly under the scanner's test cases. It provides no signal about whether the agent behaves safely under adversarial context, tool abuse attempts, or instruction injection via retrieved content.

*Signal to watch:* identify which behavioral test scenarios are covered by the current test suite. A clean DAST report with zero behavioral test coverage is a false negative signal for agentic risk.

---

**"Penetration test passed."**

If the pentest scope did not include the agent loop, MCP servers, and connector layer — and most scopes written before agentic components were introduced will not — the pass result says nothing about agentic security posture.

*Signal to watch:* review the pentest scope definition. If MCP servers, tool registry, agent configuration, and development toolchain are not listed as in-scope, the result is not applicable to the agentic attack surface.

---

**"Least privilege is implemented."**

Least privilege for service accounts and application roles is correctly implemented. This does not address least privilege for the agent's tool invocations. An agent may hold legitimately broad permissions appropriate for its range of tasks, while still exercising far more privilege than any single task requires.

*Signal to watch:* determine whether tool scope is bounded per-task or per-invocation, or whether the agent operates with a fixed broad scope for its entire session.

---

**"SCA / dependency posture is clean."**

A clean SCA report covers the application dependency tree. It covers little about system prompts, tool schemas, MCP registrations, model provider dependencies, and non-code artifacts in the agentic path. It may also miss development-time agent tooling that sits outside the production SBOM boundary.

*Signal to watch:* determine whether the SBOM includes MCP server packages, agent framework dependencies, and connector libraries — not only production application dependencies.

---

**"Security training is current."**

Developer security training covering injection, authentication, secret handling, and secure coding does not cover prompt injection, tool schema security, context trust modeling, or agentic threat taxonomy. Developers building agentic systems with current training are building on an incomplete threat model.

*Signal to watch:* assess whether security training materials cover any of the core agentic threat concepts defined in this document.

## 1.4 Sequenced Migration Roadmap

**Phase 0 — Inventory the agentic surface**
Enumerate agents and orchestrators, system prompts and policy prompts, memory and state stores, context sources, tools and MCP servers, approval checkpoints, high-blast-radius actions, and execution boundaries. Without inventory, visibility programs instrument only what is already known. For agentic systems the biggest early risk is often unknown tool surface.

**Phase 1 — Establish visibility**
Extend logging to capture context source, tool invocations, approval events, and sandbox policy results. This is the prerequisite for everything else — without action provenance, you cannot assess current exposure.

**Phase 2 — Close the review gap**
Bring system prompts, tool schemas, agent configs, and MCP server definitions into the code review process. This is a process change, not a technical one, and has immediate impact on supply chain and toolchain attack surface.

**Phase 3 — Extend threat modeling**
Re-run threat modeling for any system with agentic components, adding context flow diagrams and tool invocation paths. Identify autonomy windows and estimate temporal blast radius for each. Establish agent and tool ownership.

**Phase 4 — Add behavioral testing**
Define and implement behavioral test scenarios for Context Injection and Tool Abuse threat paths. Integrate into CI. This is the agentic equivalent of adding security test cases to a regression suite.

**Phase 5 — Bound the autonomy window**
Based on blast radius assessment from Phase 3, implement approval gating and sandboxed execution at appropriate action boundaries. Start with irreversible or high-blast-radius operations.

**Phase 6 — Extend pentest scope**
Update penetration test scope definitions to include agent layer, MCP servers, and development toolchain. Commission behavioral red team exercises covering prompt injection and tool abuse scenarios.

---