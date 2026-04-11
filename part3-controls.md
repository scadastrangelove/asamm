# Agentic SAMM — An OWASP SAMM Extension for AI-Driven Development

> **Author:** Sergey Gordeychik · CyberOK · scadastrangelove@gmail.com · 2026
> **License:** [CC BY-SA 4.0](LICENSE.md) — cite as: Gordeychik, S. (2026). *Agentic SAMM*. CyberOK.

---

# Part 3 — Shared Control Reference

This section is the reference layer of the framework. It defines controls applicable to both migration and greenfield programs, organizes them by SAMM function, specifies maturity levels, and maps them to external frameworks. It is intended as a working tool, not a compliance checklist.

## 3.1 How to Read the Matrix

### Maturity levels

| Level | Meaning |
|---|---|
| **L1** | The control exists and is manually applied at key boundaries |
| **L2** | The control is standardized, repeatable, and consistently evidenced |
| **L3** | The control is measured, adaptive, and triggers reassessment as the system evolves |

L1 is the floor, not the goal. A program at L1 across all controls has defined its boundary and can demonstrate it exists. A program at L3 has instrumented that boundary and uses it to drive decisions about autonomy expansion.

![Figure 4: Control maturity levels](assets/figures/fig4-maturity-levels.svg)
*Figure 4: L1 establishes that a control exists and is applied. L2 makes it repeatable with tracked evidence. L3 makes it adaptive — metrics drive decisions and capability changes trigger reassessment.*

### Evidence vs. process

Throughout the matrix, the Evidence column specifies what proves a control is real, not merely documented. A control that exists in policy but cannot be demonstrated should be treated as L0 for practical assessment, regardless of its formal status.

The distinction that matters: **process says the control is applied; evidence shows the effect of applying it**. For agentic controls this distinction is especially important, because many failure modes — nominal approval checkpoints, undocumented tool surface, architectural sandbox that is not enforced at runtime — produce green process metrics over uncovered risk.

### Column definitions

| Column | Meaning |
|---|---|
| **ID** | Stable short identifier for cross-referencing |
| **Control** | Plain-English name |
| **SAMM function** | Primary SAMM function this control extends or introduces |
| **Threat coverage** | Which taxonomy classes this control addresses (C1–C4, W1, W2) |
| **L1** | Minimum viable implementation |
| **L2** | Managed, repeatable, consistently evidenced |
| **L3** | Measured, adaptive, triggers spiral reassessment |
| **Evidence** | What demonstrates the control is real |
| **Path** | M = Migration, G = Greenfield, B = Both |

---

![Figure 6: Threat taxonomy to control families mapping](assets/figures/fig6-taxonomy-controls.svg)
*Figure 6: Solid lines show primary coverage — the control directly addresses the threat class. Dashed lines show overlay coverage — the weakness overlay amplifies or enables the threat, and the control reduces that amplification.*

## 3.2 Control Matrix

The controls below are written as reference controls; teams should adapt implementation detail to system autonomy, tool surface, and blast radius.

### Family 1 — Governance

---

**AG-01 — Agent Registry and Autonomy Classification**

SAMM function: Governance | Threat coverage: C3, W1 | Path: B

| Level | Implementation |
|---|---|
| L1 | Agents are enumerated with owner, tool set, and autonomy level documented in a maintained register |
| L2 | Autonomy levels are formally classified (e.g., supervised / semi-autonomous / autonomous); each classification defines its review requirements |
| L3 | Autonomy level changes require formal re-classification; classification is reviewed at each spiral turn and after any capability increase |

Evidence: agent registry exists and is current; each entry has a named owner; autonomy level is documented and matches deployed configuration.

*Trust grading in the agent registry.*

Autonomy classification alone is insufficient. An agent may be classified as semi-autonomous and still be an F-grade source — newly deployed, behaviorally untested, with no operational track record. The agent registry should record both autonomy level and trust rating (see Section 0.5). Autonomy expansion requires trust rating improvement, not only technical capability demonstration. An agent cannot hold higher autonomy than its trust rating supports.



