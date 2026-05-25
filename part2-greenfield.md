# Agentic SAMM — An OWASP SAMM Extension for AI-Driven Development

> **Author:** Sergey Gordeychik · CyberOK · scadastrangelove@gmail.com · 2026
> **License:** [CC BY-SA 4.0](LICENSE.md) — cite as: Gordeychik, S. (2026). *Agentic SAMM*. CyberOK.

---

![Figure 5: Migration path vs greenfield path](assets/figures/fig5-two-paths.svg)
*Figure 5: Both paths share the same destination — the shared control reference — but start from different premises.*

# Part 2 — Greenfield Path

> **v0.2 addition:** For structured audit methodology, see `audit/auditor-process.md`. The audit process defines three tracks (Track A self-audit, Track B independent, Track C agent-as-code-auditor), required phase gates (mission interview before technical inventory), and prompt libraries for systematic data collection.

*For teams building agentic systems without a pre-existing SAMM-aligned security program*

A greenfield path is shorter than a migration path not because agentic systems are simpler, but because they are not constrained by inherited assumptions. A new program does not need to preserve control evidence built for a non-agentic boundary. It can begin from the correct premise: the system being secured includes not only software artifacts and deployment infrastructure, but also context flows, tool boundaries, delegated authority, approval checkpoints, and runtime behavior.

The goal of the greenfield path is not completeness on day one. It is to establish a minimum secure baseline that bounds blast radius early, creates observability before scale, and can be extended at each turn of the development spiral as the system gains autonomy, tools, and operational reach.

A control that cannot be re-scoped as the agent gains new tools or autonomy is not a greenfield control. It is a future migration problem.

## 2.1 Greenfield Design Principles

**Start with bounded agency, not maximum capability.**
Do not begin by exposing the agent to every available tool and then trying to constrain it afterward. Start from the smallest useful tool surface, the shortest autonomy window, and the lowest available blast radius. Expand only when observability and control maturity justify it.

**Establish observability before optimization.**
A system that cannot explain what context influenced a decision, what tool was invoked, and what approval boundary was crossed is not ready for autonomy tuning. Logging and provenance are not operational polish. They are the prerequisite for all later assurance.

**Treat every new tool as a new trust boundary.**
Adding a tool is not equivalent to adding a helper function. It is equivalent to adding a new delegated authority surface with its own threat model, failure modes, and containment needs.

**Design for reviewable autonomy.**
The question is not whether humans remain in the loop in principle. The question is whether the number, timing, and quality of checkpoints are realistic relative to action volume and blast radius. If human review cannot keep up, the checkpoint is nominal rather than effective.

**Assume reassessment at the next spiral turn.**
Every autonomy increase, tool addition, connector addition, or context-source expansion must trigger re-evaluation of the system's threat model, autonomy window, and temporal blast radius.

## 2.2 Minimum Secure Baseline

A greenfield agentic system should not go live without the following baseline controls in place.

**1. Inventory the agentic surface.**
Before defining controls, enumerate what exists: agents and orchestrators, system prompts and policy prompts, memory and state stores, context sources, tools and MCP servers and connectors, approval checkpoints, high-blast-radius actions, and execution boundaries. If this inventory does not exist, the system does not yet have a defined assurance boundary.

**2. Bound the execution boundary.**
All agent-executed actions should run in a constrained environment by default. The design must define where tools execute, what filesystem and network access they have, what secrets are accessible, what actions are irreversible, and what policy is enforced when a boundary is crossed. A sandbox is not an optimization. It is the primary containment mechanism for misaligned or compromised agent behavior.

**3. Minimize delegated authority.**
The agent should not operate under a broad fixed scope if narrower task-bounded scopes are possible. Define which tools are always allowed, which require approval, which are prohibited, what scope each tool receives, and what actions require a stronger checkpoint.

**4. Require action provenance.**
For each security-relevant agent action, record: context source and trust level, selected tool, task or plan reference, approval event if any, scope exercised, sandbox or execution policy applied, and resulting side effect or external destination.

**5. Define behavioral test cases before production.**
The minimum requirement is a behavioral test suite that exercises the main threat classes: context injection, tool abuse, autonomy-window exploitation, and supply-chain-relevant configuration tampering.

**6. Put prompts, schemas, and configs under security review.**
Prompts, tool schemas, MCP definitions, connector configs, and policy artifacts must be treated as reviewable security-relevant assets. If they can change outside the normal review path, the system has a structural control gap from day one.

## 2.3 Priority-Ordered Greenfield Controls

Ordered by likelihood × blast radius, with preference for controls that both reduce immediate harm and improve the quality of future assurance evidence.

