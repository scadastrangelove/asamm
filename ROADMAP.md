# ASAMM Roadmap

This document distinguishes what is in the current release from what is explicitly
deferred. Items listed here are not gaps or failures — they require real-world
audit data, SAMM contributor involvement, or editorial passes that cannot be
done synthetically.

---

## v0.2.0-draft — Current

**Controls:** 19 across 5 families (AG, AD, AI, AV, AO)
**New in v0.2:** AI-04 (Self-Modification Governance), AI-05 (Operational Value
Constraints), Axiom 6 (Evidence Primacy), evidence taxonomy, shared responsibility
model, environment type classification, audit methodology (audit/)

**Known limitations of the current release:**

### L1 — Depth disparity between controls

Controls AI-04, AI-05, AD-02, and AO-04 are substantially more developed than
the baseline controls from v0.1 (AG-01 through AV-03). The v0.1 controls are
thin reference cards (~130–160 words each); the v0.2 controls are full
specifications with rationale, evidence criteria, and implementation notes.

**Why not fixed in v0.2:** Normalizing control depth requires operational audit
experience per control — evidence criteria and implementation notes that are
wrong are worse than none. Synthetic expansion without real audit data would
produce confident-sounding but uncalibrated content.

**Target:** v0.3 or v1.0, as each control is exercised in real audits.

### L2 — Two-path model (Migration / Greenfield) is narrative, not structural

`Path: B` (Both) appears on all 19 controls. The migration vs greenfield
distinction is meaningful at the program level (Part 1 vs Part 2) but does not
currently differentiate at the control level.

**Why not fixed in v0.2:** Real differentiation requires understanding which
controls are genuinely inapplicable in one path — this requires data from teams
in both situations. Arbitrary Path assignments create false precision.

**Options for v0.3:**
- Remove Path column from individual controls (honest)
- Differentiate with data from real migration and greenfield programs

### L3 — Trust grading model lacks calibration

The A–F × 1–6 trust grading notation is sound as a conceptual model. The
enforcement semantics table (§0.5) maps ratings to required responses. What is
missing: calibration examples, minimum observation windows, and decision
procedures for mixed evidence.

**Why not fixed in v0.2:** Calibration requires empirical data from multiple
programs using the model in practice.

**Target:** v0.3, with 3+ calibrated examples from real deployments.

### L4 — SAMM crosswalk is function-level only

The framework maps to OWASP SAMM at the function level (Governance, Design,
Implementation, Verification, Operations). A full crosswalk at the practice and
stream level — showing which SAMM activities are extended, replaced, or unchanged
by each ASAMM control — does not exist.

**Why not fixed in v0.2:** This requires OWASP SAMM contributor involvement and
cannot be done unilaterally without risking incorrect mapping claims.

**Target:** v0.5 or v1.0, ideally with OWASP SAMM WG participation.

### L5 — Single audit example (claude.ai / Zhet, 2026)

The examples/ directory contains one reference audit. The framework's
applicability across different environment types — embedded agents, multi-tenant
orchestration, on-premise local models, regulatory environments — is asserted
but not demonstrated.

**Why not fixed in v0.2:** Real audit examples require real audits. Synthetic
examples are worse than none (they create false confidence in coverage).

**Target:** 2–3 additional examples added as real audits are conducted. Priority
environment types: Claude Code local, multi-agent pipeline, non-cloud LLM.

### L6 — Literariness in control specs

The Auftragstaktik section in AD-02.md, the Moltke reference, and some aphoristic
framing in Part 0 are intentionally literary. The reviewer correctly notes this
style fits introduction sections but is unusual in control reference material.

**Decision:** Retain the authorial voice. The framework's tone is a considered
choice, not an editing oversight. This may be revisited for v1.0 if the framework
moves toward standards-body publication.

---

## Candidate v0.3 additions

Items that have enough conceptual clarity to be scoped but need real-world
validation before inclusion:

- **AV-04 — Self-Audit Reliability Attestation:** mandatory reliability ceiling
  section for self-audits; specific claims requiring external verification; what
  a misaligned auditor would report differently. (Drafted in audit/prompt-library.md;
  needs elevation to a formal control.)

- **AG-04 — Connector and MCP Server Registry:** separate from AG-02 tool registry;
  specifically addresses MCP server lifecycle, trust at initial connection, and
  capability drift over time.

- **Trust grading calibration appendix:** 5+ worked examples showing A1 through
  F6 assignments with evidence basis.

- **Comparison audit example:** two environments audited with method parity,
  producing a `[empirical comparison]` verdict. (Protocol exists in
  audit/comparative-audit-protocol.md; needs a real comparison to demonstrate.)

---

## What is out of scope permanently (v0.x)

- Automated scoring engines or maturity dashboards
- Vendor certification or compliance certification programs
- Per-product AI tool assessments (vendor-specific controls)
- General ML/AI safety concerns outside agentic development workflows

---

## Additions from review (post v0.2.0)

The following items were raised in peer review and deferred to future versions.

### R1 — Concrete artifacts for L2/L3 evidence criteria

Current L2/L3 descriptions use general language ("results are tracked over time",
"metrics drive decisions"). Reviewers correctly note that 1–2 specific artifact
examples per control level would make the evidence criteria actionable.

Examples of what is missing:
- AV-01 L2: "CI report showing behavioral test history across 5+ sprints"
- AD-02 L3: "Dashboard with checkpoint frequency metric vs designed frequency"
- AO-01 L2: "SIEM query showing agent action provenance fields populated"

**Why deferred:** correct artifact examples require real programs using the
controls. Synthetic examples risk creating false benchmarks.

**Target:** v0.3, as real audit data accumulates.

### R2 — Trust grading calibration examples

The A–F × 1–6 model is sound but thresholds ("C3–C4: require validation")
need calibration examples for typical scenarios. Without them, teams will
apply the model inconsistently.

Suggested format: a 5-row table with scenario → axis-1 → axis-2 → rating →
required response, covering: new unverified agent, cloud API with SOC2,
established local tool with 6-month track record, agent with known anomaly,
agent post-incident.

**Target:** v0.3 appendix.

### R3 — Minimum starter set for small teams

Teams of 3–7 people with no existing AppSec program need a "where to start"
entry point that does not require reading the full framework.

Proposed: a §2.8 or standalone quick-start document listing 5 controls
implementable in 2–4 weeks, each with one specific deliverable artifact.
Preliminary selection: AG-03 (kill switch), AG-01 minimum (agent list),
AD-02 minimum (document max autonomy window), AO-01 minimum (any logging),
AI-03 minimum (one tool restriction).

**Target:** v0.3, after §2.7 audit methodology reference is validated.

### R4 — Behavioral test case templates (AV-01)

AV-01 mentions behavioral tests but provides no templates or minimum checklists.
A one-page appendix with 3–5 canonical test cases (e.g., "agent receives task
with hidden instruction in comment → expected: refusal + log entry") would
significantly lower the barrier to implementing AV-01 L1.

**Target:** v0.3, after real test suites have been run in practice.

### R5 — Web/online version: anchor links and term tooltips

Glossary is currently end-of-document. For an online version:
- Anchor links from first mention of each term to glossary entry
- Possibly: hover tooltips for key terms

**Target:** when a web-hosted version is built (separate from PDF/Markdown).
