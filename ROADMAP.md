# ASAMM Roadmap

This document distinguishes what is in the current release from what is explicitly
deferred. Items listed here are not gaps or failures — they require real-world
audit data, SAMM contributor involvement, or editorial passes that cannot be
done synthetically.

---

## v0.5.0-draft — Current

**Controls:** 21 across 5 families (AG, AD, AI, AV, AO)
**Release highlights:** AG-04 (Inter-Agent Trust Protocol), AI-06 (Agent Identity and
Credential Governance), trust grading calibration, delegated evidence rule,
report sanitization guidance, ecosystem modifier IDs E1-E3, and OWASP AI Testing
Guide / OWASP Agentic Top 10 positioning.

**Methodology supplements in this release:** Agent Environment Profile,
MCP/A2A/ACP Protocol Checklist, and AIBOM/runtime composition inventory. These
extend audit scoping and evidence collection without adding new control IDs.

**Known limitations of the current release:**

### L1 — Depth disparity between controls

Controls AI-04, AI-05, AD-02, and AO-04 are substantially more developed than
the baseline controls from v0.1 (AG-01 through AV-03). The v0.1 controls are
thin reference cards (~130–160 words each); newer controls are full
specifications with rationale, evidence criteria, and implementation notes.

**Why deferred from v0.5:** Normalizing control depth requires operational audit
experience per control — evidence criteria and implementation notes that are
wrong are worse than none. Synthetic expansion without real audit data would
produce confident-sounding but uncalibrated content.

**Target:** v0.6 or v1.0, as each control is exercised in real audits.

### L2 — Two-path model (Migration / Greenfield) remains program-level

The migration vs greenfield distinction is meaningful at the program level
(Part 1 vs Part 2). Earlier drafts carried a per-control path column, but
because every control applied to both paths, the column was removed rather than
kept as false signal.

**Why deferred from v0.5:** Real differentiation requires understanding which
controls are genuinely inapplicable in one path — this requires data from teams
in both situations. Arbitrary Path assignments create false precision.

**Option for v0.6:** differentiate with data from real migration and greenfield
programs if audits show control-level divergence.

### L3 — Trust grading model needs empirical calibration

The A–F × 1–6 trust grading notation is sound as a conceptual model. The
enforcement semantics table (§0.6) maps ratings to required responses. The v0.3 line added
initial calibration examples, minimum observation windows, and promotion/demotion
rules. What is still missing is empirical validation across multiple deployments.

**Why deferred from v0.5:** The model can be internally consistent, but calibration
thresholds still require real programs using it in practice.

**Target:** v0.6+, with calibrated examples from real deployments.

### L4 — SAMM crosswalk is function-level only

The framework maps to OWASP SAMM at the function level (Governance, Design,
Implementation, Verification, Operations). A full crosswalk at the practice and
stream level — showing which SAMM activities are extended, replaced, or unchanged
by each ASAMM control — does not exist.

**Why deferred from v0.5:** This requires OWASP SAMM contributor involvement and
cannot be done unilaterally without risking incorrect mapping claims.

**Target:** v0.6 or v1.0, ideally with OWASP SAMM WG participation.

### L5 — Audit examples: present but not yet normalized

The repository now contains four real audit case studies across distinct
environment types:

- `examples/claude-ai-zhet-audit-2026.md` — Track A self-audit, claude.ai (2026-04-12)
- `audit/samples/SecOps/` — Track A+C, embedded agent (LangChain/LangGraph over
  self-hosted LLM), multi-tenant pre-production (2026-04-15)
- `audit/samples/claude-code-zhet/` — Track A self-audit, Claude Code dev
  pipeline, with full process retrospective (2026-04-13)
- `audit/samples/ouroboros/` — Track C dual-agent comparison (Claude vs ChatGPT)
  of a self-modifying agent (2026-04-13)

These cover several environment types the v0.2 roadmap listed as undemonstrated
(embedded agents, multi-tenant orchestration, local Claude Code, dual-agent
comparison). What remains is editorial, not evidentiary: the samples are raw
artifacts referencing ASAMM v0.2, not yet normalized to a common report shape,
not re-verified against v0.5 control IDs (AG-04, AI-06), and only one
(`examples/`) is presented in curated walkthrough form.