**Priority 1 — Establish the containment boundary.**
Implement isolated execution for tools, a basic tool allow/deny policy, explicit approval for destructive or irreversible actions, and secret minimization inside the agent execution boundary. This comes first because it limits the immediate consequences of Context Injection, Tool Abuse, and Autonomy Window Exploitation regardless of how mature the team's verification or governance processes are.

**Priority 2 — Establish observability.**
Implement action provenance logging, context provenance metadata, tool-call logging into the security monitoring pipeline, and periodic review of intent–action gap. This comes second because a system that cannot observe its own decisions cannot safely tune autonomy, expand tooling, or interpret failures.

**Priority 3 — Establish behavioral verification.**
Implement behavioral test cases for context injection, tool abuse, ambiguous-task execution, unsafe action chaining, and high-blast-radius workflows. This comes before configuration-control maturity because the team must first learn whether the system behaves safely under realistic adversarial or misaligned conditions. Process control over changes is necessary, but it is less useful if the organization still lacks evidence about what safe behavior looks like.

**Priority 4 — Establish reviewable configuration control.**
Once behavioral expectations are defined, bring system prompts, tool schemas, MCP definitions, connector configs, and policy artifacts under explicit security review. At this stage the goal is not only to control change, but to ensure that changes are reviewed against a known behavioral baseline rather than against syntax or policy alone.

**Priority 5 — Establish spiral reassessment triggers.**
Define mandatory reassessment whenever the agent receives a new tool, broader scope, a longer autonomy window, new external context sources, persistent memory, or access to higher-blast-radius environments. This is where the spiral becomes operational rather than conceptual.

## 2.4 Greenfield False Starts

A greenfield program avoids legacy assumptions but creates a different risk: teams confuse design intent with effective control. The following are common failure modes in early agentic programs.

The first three concern runtime controls; the second three concern assurance and control-process failures. Both categories produce the same result: assurance that exists on paper rather than in evidence.

---

**"We designed containment, therefore containment exists."**
A sandbox boundary described in architecture is not equivalent to a sandbox boundary enforced at runtime. Teams often specify constrained execution but fail to verify filesystem, network, or process escape paths under actual tool behavior.

---

**"We added approval checkpoints, therefore humans remain in control."**
A checkpoint is only effective if human reviewers can realistically evaluate the volume, pace, and consequence of actions presented to them. When action volume exceeds review capacity, the checkpoint becomes nominal rather than effective.

---

**"We limited agent scope, therefore least privilege is satisfied."**
Session-wide authority is often relabeled as acceptable because the system is still early-stage. In practice this creates immediate privilege debt: the agent may hold permissions reasonable in aggregate but excessive for any given task.

---

**"We have provenance logs, therefore behavior is explainable."**
Many systems log actions without logging the context source, trust level, approval boundary, or policy evaluation that led to the action. This creates traceability theater rather than usable assurance.

---

**"We review prompts and configs, therefore behavioral risk is covered."**
Review of prompts, schemas, and configuration files is necessary, but without behavioral verification it only proves that artifacts passed a process gate. It does not prove that the resulting system behaves safely.

---

**"We added tools incrementally, therefore the threat model remained current."**
Tool growth is often treated as ordinary feature expansion. In agentic systems, every new MCP server, connector, or execution-capable tool is a new trust boundary and should trigger explicit reassessment.

## 2.5 Greenfield Spiral Check

At the end of each development iteration, before expanding autonomy or tool surface:

1. What new context sources can now influence the agent?
2. What new tools or connectors can it invoke?
3. Has the autonomy window changed?
4. Has the temporal blast radius increased?
5. Are action provenance and review checkpoints still sufficient for the current action volume?
6. Did any new inherited assumptions enter the system through convenience or scale?

If the answer to any of these is yes, revisit the relevant design, verification, and operations controls before expanding further.

## 2.6 Greenfield Readiness Assessment

A greenfield system that has infrastructure, prompts, tools, and tests, but cannot demonstrate containment, provenance, and action-bounded authority, is feature-complete before it is security-complete.

A system is ready for scaled use only when the following outcomes are demonstrably true — not merely present in design or process documentation.