---

**AG-02 — Tool Registry Governance**

SAMM function: Governance | Threat coverage: C2, C4 | Path: B

| Level | Implementation |
|---|---|
| L1 | All tools and MCP servers available to the agent are enumerated with owner and access policy |
| L2 | Tools are classified by risk tier (always allowed / conditionally allowed / prohibited); conditional tools have defined approval requirements |
| L3 | Tool registry is versioned; additions trigger automated threat model review; removal of unused tools is tracked and enforced |

Evidence: tool registry exists; each entry has owner and risk classification; prohibited tools cannot be invoked without explicit exception; registry is included in security review scope.

---

**AG-03 — Kill Switch and Escalation Ownership**

SAMM function: Governance | Threat coverage: C3 | Path: B

| Level | Implementation |
|---|---|
| L1 | A named owner can halt any agent's execution; halt procedure is documented and tested |
| L2 | Kill switch can be invoked without requiring production access; escalation path covers out-of-hours scenarios |
| L3 | Kill switch invocation is logged and reviewed; partial-halt capabilities exist (halt specific tools or autonomy scope without full shutdown) |

Evidence: kill switch owner is identifiable; halt was tested in the last cycle; escalation path is current and does not depend on a single individual.

---

### Family 2 — Design

---

**AD-01 — Context Threat Modeling**

SAMM function: Design | Threat coverage: C1, W1 | Path: B

| Level | Implementation |
|---|---|
| L1 | Threat model includes at least one context flow diagram identifying external content sources and their trust levels |
| L2 | All context sources are classified by trust level; threat paths for indirect and persistent context injection are explicitly modeled |
| L3 | Context threat model is updated at each spiral turn; new context sources require threat model amendment before integration |

Evidence: threat model document includes context flow diagrams; each source carries a trust classification; injection threat paths are present in the model, not only in generic notes.

---

**AD-02 — Autonomy Window Assessment**

SAMM function: Design | Threat coverage: C3 | Path: B

| Level | Implementation |
|---|---|
| L1 | Autonomy windows are identified for the system's primary workflows; maximum duration between effective human checkpoints is documented |
| L2 | Temporal blast radius is estimated per autonomy window; checkpoints are designed relative to blast radius, not only workflow convenience |
| L3 | Autonomy window metrics are instrumented; actual checkpoint frequency is compared against designed frequency; deviations trigger review |

Evidence: autonomy windows are documented; blast radius estimates exist; checkpoint design references these estimates explicitly.


*Auftragstaktik and the design of autonomous action.*

Prussian military doctrine introduced the concept of *Auftragstaktik* — mission-type tactics — in the 19th century as a solution to a problem that remains unsolved in most agentic systems: how do you maintain coherent, aligned behavior across a distributed force when communications are unreliable, the situation changes, and no plan survives first contact? Helmuth von Moltke the Elder captured it in 1869: *"Kein Operationsplan reicht mit einiger Sicherheit über das erste Zusammentreffen mit der feindlichen Hauptmacht hinaus"* — no plan of operations extends with any certainty beyond the first encounter with the enemy's main force.

Auftragstaktik's answer was not more detailed orders. It was better-internalized intent. A commander specifies the *Auftrag* (mission objective and its rationale), the *Ressourcen* (available means), and the *Raum* (boundaries of discretion). Subordinates act autonomously within those boundaries, using judgment to achieve the objective when the plan no longer fits the situation.

The mapping to agentic systems is direct. A system prompt is an Auftrag, not an algorithm. Tool access is Ressourcen. The autonomy window is Raum. Security is not achieved by enumerating every prohibited action — it is achieved by the agent understanding the objective well enough to act correctly when the context deviates from what the prompt anticipated. An agent that executes a prompt injection payload is not just exploited; it has failed its Auftrag.

