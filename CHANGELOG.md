# Changelog

All notable changes to Agentic SAMM are documented here.
Format: [version] — date — summary

---

## [0.2.0] — 2026-04-12

### Added

**Part 0 — Foundations:**
- Axiom 6: Evidence primacy (§0.2) — a claim about system state is a hypothesis until verified against primary evidence; authority of source does not substitute for evidence
- Evidence taxonomy (§0.6) — six evidence states: [empirical], [empirical absence], [config], [inferred], [not testable], [unknown]; grade caps per evidence level
- Cloud-hosted agent shared responsibility model (§0.7) — user-side / vendor-side / structural control classification; platform safety vs workflow safety must be graded separately
- Environment type classification (§0.8) — unified sandbox / capability-partitioned / local agent / hybrid pipeline; governance requirement per type
- Core concepts: Diagnostic blast radius, Platform safety vs workflow safety, Self-modification surface (§0.3)
- Attack pattern example: Self-modification via cross-session memory write (§0.4)
- Trust grading: subagent spawning note (spawned agents start at F6 regardless of parent trust rating) (§0.5)

**Part 3 — Controls:**
- AI-04: Agent Self-Modification Governance (new control) — enumerates and grades agent capability to write persistent context; L1/L2/L3 with evidence criteria; self-report ceiling explicit
- AI-05: Operational Value Constraint Mapping (new control) — "will not" vs "cannot" distinction; behavioral test protocol; formal risk acceptance requirement
- AG-01 extended: dynamic agent graph coverage — spawning capability classification; spawned agent trust and tool surface bounding
- AD-02 extended: blast radius must be derived from mission interview, not technical inventory alone; behavioral-only constraints must be inventoried
- AI-02 extended: platform safety vs workflow safety must be graded and reported separately

**Part 1 — Migration Path:**
- Added: self-modification surfaces to inventory (§1.2, Phase 0)
- Added: constraint verification tests to behavioral testing extension (§1.2)
- Added: self-modification logging as extension requirement for logging (§1.2)
- Added: false positive "the agent says it can't do that" (§1.3)
- Added: false positive "the AI tool has a strong sandbox" (§1.3)

**Part 2 — Greenfield Path:**
- Added: §2.7 Audit Methodology Reference — pointer to audit/ directory with quick-start table

**controls/AI-04.md:** full standalone control specification
**controls/AI-05.md:** full standalone control specification
**controls/README.md:** updated with new controls

**audit/ directory (new):**
- `auditor-process.md` — three audit tracks (A/B/C), phase gates, anti-patterns
- `prompt-library.md` — [OWNER], [SELF], [AUDITOR], [PRODUCT] prompt families
- `environment-adapters.md` — verification commands for claude.ai, ChatGPT, Claude Code, local agents
- `report-template.md` — blank audit report with all mandatory sections
- `comparative-audit-protocol.md` — method-parity comparison of two environments
- `analysis-principles.md` — 11 analysis principles + environment checklists
- `data-collection-prompt-v2-ru.md` — self-audit data collection prompt (Russian)

**examples/ directory (new):**
- `claude-ai-zhet-audit-2026.md` — full Track A self-audit: claude.ai as upstream dev environment for Zhet EASM+DAST scanner; 2026-04-12

### Changed

- `part0-foundations.md`: restructured; new sections §0.6–§0.8 added
- `part3-controls.md`: 17 → 19 controls; evidence taxonomy integrated into §3.1
- `part1-migration.md`: §1.2 expanded; §1.3 two new false positives added
- `part2-greenfield.md`: §2.7 added

---

## [0.1.0] — 2026-03-01

Initial release.

17 controls across 5 SAMM function families:
- AG: AG-01, AG-02, AG-03
- AD: AD-01, AD-02, AD-03, AD-04
- AI: AI-01, AI-02, AI-03
- AV: AV-01, AV-02, AV-03
- AO: AO-01, AO-02, AO-03, AO-04

Core documents: part0-foundations.md, part1-migration.md, part2-greenfield.md, part3-controls.md, taxonomy.md