| Outcome | Readiness question |
|---|---|
| **The agentic surface is known** | Can the organization enumerate agents, tools, MCP servers, prompts, schemas, configs, and memory stores without relying on tribal knowledge? |
| **Containment is real, not assumed** | Can the organization demonstrate that agent-executed tools are bounded by an enforced execution policy rather than only architectural intent? |
| **Authority is bounded at action level** | Can the organization show that high-risk actions require narrower scope or stronger checkpoints than low-risk actions? |
| **No security-critical artifact can change without review** | Can any prompt, schema, MCP definition, connector config, or policy artifact be deployed without passing through an approved review path? |
| **Behavior is tested, not inferred** | Does the system have repeatable behavioral tests for the primary threat classes rather than relying only on unit, integration, or DAST-style evidence? |
| **Actions are reconstructable** | For any security-relevant action, can the team reconstruct the triggering context, selected tool, approval event, exercised scope, and resulting side effect? |
| **Human checkpoints are effective** | Can the organization show that action volume and timing remain within realistic human review capacity for high-blast-radius operations? |
| **Capability growth triggers reassessment** | Is there a defined trigger that forces reassessment when autonomy, tool surface, context surface, or blast radius increases? |

This assessment asks for evidence of state, not evidence of process. A team may have review, logging, or approval mechanisms on paper and still fail if those mechanisms do not produce the required security outcome.

---
---

## 2.7 Audit Methodology Reference *(introduced v0.2; updated v0.3)*

The structured audit methodology introduced in v0.2 is in the `audit/` directory of the repository. It defines three audit tracks:

- **Track A — Self-audit:** the development agent audits its own environment. Requires an external verification pass (Phase 4.5) before the report leaves draft status.
- **Track B — Independent audit:** separate auditor without direct environment access. All agent self-report treated as [inferred].
- **Track C — Agent-as-code-auditor:** an agent reviews a codebase it did not build. Mission interview must complete before any code access.

**Critical ordering constraint:** Phase 1 (Mission Interview) must complete before Phase 2 (Data Collection). Blast radius cannot be correctly assigned without knowing what the owner cannot afford to lose. This is the single most common cause of audits that are technically correct but operationally useless.

**Bounded severity.** *(v0.3)* When a finding's severity depends on a Phase 1 question the owner has not yet answered, the auditor must not manufacture certainty. Express severity as a bounded tuple: `High [bounded by C1]` means "High if C1 is answered unfavorably; may reduce if C1 clarifies the situation." This practice was validated in the PentOPS audit where 5 of 18 findings carried explicit bounds due to unanswered deepening questions. The audit continued — and the bounds gave the owner actionable information about what answers would change which severities.

**Phase 0 checklist.** *(v0.3)* Before any data collection, the auditor should classify the engagement:

- **Environment type:** production / staging / dev / personal workstation / hybrid
- **Data classification:** regulated / commercial secret / internal / public
- **Attacker model:** opportunistic / targeted / state-level / insider
- **Shared responsibility:** self-hosted LLM? SaaS tools? vendor auth? cloud infrastructure?
- **Repository topology:** monorepo / multi-repo / non-git working tree
- **Audit track:** A (self-audit) / B (independent) / C (agent-as-code-auditor)

This classification determines scope, severity calibration, and which deepening questions to prioritize. Without it, auditors invent categories ad hoc — reducing method parity across audits.

See `audit/auditor-process.md` for the complete process, `audit/prompt-library.md` for data collection prompts, and `audit/environment-adapters.md` for platform-specific verification commands.

**Report sanitization before sharing.** *(v0.3)*

An audit that finds credential leaks will, by construction, contain those credentials in its findings — API keys, OAuth tokens, PATs, internal hostnames, file paths. The irony is structural: the more thorough the audit, the more secrets it captures; the more useful the report, the more dangerous it is to share unsanitized. An audit report that discovers credential leaks and is then shared without redaction *is itself a credential leak* — through the very artifact meant to prevent them.

Before any report leaves the auditor–owner boundary:

1. **Strip all literal secrets.** Replace every token, key, and password with a redacted placeholder: `tvly-dev-eJRPznY…` → `[REDACTED-TAVILY-KEY]`. Keep enough structure to show the finding class (e.g., "API key with default value in settings.py") without the actual credential.
2. **Strip internal hostnames and IPs** that are not necessary to understand the finding. `192.168.100.206` → `[INTERNAL-HOST]`.
3. **Strip PII** — real usernames, email addresses, file paths containing usernames (`/Users/arudakov/…` → `/Users/[USER]/…`).
4. **Triple-check.** Run a grep for known secret patterns (`grep -iE "token|key|password|secret|oauth|pat|tvly|y0__|bearer" report.html`). If any literal credential survives, redact it. Repeat the grep after redaction to confirm.
5. **Automate where possible.** A `sanitize-report.sh` that applies regex replacements for known patterns is less error-prone than manual review. The script itself should be versioned alongside the audit tooling.

This applies to reports shared for any purpose — peer review, methodology improvement, public case studies, or submission to the ASAMM project.

---

---