The autonomy window assessment should therefore ask not only "how long before a checkpoint" but "how well does the agent understand the mission intent well enough to resist adversarial deviation from it."

*Mission-centric blast radius.*

Standard blast radius assessment asks how much damage a compromised action causes. Mission-centric assessment asks a more precise question: **does the damage prevent mission completion, and can the mission state be recovered?**

This distinction matters because CIA-centric assessments produce wrong priorities. Code leaking from a public repository is a low-severity event in mission terms even if it scores high on confidentiality impact. An unauthorized push to a protected branch is a high-severity event even if the pushed content is benign, because it compromises mission integrity and may be irreversible within the operational window.

Mission-centric blast radius assessment uses three dimensions:

| Dimension | Question | Grades |
|---|---|---|
| **Mission impact** | Does this action, if wrong, prevent achieving the task objective? | Critical / Significant / Marginal / None |
| **Recovery envelope** | Can mission state be restored, and in how long? | Immediate / Minutes / Hours / Irreversible |
| **Lateral mission impact** | Does this affect concurrent agents or downstream tasks? | None / Adjacent / Cross-domain |

Approval gating requirements follow from position in this space, not from a generic "high/medium/low" label. An action that is Critical × Irreversible × Cross-domain requires a hard checkpoint regardless of how routine it appears in isolation.

This approach was first articulated for industrial control systems by Gordeychik, Gapanovich, and Rozenberg (2016) in the context of railway signalling security: CIA-based assessment systematically underestimates risk to safety-critical systems because it measures information properties rather than operational consequences. The same principle applies to agentic systems operating in production environments.



---

**AD-03 — Tool and Connector Trust Modeling**

SAMM function: Design | Threat coverage: C2, C4 | Path: B

| Level | Implementation |
|---|---|
| L1 | Each MCP server and connector is included in the system's trust boundary model with a defined trust level |
| L2 | Tool invocation paths are modeled as trust boundaries; confused-deputy and chain-exploitation threat paths are explicitly addressed |
| L3 | Trust model is updated when tools are added, modified, or replaced; supply-chain threat paths for tool infrastructure are included |

Evidence: trust boundary model includes tool layer; tool entries have trust classifications; threat paths cover tool abuse scenarios.

---

**AD-04 — Development-Surface Threat Modeling**

SAMM function: Design | Threat coverage: C1, C2, C4, E3 | Path: B

| Level | Implementation |
|---|---|
| L1 | Development-time tooling — IDE plugins, LSP extensions, local MCP servers, pre-commit hooks, CI-integrated agents — is enumerated and included in at least one threat model |
| L2 | Development-surface threat model identifies privileged access paths, secret exposure risks, and injection vectors present during development but not production; mitigations are defined |
| L3 | Development-surface threat model is reviewed at each spiral turn; new development tooling requires threat model amendment; development-time sandboxing is assessed separately from production sandboxing |

Evidence: threat model document explicitly covers development tooling; development-surface attack paths are listed separately from production paths; at least one control addresses development-specific risk.

---

### Family 3 — Implementation

---

**AI-01 — Prompt and Schema Security Review**

SAMM function: Implementation | Threat coverage: C1, C4, W1 | Path: B

| Level | Implementation |
|---|---|
| L1 | System prompts, tool schemas, MCP server definitions, and agent config files are identified as security-critical and included in code review scope |
| L2 | Changes to these artifacts require security-aware review; reviewers are trained on prompt injection and schema poisoning risks |
| L3 | Automated diff analysis flags high-risk changes (new tool permissions, modified trust boundaries, constraint relaxations) for mandatory security review |

Evidence: no security-critical agentic artifact can be deployed without appearing in the review queue; reviewer training covers prompt and schema risk; rejected changes are logged.

---

**AI-02 — Execution Boundary Control**

