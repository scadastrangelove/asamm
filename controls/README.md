# ASAMM Controls — Individual Reference Files

Each file in this directory contains the full specification for one control,
including rationale, implementation levels, evidence criteria, and related controls.

## Control index

### Family AG — Governance

| Control | Title | Added |
|---|---|---|
| [AG-01](AG-01.md) | Agent Registry | v0.1 |
| [AG-02](AG-02.md) | Tool Registry | v0.1 |
| [AG-03](AG-03.md) | Kill Switch | v0.1 |

### Family AD — Design

| Control | Title | Added |
|---|---|---|
| [AD-01](AD-01.md) | Context Threat Model | v0.1 |
| [AD-02](AD-02.md) | Autonomy Window Assessment | v0.1 |
| [AD-03](AD-03.md) | Tool Trust Model | v0.1 |
| [AD-04](AD-04.md) | Dev Surface Threat Model | v0.1 |

### Family AI — Implementation

| Control | Title | Added |
|---|---|---|
| [AI-01](AI-01.md) | Prompt and Config Security | v0.1 |
| [AI-02](AI-02.md) | Execution Boundary | v0.1 |
| [AI-03](AI-03.md) | Tool Authorization | v0.1 |
| [AI-04](AI-04.md) | Agent Self-Modification Governance | **v0.2** |
| [AI-05](AI-05.md) | Operational Value Constraint Mapping | **v0.2** |

### Family AV — Verification

| Control | Title | Added |
|---|---|---|
| [AV-01](AV-01.md) | Behavioral Testing | v0.1 |
| [AV-02](AV-02.md) | Adversarial Testing | v0.1 |
| [AV-03](AV-03.md) | Penetration Testing Scope | v0.1 |

### Family AO — Operations

| Control | Title | Added |
|---|---|---|
| [AO-01](AO-01.md) | Action Logging | v0.1 |
| [AO-02](AO-02.md) | Intent-Action Gap Monitoring | v0.1 |
| [AO-03](AO-03.md) | Reassessment Triggers | v0.1 |
| [AO-04](AO-04.md) | Behavioral Vulnerability Tracking | v0.1 |

## Evidence requirements

| Grade level | Minimum evidence |
|---|---|
| L0 | No demonstrable evidence the control exists |
| L1 | [config] evidence or better |
| L2 | [empirical] evidence or [config] with corroborating [inferred] |
| L2-structural | Architectural property of the platform — not a designed control; counts toward posture, not maturity |
| L3 | [empirical] evidence plus measurement artifacts |

Self-report is [inferred] by default and cannot alone upgrade a control above L1.
