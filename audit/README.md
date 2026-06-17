# ASAMM Audit Methodology

This directory contains the structured audit methodology introduced in ASAMM v0.2 and updated in ASAMM v0.5.
All documents are in English except `data-collection-prompt-v2-ru.md` (Russian).

## How to use

| Document | Purpose | Start here if... |
|---|---|---|
| `auditor-process.md` | Complete audit process: phases, tracks, gates, anti-patterns | Running any ASAMM audit |
| `agent-environment-profile.md` | Environment/profile classification: role, implementation, composition, tier, protocols, runtime composition | Scoping an audit before grading controls |
| `prompt-library.md` | Structured prompts for [OWNER], [SELF], [AUDITOR], [PRODUCT] | Conducting data collection |
| `environment-adapters.md` | Platform-specific verification commands (claude.ai, ChatGPT, Claude Code, local agents) | Auditing a specific AI environment |
| `protocol-checklist.md` | MCP / A2A / ACP protocol checklist mapped to ASAMM controls | Auditing protocol-mediated tools, delegation, or discovery |
| `runtime-composition-inventory.md` | AIBOM/runtime composition inventory for dynamic tools, context, identities, and delegated agents | Auditing dynamic or high-impact agentic workflows |
| `report-template.md` | Blank audit report with all mandatory sections | Writing an audit report |
| `comparative-audit-protocol.md` | Method-parity comparison of two environments | Evaluating two environments for the same workflow |
| `analysis-principles.md` | 11 principles derived from practice + environment checklists | Reviewing findings or designing an audit |
| `data-collection-prompt-v2-ru.md` | Self-audit data collection prompt (Russian) | Running a self-audit in Russian |

## Three audit tracks

**Track A — Self-audit:** the agent or development team audits its own environment.  
Advantage: direct access, more context.  
Risk: self-report ceiling — must complete Phase 4.5 external verification pass.

**Track B — Independent audit:** separate auditor without direct environment access.  
Advantage: no conflict of interest.  
Risk: lower evidence quality by default; requires active source contradiction management.

**Track C — Agent-as-code-auditor:** an agent reviews a codebase it did not build.  
Risk: authority inflation, recommendation laundering, contradiction suppression.  
Requirement: mission interview must complete before any code access.

## Critical ordering rule

**Phase 1 (Mission Interview) must complete before Phase 2 (Data Collection) begins.**  
This is a hard requirement, not a recommendation.  
An audit that starts with technical inventory cannot produce mission-centric findings
without re-interviewing the owner.

Phase 0 must also produce an Agent Environment Profile. The profile is not a
maturity score; it determines which controls, evidence prompts, and supplements
are mandatory for the audit scope.

## Evidence hierarchy

```
[empirical]          > [config]  > [inferred]  > [unknown]
[empirical absence]  — tested and NOT found; stronger than [unknown]
[not testable]       — cannot be verified in this scope; state why
```

Self-report is [inferred] by default. Controls cannot be graded above L1 on [inferred] evidence alone.