SAMM function: Implementation | Threat coverage: C2, C3, W2 | Path: B

| Level | Implementation |
|---|---|
| L1 | All agent-executed tool calls run in a defined execution boundary; filesystem and network access is documented per tool |
| L2 | Default execution policy is isolation; host execution requires explicit justification and approval; sandbox policy is enforced, not merely described |
| L3 | Sandbox policy violations are logged and alerted; escape paths are tested in adversarial evaluation; development-time tooling is sandboxed separately from production |

Evidence: sandbox policy document exists and matches deployed configuration; network and filesystem access has been verified under actual tool behavior, not only architecture review; escape attempt tests have been run.

---

**AI-03 — Scoped Tool Authorization**

SAMM function: Implementation | Threat coverage: C2, W1 | Path: B

| Level | Implementation |
|---|---|
| L1 | Agents do not operate under session-wide maximum scope; at least high-blast-radius tools have narrower access |
| L2 | Tool scope is bounded per task or per invocation for conditional tools; broad scope requires explicit justification |
| L3 | Scope minimization is enforced programmatically; scope creep is detected and alerted; per-invocation scope is the default for all non-trivial tools |

Evidence: scope policy is documented per tool; high-blast-radius tools have demonstrably narrower access than the agent's maximum; scope assignments are reviewable.

---

### Family 4 — Verification

---

**AV-01 — Behavioral Test Coverage**

SAMM function: Verification | Threat coverage: C1, C2, C3, W1 | Path: B

| Level | Implementation |
|---|---|
| L1 | Behavioral test cases exist for at least one scenario in each primary threat class (context injection, tool abuse, autonomy-window exploitation) |
| L2 | Behavioral tests are integrated into CI; test results are tracked over time; regressions are treated as security defects |
| L3 | Behavioral test coverage is measured and reported; new threat scenarios are added when new tools or autonomy levels are introduced; adversarial test cases are maintained by a red team function |

Evidence: behavioral test suite exists and runs in CI; test cases map to threat taxonomy classes; failure history is tracked; coverage is reported separately from functional test coverage.

---

**AV-02 — Adversarial Prompt and Tool Abuse Testing**

SAMM function: Verification | Threat coverage: C1, C2, W1 | Path: B

| Level | Implementation |
|---|---|
| L1 | At least one adversarial context injection scenario has been tested against the system; tool abuse paths have been manually exercised |
| L2 | Adversarial testing is conducted on a defined schedule; scenarios cover indirect and persistent injection vectors; tool chain exploitation paths are included |
| L3 | Adversarial evaluation is conducted by a dedicated red team function; novel attack scenarios are introduced at each spiral turn; findings feed into constraint and behavioral test refinement |

Evidence: adversarial test results are documented; findings have remediation tracking; test scope covers both user-facing and retrieved-context injection paths.

---

**AV-03 — Pentest Scope Completeness**

SAMM function: Verification | Threat coverage: C1, C2, C3, C4 | Path: B

| Level | Implementation |
|---|---|
| L1 | Penetration test scope explicitly includes agent loop, at least one MCP server, and at least one connector |
| L2 | Pentest scope covers agent loop, full tool registry, connector layer, and development-time toolchain; prompt injection and tool abuse are included in methodology |
| L3 | Pentest findings are mapped to the agentic threat taxonomy; re-test covers behavioral regressions, not only code-level fixes |

Evidence: pentest scope document names agentic components; findings include behavioral attack results; scope has been updated since agentic components were introduced.

---

### Family 5 — Operations

---

**AO-01 — Action Provenance Logging**

SAMM function: Operations | Threat coverage: W2 | Path: B

| Level | Implementation |
|---|---|
| L1 | Agent actions are logged with at minimum: tool invoked, task reference, and timestamp |
| L2 | Logs include context source and trust level, approval event, scope exercised, and sandbox policy result; logs are ingested into the security monitoring pipeline |
| L3 | Provenance logs are structured and queryable; retention policy covers incident investigation needs; log integrity is protected against agent-writable modification |

