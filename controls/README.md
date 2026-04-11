# Controls Reference

All 17 controls in Agentic SAMM, organized by SAMM function.

Each control has a stable ID, L1/L2/L3 maturity levels, evidence criteria, and threat coverage mapping.

---

## Governance (AG)

| ID | Control | Threat coverage | Path |
|---|---|---|---|
| [AG-01](AG-01.md) | Agent Registry and Autonomy Classification | C3, W1 | Both |
| [AG-02](AG-02.md) | Tool Registry Governance | C2, C4 | Both |
| [AG-03](AG-03.md) | Kill Switch and Escalation Ownership | C3 | Both |

## Design (AD)

| ID | Control | Threat coverage | Path |
|---|---|---|---|
| [AD-01](AD-01.md) | Context Threat Modeling | C1, W1 | Both |
| [AD-02](AD-02.md) | Autonomy Window Assessment | C3 | Both |
| [AD-03](AD-03.md) | Tool and Connector Trust Modeling | C2, C4 | Both |
| [AD-04](AD-04.md) | Development-Surface Threat Modeling | C1, C2, C4, E3 | Both |

## Implementation (AI)

| ID | Control | Threat coverage | Path |
|---|---|---|---|
| [AI-01](AI-01.md) | Prompt and Schema Security Review | C1, C4, W1 | Both |
| [AI-02](AI-02.md) | Execution Boundary Control | C2, C3, W2 | Both |
| [AI-03](AI-03.md) | Scoped Tool Authorization | C2, W1 | Both |

## Verification (AV)

| ID | Control | Threat coverage | Path |
|---|---|---|---|
| [AV-01](AV-01.md) | Behavioral Test Coverage | C1, C2, C3, W1 | Both |
| [AV-02](AV-02.md) | Adversarial Prompt and Tool Abuse Testing | C1, C2, W1 | Both |
| [AV-03](AV-03.md) | Pentest Scope Completeness | C1, C2, C3, C4 | Both |

## Operations (AO)

| ID | Control | Threat coverage | Path |
|---|---|---|---|
| [AO-01](AO-01.md) | Action Provenance Logging | W2 | Both |
| [AO-02](AO-02.md) | Intent–Action Gap Monitoring | W2, C1 | Both |
| [AO-03](AO-03.md) | Spiral Reassessment Triggers | C3, W1 | Both |
| [AO-04](AO-04.md) | Behavioral Vulnerability Tracking | W1, W2 | Both |

---

*[← Back to README](../README.md)*
