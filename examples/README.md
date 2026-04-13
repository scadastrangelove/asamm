# ASAMM Audit Examples

This directory contains real-world audit reports produced using the ASAMM framework.
Examples serve as reference implementations for auditors, not as templates to copy verbatim —
every system's mission model, threat surface, and control posture is different.

## Examples

| File | System | Date | Track | Key finding |
|---|---|---|---|---|
| `claude-ai-zhet-audit-2026.md` | claude.ai as upstream dev environment for Zhet EASM+DAST scanner | 2026-04-12 | Track A (self-audit) | 9/15 controls at L0; hostile scan output injection path unmodeled before audit; all Zhet operational constraints behavioral-only |

## What to learn from examples

Each example shows:
- How the mission interview produces a blast radius matrix that drives finding prioritization
- How empirical evidence (bash commands, network tests, environment introspection) differs from self-report
- How the shared responsibility model splits vendor-side from user-side controls
- How the Section D audit reliability ceiling is documented for self-audits
- How framework gaps discovered during the audit are formatted for filing as issues
