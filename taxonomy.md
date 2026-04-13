# Agentic SAMM — An OWASP SAMM Extension for AI-Driven Development

> **Author:** Sergey Gordeychik · CyberOK · scadastrangelove@gmail.com · 2026
> **License:** [CC BY-SA 4.0](LICENSE.md) — cite as: Gordeychik, S. (2026). *Agentic SAMM*. CyberOK.

---

# Agentic Threat Taxonomy

*Standalone reference. For full context see [Part 0](part0-foundations.md).*

![Figure 2: Agentic threat taxonomy — three layers](assets/figures/fig2-taxonomy.svg)
*Figure 2: The taxonomy separates attack paths (Layer A), system weaknesses that enable them (Layer B), and ecosystem conditions that modify their severity (Layer C).*

---

## Layer A — Attack Paths

| Class | Name | Description |
|---|---|---|
| **C1** | Context Injection | Untrusted content enters the agent's context window and influences its decisions or tool invocations. Subclasses: direct (system prompt / user message), indirect (retrieved document, tool output, web content), persistent (cross-session memory write). |
| **C2** | Tool Abuse | Agent invokes a tool in a way that produces unintended or unsafe side effects. Subclasses: chain exploitation (composite harm from individually authorized actions), privilege escalation, data exfiltration, supply chain (compromised tool or MCP server). |
| **C3** | Autonomy Window Exploitation | Unsafe action sequence completes within a single autonomy window before a human checkpoint can intervene. Subclasses: approval bypass (action volume exceeds review capacity), constraint bypass (behavioral-only constraint violated by adversarial context). |
| **C4** | Supply Chain and Pipeline | Attack enters through a dependency, connector, upstream stage, or code artifact rather than through the agent's context window directly. Subclasses: poisoned dependency, MCP server compromise, upstream pipeline injection, code provenance loss. |

---

## Layer B — Enabling Weaknesses

| Class | Name | Description |
|---|---|---|
| **W1** | Constraint Failure | The agent's behavioral or operational constraints do not hold under adversarial or unexpected conditions. Subclasses: incomplete constraint specification, behavioral-only enforcement of mission-critical boundaries, constraint drift via persistent memory write (v0.2). |
| **W2** | Assurance Blindspot | The system lacks visibility into what the agent did, why it did it, or what context influenced it. Subclasses: missing action logs, missing context provenance, intent-action gap not monitored, self-modification surface not auditable (v0.2). |

---

## Layer C — Ecosystem Conditions

Conditions that modify the severity or likelihood of Layer A and B events:

- **Elevated development privilege:** agents operating in dev environments have broader filesystem, secret, and network access than production agents; blast radius is higher
- **Nominal checkpoints:** approval points exist but action volume or pace makes effective review unrealistic
- **Trust accumulation:** an agent's trust rating improves over time, enabling broader autonomy; a single compromise event can then have larger blast radius
- **Pipeline amplification:** errors or injections in upstream AI outputs are amplified downstream (cloud AI → local agent → production tool)

---

## v0.2 additions to taxonomy

**C1 persistent subclass:** cross-session memory write as C1 vector. An agent writing an incorrect assumption to persistent memory creates a C1 injection that activates in all future sessions without further attacker action. Requires AI-04 (Self-Modification Governance) to address.

**W1 constraint drift subclass:** behavioral constraints stored only in volatile session context reset each session (protective). Behavioral constraints written to persistent memory can drift if the write is wrong or gets stale. This is W1 with cross-session blast radius — distinct from session-local W1 failures.

**C3 constraint bypass subclass — "will not" vs "cannot":** a behavioral-only constraint on a mission-critical boundary (e.g., "scanner must not attack out-of-scope targets") is exploitable via adversarial context in a way that a technical enforcement check is not. AI-05 (Operational Value Constraint Mapping) requires explicit documentation and risk acceptance for this subclass.

---

*For controls addressing each threat class, see [Part 3 — Control Reference](part3-controls.md).*
*For attack pattern examples, see [Part 0 §0.4](part0-foundations.md).*
