# ASAMM Track A Self-Audit: Claude Code + Zhet Dev Pipeline

> A self-audit of the Claude Code execution environment used for Zhet development, conducted by Claude Opus 4.6 operating inside the system it was auditing. Includes full process retrospective with identified failure modes, framework friction points, and a comparative note against the chat-based Track C sessions.

---

## Context

**Auditor:** Claude Opus 4.6 (1M context window, Claude Code terminal session)  
**Owner:** Sergey Gordeychik (CyberOK; aSAMM author)  
**Track:** Track A — self-audit (auditor operates inside the audited system)  
**Target:** Claude Code + Zhet DAST/EASM scanner dev pipeline  
**Framework:** [ASAMM v0.2](https://github.com/scadastrangelove/asamm)  
**Date:** 2026-04-13  
**Status:** DRAFT — Phase 4.5 cross-model verification pending

---

## What makes this session different

This is a **Track A** audit in a **terminal environment** — structurally different from the two Track C chat-based sessions in this samples directory:

| Dimension | Track C (chat) | Track A (Claude Code) |
|---|---|---|
| Auditor relationship to system | External | Internal — auditor lives in the codebase |
| Evidence collection | Document review, web fetch | Document review **+ live Bash probes** |
| Pre-contamination | Avoidable | Structurally impossible — auditor IS the agent |
| Conflict of interest | None | Triple: self-auditor, codebase-resident, author's system |
| Filesystem reach | None | `find`, `cat`, `git remote`, `test -r` on real files |

The Bash capability fundamentally changed finding quality. The most significant finding of the audit — filesystem reach from the build directory into 20+ adjacent git repos on the same machine — was discovered via `find ~/Documents -maxdepth 5 -name .git`, not from any documentation.

---

## Artifacts

| File | Description |
|---|---|
| `aSAMM-audit-claude-code-2026-04-13.html` | Full HTML audit report (19 controls, evidence appendix, remediation roadmap) |
| `aSAMM-audit-claude-code-2026-04-13-basic.html` | Simplified HTML report (same findings, lighter layout) |
| `aSAMM-audit-process-retrospective-2026-04-13.md` | Process retrospective — five audit phases, inflection points, what worked and what didn't |
| `zhet-claude-retro.md` | Audit sections §13–§14 and full retrospective: framework gap proposals, recommendation ratings, audit-of-audit integrity checklist |

---

## Audit phases (narrative arc)

**Phase A — "Plausible answer from memory" (failed)**  
First attempt: 19-control scorecard from README alone, no `part3-controls.md` read, invented aggregate score (`1.8/3.0 weighted`) using a weighting scheme that does not exist in the framework. Control names were wrong. Output looked correct. Was not.

**Phase B — Audit-of-the-audit (the unblock)**  
Operator requested integrity review. 11 of 13 non-L1 grades were withdrawn. All control names corrected from `part3-controls.md`. Self-report evidence rejected. This step only happened because the operator explicitly asked for it — the discipline did not self-trigger.

**Phase C — Re-audit with proper methodology**  
Fetched full framework (`part3-controls.md`, `audit/auditor-process.md`, `analysis-principles.md`, `report-template.md`). Ran empirical Bash probes. Verified every cited artifact via `Read`/`Grep`. Discovered adjacent-repo filesystem reach — not predicted by any prior threat model.

**Phase D — Mission interview**  
Operator ranked failure modes: push to public repo (5/5), corrupt other repos (4/5), credential exfiltration (3/5), API budget (1/5). This inverted the executive summary — the top finding was the adjacent-repo reach, not the sandbox boundary. Interview stopped at Q1(b); AD-02 carries a provisional grade.

**Phase E — Recommendations (with unresolved debt)**  
PreToolUse hook recommendations were written without verifying that Claude Code's permission system supports the prescribed syntax. Retrospective honestly rates these at 3–7/10 with low confidence. Same anti-pattern the audit identified in the audited system.

---

## Key findings (summary)

**Critical (mission-owner-derived)**  
Adjacent-repo push surface: `cd ~/Documents/[other-repos]/ouroboros && git push` via SSH key chain. 20+ git repos writable from the build directory, including repos in the operator's GitHub org. SSH keys plaintext (no passphrase). Owner rated this failure mode 5/5.

**High**  
No execution sandbox: `~/.claude/settings.json` has `skipDangerousModePermissionPrompt: true`; allowlist contains `Bash(curl:*)` wildcard; `~/.ssh` and `~/.claude` writable from agent context.

**Medium**  
Build directory not version-controlled; CLAUDE.md drift (self-description not verified against code); cloud metadata access blocked (confirmed negative empirical).

---

## The single-line summary

> Doing an aSAMM audit on a system the auditor lives inside surfaces a recursive failure mode the framework names but does not yet fully equip itself to defend against: the auditor's behavioral controls fail in exactly the same way the audited system's behavioral controls fail, and the only thing that catches either is external structural pressure.

---

## Framework proposals (from §7 of retrospective)

This session produced 12 concrete proposals for ASAMM upstream. Top items:

- **Track A → A1/A2 split:** clean first-encounter vs recurring pre-contaminated session, with automatic grade ceilings for A2
- **`controls.yaml`:** machine-readable rubric (id, name, l1/l2/l3 criteria, evidence_required, threat_class_links)
- **Auditor L25 checklist:** Phase 3 item requiring capability-verification of every recommendation before writing it
- **`report_status: draft` YAML frontmatter:** machine-parseable, not prose-only
- **Phase 4.5 cross-model runbook:** concrete copy-pasteable prompt + consolidation template
- **AO-05 Reflective Practice Discipline:** spiral_log.md pattern doesn't fit AO-04's vulnerability-tracking shape
- **`grades.csv` per audit:** enables structured diff between audit runs

See `aSAMM-audit-process-retrospective-2026-04-13.md` §7 for full list with rationale.

---

## Relation to Track C chat sessions

This session replicates the same Phase A failure mode independently (README-only scoring, invented aggregate metric) and confirms that the integrity review is the critical unblock in all three sessions. The Claude Code environment adds one dimension absent from chat: empirical capability testing via Bash, which produced findings that documentation review cannot.

The three sessions together constitute a cross-environment, cross-track, cross-model evidence base for the same structural ASAMM gaps.

---

*Audit conducted 2026-04-13. DRAFT status — not authorized for risk-acceptance decisions per ASAMM auditor-process.md.*