Evidence: log records include all L2 fields; a sample incident can be reconstructed from logs without additional investigation; logs are present in the SIEM, not only in application storage.

---

**AO-02 — Intent–Action Gap Monitoring**

SAMM function: Operations | Threat coverage: W2, C1 | Path: B

| Level | Implementation |
|---|---|
| L1 | Periodic manual review of agent action logs identifies significant divergences between stated plan and actual tool invocations |
| L2 | Intent–action gap detection is partially automated; material divergences generate alerts for review |
| L3 | Intent–action gap is a first-class runtime security signal; thresholds are calibrated per agent and autonomy level; gap events trigger spiral reassessment when they indicate constraint failure |

*Observable detection patterns.*

Intent–action gap manifests in three distinct forms, each requiring different instrumentation:

- **Plan–tool divergence:** the agent declared a sequence of steps (read → summarize → report) but the actual tool call log shows a different sequence or additional tools not in the declared plan. Detectable by diffing declared task plan against tool invocation log.
- **Scope expansion:** the agent executed the declared plan but invoked tools outside the task's stated scope — accessing systems, files, or APIs not mentioned in the task framing. Detectable by comparing tool calls against the task's declared resource set.
- **Reasoning opacity:** the agent produced no explicit plan before acting, making divergence invisible without chain-of-thought logging. Systems without explicit plan declaration should treat any tool invocation whose purpose cannot be reconstructed from logs as a gap event by default.

Evidence: gap review process exists and is conducted on schedule; gap events are logged and tracked; at least one gap event has been investigated and closed with documented outcome.

---

**AO-03 — Spiral Reassessment Triggers**

SAMM function: Operations | Threat coverage: C3, W1 | Path: B

| Level | Implementation |
|---|---|
| L1 | A defined list of events triggers security reassessment: new tool added, autonomy window expanded, new external context source enabled |
| L2 | Reassessment events are logged; reassessment includes review of threat model, autonomy window, and behavioral test coverage |
| L3 | Reassessment is time-bounded; incomplete reassessments block capability expansion; reassessment outcomes are tracked against prior baselines |

Evidence: trigger list exists and is current; at least one reassessment has been completed and documented; capability expansion was blocked or conditioned on reassessment completion.

---

**AO-04 — Behavioral Vulnerability Tracking**

SAMM function: Operations | Threat coverage: W1, W2 | Path: B

| Level | Implementation |
|---|---|
| L1 | Unsafe agent behaviors that do not correspond to code defects are tracked as remediation items in the vulnerability management function |
| L2 | Behavioral vulnerabilities are classified by threat class and severity; remediation includes constraint update, behavioral test addition, and regression verification |
| L3 | Behavioral vulnerability backlog is reviewed at each spiral turn; repeat patterns trigger constraint architecture review; disclosure compression risk is assessed for behavioral findings |

Evidence: at least one behavioral vulnerability has been tracked and closed; closure includes a behavioral regression test; the vulnerability management function accepts non-CVE behavioral findings.

---

## 3.3 Framework Mapping

Control IDs are stable across minor versions of this framework. The mapping is directional — it shows functional alignment, not clause-level equivalence.