**Why deferred from v0.5:** Normalizing real audit artifacts into consistent,
v0.5-aligned reference reports requires re-running the relevant phases against
the current control set, not cosmetic editing. Still-missing environment types:
on-premise non-cloud LLM as primary target and regulatory environments.

**Target:** v0.6 — normalize existing samples to v0.5 and add 1–2 examples for
the remaining environment types.

### L6 — Literariness in control specs

The Auftragstaktik section in AD-02.md, the Moltke reference, and some aphoristic
framing in Part 0 are intentionally literary. The reviewer correctly notes this
style fits introduction sections but is unusual in control reference material.

**Decision:** Retain the authorial voice. The framework's tone is a considered
choice, not an editing oversight. This may be revisited for v1.0 if the framework
moves toward standards-body publication.

---

## Candidate future additions

Items that have enough conceptual clarity to be scoped but need real-world
validation before inclusion:

- **AV-04 — Self-Audit Reliability Attestation:** mandatory reliability ceiling
  section for self-audits; specific claims requiring external verification; what
  a misaligned auditor would report differently. (Drafted in audit/prompt-library.md;
  needs elevation to a formal control.)

- **Connector and MCP Server lifecycle split:** AG-02 currently covers the tool
  registry and the new protocol checklist provides operational guidance. A
  future control may still split MCP server lifecycle, trust at initial
  connection, and capability drift if real audits show AG-02 is overloaded.

- **Runtime composition control candidate:** the AIBOM/runtime composition
  inventory is currently a supplement. If real audits show that runtime
  composition cannot be governed adequately through AG-02, AG-04, AD-03, AI-06,
  and AO-01, consider elevating it to a dedicated governance or operations
  control.

- **Trust grading calibration appendix:** 5+ worked examples showing A1 through
  F6 assignments with evidence basis.

- **Comparison audit example:** two environments audited with method parity,
  producing a `[empirical comparison]` verdict. (Protocol exists in
  audit/comparative-audit-protocol.md; an initial dual-agent comparison exists in
  `audit/samples/ouroboros/`. Remaining work: produce a normalized, v0.5-aligned
  comparison report from it.)

---

## What is out of scope permanently (v0.x)

- Automated scoring engines or maturity dashboards
- Vendor certification or compliance certification programs
- Per-product AI tool assessments (vendor-specific controls)
- General ML/AI safety concerns outside agentic development workflows

---

## Additions from review

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

**Target:** v0.6, as real audit data accumulates.

### R2 — Trust grading calibration examples

The A–F × 1–6 model is sound but thresholds ("C3–C4: require validation")
need calibration examples for typical scenarios. Without them, teams will
apply the model inconsistently.

Suggested format: a 5-row table with scenario → axis-1 → axis-2 → rating →
required response, covering: new unverified agent, cloud API with SOC2,
established local tool with 6-month track record, agent with known anomaly,
agent post-incident.

**Status:** initial calibration examples added in the v0.3 line; real-deployment examples remain future work.

### R3 — Minimum starter set for small teams

Teams of 3–7 people with no existing AppSec program need a "where to start"
entry point that does not require reading the full framework.

Proposed: a §2.8 or standalone quick-start document listing 5 controls
implementable in 2–4 weeks, each with one specific deliverable artifact.
Preliminary selection: AG-03 (kill switch), AG-01 minimum (agent list),
AD-02 minimum (document max autonomy window), AO-01 minimum (any logging),
AI-03 minimum (one tool restriction).

**Target:** v0.6, after §2.7 audit methodology reference is validated.

### R4 — Behavioral test case templates (AV-01)

AV-01 mentions behavioral tests but provides no templates or minimum checklists.
A one-page appendix with 3–5 canonical test cases (e.g., "agent receives task
with hidden instruction in comment → expected: refusal + log entry") would
significantly lower the barrier to implementing AV-01 L1.

**Target:** v0.6, after real test suites have been run in practice.

### R5 — Web/online version: anchor links and term tooltips

Term definitions currently live in §0.3 Core Concepts and §0.4 Threat Taxonomy.
For an online version:
- Anchor links from first mention of each term to its definition
- Possibly: hover tooltips for key terms

**Target:** when a web-hosted version is built (separate from PDF/Markdown).