| Control ID | SAMM Function | NIST AI RMF | NCSC Secure AI | MCP Security Spec |
|---|---|---|---|---|
| AG-01 | Governance | GOVERN 1.1, 1.2 | Principle 1 (Secure design) | — |
| AG-02 | Governance | GOVERN 2.2 | Principle 3 (Secure build) | Security Best Practices §3 |
| AG-03 | Governance | GOVERN 1.7 | Principle 6 (Secure operation) | — |
| AD-01 | Design | MAP 1.5, 2.2 | Principle 1 (Secure design) | — |
| AD-02 | Design | MAP 2.3 | Principle 1 (Secure design) | — |
| AD-03 | Design | MAP 1.5 | Principle 1 (Secure design) | Security Best Practices §2 |
| AD-04 | Design | MAP 1.5 | Principle 1 (Secure design) | Security Best Practices §4 |
| AI-02 | Implementation | MANAGE 1.3 | Principle 5 (Secure deployment) | Security Best Practices §5 |
| AI-03 | Implementation | MANAGE 1.3 | Principle 3 (Secure build) | Security Best Practices §3 |
| AV-01 | Verification | MEASURE 2.6 | Principle 4 (Secure evaluation) | — |
| AV-02 | Verification | MEASURE 2.6, 2.7 | Principle 4 (Secure evaluation) | — |
| AV-03 | Verification | MEASURE 2.7 | Principle 4 (Secure evaluation) | — |
| AO-01 | Operations | MEASURE 2.8 | Principle 6 (Secure operation) | Security Best Practices §6 |
| AO-02 | Operations | MEASURE 2.8 | Principle 6 (Secure operation) | — |
| AO-03 | Operations | GOVERN 1.7 | Principle 6 (Secure operation) | — |
| AO-04 | Operations | MANAGE 2.4 | Principle 6 (Secure operation) | — |

---

## 3.4 Agentic Vulnerability Lifecycle

Classical vulnerability lifecycle assumes a code defect with a CVE identifier, a patch, and a deployment window. For agentic systems, this model is incomplete in three ways: behavioral vulnerabilities have no CVE equivalent; the window between disclosure and weaponization is compressed by AI-assisted exploit development; and the "fix" may be a constraint update rather than a code change.

The following lifecycle applies to behavioral and constraint-level vulnerabilities in agentic systems.

**Discovery**
A behavioral vulnerability may be discovered through adversarial evaluation, production incident, intent–action gap monitoring, or external report. Unlike code vulnerabilities, behavioral vulnerabilities are often discovered through observed unsafe behavior rather than static analysis. Discovery must be accepted from non-CVE sources.

**Reproduction**
Reproduce the unsafe behavior in a controlled environment. Document the triggering context, the tool invocation sequence, the constraint or policy that failed, and the outcome. Reproduction confirms that the finding is real and scopes the blast radius.

**Behavioral classification**
Classify the finding against the threat taxonomy: which primary threat class does it exploit (C1–C4), which weakness overlay enabled it (W1 constraint failure, W2 assurance blindspot), and which ecosystem modifier affects urgency (E1 disclosure compression, E2 composite accountability, E3 development surface). Classification determines remediation approach and priority.

**Containment**
Apply immediate controls to reduce blast radius while permanent remediation is developed. Containment may include: restricting the relevant tool, reducing autonomy window, adding an explicit approval checkpoint, or disabling the context source that carried the injection payload.

**Remediation**
Remediation for a behavioral vulnerability is typically one or more of: constraint update (tighten policy or guardrails), tool scope reduction, context source trust reclassification, or behavioral test addition that would have caught the finding. Code changes may also be required but are often not sufficient alone.

**Regression verification**
Verify that the behavioral test suite now catches the finding. A remediated behavioral vulnerability with no regression test is treated as partially closed. The regression test becomes a permanent member of the behavioral test suite.

**Spiral reassessment**
A closed behavioral vulnerability is a reassessment trigger. Review: does this finding indicate a constraint gap that affects other agents or tools, does it change the autonomy window or blast radius assessment, and does it require updates to the threat model or tool registry governance. Close the spiral turn with updated documentation.

**A note on disclosure compression (E1)**
For agentic systems, the interval between public disclosure of a behavioral attack pattern and its adoption in active exploitation may be very short. Teams should treat novel behavioral attack classes — new prompt injection techniques, new tool abuse patterns — with the urgency of a critical CVE even when no CVE exists. The absence of a CVE identifier does not indicate low severity. It indicates an immature classification system.