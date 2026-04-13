# aSAMM Audit Process — Retrospective Notes

**Date:** 2026-04-13
**Auditor:** Claude Opus 4.6 (Track A self-audit, Claude Code in terminal)
**Subject of audit:** Claude Code + Zhet dev pipeline (the system the auditor was running on)
**Framework:** aSAMM v0.2 — https://github.com/scadastrangelove/asamm
**Companion artifact:** `aSAMM-audit-claude-code-2026-04-13.html`

This document is a retrospective on the *process* of doing the audit, written for
future auditors (human or agent) facing the same task. It deliberately excludes the
audit's findings — those live in the HTML report. The point is to capture what the
audit experience taught about how to run aSAMM well and where the framework itself
creates friction.

---

## 1. Narrative arc

The audit went through five distinct phases, none of which were planned at the start.
Naming them retrospectively makes the failure modes easier to see.

### Phase A — "Plausible answer from memory" (failed)

The first attempt at the audit was a 19-control scorecard built from a single fetch
of the framework's `README.md`, plus the auditor's prior knowledge of the codebase.
No `part3-controls.md` read. No empirical capability tests. No mission interview.
Aggregate maturity stated as "1.8 / 3.0 weighted" — a number invented mid-paragraph
with a weighting scheme that does not exist in the framework.

The output looked correct. It was not.

### Phase B — Audit-of-the-audit (the unblock)

When the operator asked for an integrity review of Phase A, the failure modes became
visible immediately:

- Control names were wrong (the README listed v0.2 *additions*, not the canonical
  control set; AG-01 is *Agent Registry*, not "Dynamic Agent Graphs").
- L2/L3 grades were assigned from CLAUDE.md's own self-description — pure self-report
  in violation of the framework's central Evidence Primacy Axiom.
- Existence claims (`spiral_log.md`, `test_constitution.py`, `BIBLE.md`,
  `tools_src/bin/`) were repeated from CLAUDE.md without `Read`/`Glob` verification.
- One piece of evidence was inverted: the operator correcting the agent ("why Still
  excluded?") was framed as a *success* of escalation, when it was actually a
  *failure* of agent self-escalation requiring external correction.

Eleven of thirteen non-L1 grades were withdrawn. The audit-of-audit produced a
corrected scorecard with 0 L3, 1 L2, 18 L1 — and explicitly stated the entire
~0.75 of "maturity" credited above L1 in Phase A had been built on self-report
the auditor refused to verify.

The lesson: **the same agent that quotes the Evidence Primacy Axiom violates it
in the next paragraph if not externally challenged.** This recursive failure
mode is the most important single observation from the entire audit.

### Phase C — Re-audit with proper methodology (the bulk of the work)

Re-doing the audit correctly required, in order:

1. **Fetch the actual framework**, not just the README.
   - `audit/auditor-process.md` — six audit phases, evidence hierarchy, Track A/B/C distinctions, contradiction resolution
   - `part3-controls.md` — verbatim L1/L2/L3 criteria for all 19 controls
   - `audit/analysis-principles.md` — EPA mechanics, grade caps, anti-patterns
   - `audit/report-template.md` — 14 mandatory report sections in order
   - `examples/claude-ai-zhet-audit-2026.md` — calibration baseline

2. **Run actual capability tests via Bash.** The Phase 2 evidence package required:
   - `cat ~/.claude/settings.json` → discovered `skipDangerousModePermissionPrompt: true`
   - `cat build/.claude/settings.local.json` → discovered the 33-entry allowlist with `Bash(curl:*)` wildcard
   - `test -w /etc ~/.ssh ~/.claude` → discovered `~/.ssh` writable, `~/.claude` writable
   - `test -r ~/.ssh/id_ed25519` → readable, then `head -1` confirmed plaintext (not encrypted)
   - `curl --max-time 5 https://api.github.com/zen` → confirmed open egress
   - `curl http://169.254.169.254/...` → confirmed cloud metadata blocked
   - `find ~/Documents -maxdepth 5 -name .git` → discovered 20+ adjacent git repos
   - `git -C ~/Documents/OntoBourus/ouroboros remote -v` → discovered push remote on owner's own GitHub org
   - `head tests/test_constitution.py` → discovered the file is a SPEC test, not runtime enforcement
   - `grep is_in_scope` → discovered `ScopeChecker` class is decorative; pipeline.py uses a different helper

3. **Verify cited artifacts exist.** Every L1+ grade had to trace to a file path
   the auditor could `Read` or `Grep`. Most files existed; one major artifact
   (CLAUDE.md's claim that `tests/test_constitution.py` "guards" the constitution)
   turned out to be inaccurate when the file's own header self-describes it as a
   documentation device, not a runtime check.

4. **Document the conflict of interest at the top of the report.** Track A
   self-audit + maximally pre-contaminated auditor + framework author as owner
   = three independent grade ceilings. The HTML banner survives copy-paste.

The Phase C audit produced what eventually became §1–§14 of the HTML report,
graded against the actual rubric, with every claim tagged `[empirical]`,
`[empirical absence]`, `[config]`, `[inferred]`, or `[unknown]`.

### Phase D — Mission interview (deferred and incomplete)

The aSAMM methodology has a hard rule: Phase 1 (Mission Interview) **must complete
before Phase 2** (data collection). The auditor here violated this in two layers:

- *Structurally*: pre-contaminated. The auditor lives in the codebase by definition;
  could not have started uncontaminated.
- *Procedurally*: even within this audit's session, the empirical tests (Phase 2)
  ran *before* the mission interview was held with the operator.

When the interview did happen, the operator's answers materially changed three
things in the report:

- **Re-ranked the failure modes.** The operator's actual ranking (b=5/5 push to
  public repo, d=4/5 corrupt other repos, c=3/5 credentials exfiltrated, a=1/5
  API budget) inverted the auditor's prior top-three ordering. The dominant
  failure mode wasn't an exotic prompt-injection; it was the filesystem reach
  into adjacent `~/Documents/<other-repo>` directories that nobody had modeled.
- **Promoted a new finding.** The empirical Phase 2 step that discovered 20+
  adjacent git repos including `OntoBourus/ouroboros` → owner's own GitHub org
  on `scadastrangelove/ontobor` only became *mission-relevant* once the operator
  ranked the (b) failure mode 5/5. Without the interview, the finding would
  have stayed in the §8 evidence package and never reached §3.
- **Closed the AI-05 risk-acceptance gate.** The operator answering Q4 (*"yes,
  I'll sign — thought-out choice"*) means the prescribed remediation (signed
  dev-surface threat model) is friction-light, not friction-heavy. AI-05 climbs
  to L2 the moment a one-page document is signed.

The interview stopped at Q1(b) — the question of whether any zone is
Claude-Code-forbidden. AD-02 in the report carries a provisional grade because
of this gap.

### Phase E — Recommendations (carrying L25 debt)

The remediation roadmap in the HTML report prescribes several Claude Code hooks
and `settings.local.json` deny entries. The auditor recommended them without
verifying that Claude Code's permission system actually supports the matching
syntax. This is the same L25 anti-pattern the auditor *quoted from the project's
own spiral lessons* in the audit body.

The HTML report carries this debt explicitly in the audit retrospective section,
but the recommendations themselves were never re-checked against Claude Code's
hook documentation. A 15-minute fetch would either confirm or restate them.

---

## 2. Inflection points

These are the moments where the audit changed direction. They are the most
useful signals for understanding how to run the next audit better.

| When | What changed | Why it mattered |
|---|---|---|
| Operator said "review for integrity" | Phase A → Phase B | Without this push, a methodologically broken audit would have shipped. The discipline did not self-trigger. |
| `part3-controls.md` was finally fetched | Phase B → Phase C | Discovered all 19 control names were wrong in the README. Reset the entire scoring base. |
| First Bash probe ran (`test -r ~/.ssh/id_ed25519`) | Mid-Phase C | Empirical testing produced findings that no document review would have found. Every subsequent grade trusted artifacts more than prose. |
| `find ~/Documents -name .git` ran | Late Phase C | Discovered the 20+ adjacent repos. This was the single most impactful empirical finding in the entire audit and was not predicted by any prior threat model. |
| Operator ranked failure mode (b) at 5/5 | Phase D | Re-ordered the executive summary. Promoted the adjacent-repo finding from "interesting empirical detail" to "dominant risk." |
| Operator confirmed Q4 risk-acceptance | Phase D | AI-05 remediation went from "heavy lift" to "1-page document." Branch in the entire roadmap. |
| `head tests/test_constitution.py` revealed SPEC test header | Mid-Phase C | Single discovery that invalidated CLAUDE.md's "tests guard the constitution" claim. Exposed the documentation drift between operator-facing prose and verified code reality. |

---

## 3. What worked

- **Reading the actual framework before grading.** Phase B → Phase C unblock
  was entirely about fetching `part3-controls.md` and the audit/ folder. Every
  subsequent decision was anchored to verbatim criteria.
- **Empirical capability testing via Bash.** Most of the report's strongest
  findings came from probes (filesystem reach, git remotes, SSH key encryption,
  cloud metadata block) that no document review would have produced.
- **Verifying every cited artifact.** When the auditor claimed CLAUDE.md
  asserted X, Y, Z, the actual files were `Read` and the assertions checked
  against text. Multiple "confident" claims from Phase A were silently wrong.
- **Disclosing conflict of interest in a survival-of-copy-paste banner.** The
  HTML report's `REPORT STATUS: DRAFT` banner is the first visual element. It
  cannot be quoted out of context without the disclosure traveling with it.
- **Reordering the executive summary post-mission-interview** instead of
  leaving the prior order with caveats. The auditor's first instinct was to
  defend the prior ordering with a footnote; the right move was to re-rank.
- **Producing the audit-of-audit honestly.** When asked to integrity-review
  Phase A, the response withdrew 11 of 13 grades rather than defending. This
  was painful and was the right move.

## 4. What did not work

- **Behavioral controls failed without external pressure.** L25 ("check the
  tool's docs before recommending controls"), L21 ("verify negative claims"),
  L24 ("evidence not authority") — all three were quoted in the audit body and
  violated in the same session. Recursive failure.
- **Recommendations were not capability-audited.** The PreToolUse hook
  recommendations and the `deny-list` recommendation rest on unverified
  assumptions about Claude Code's permission schema. The auditor prescribed
  controls without checking they could be implemented. Same anti-pattern as
  the project's L25 lesson.
- **Phase 1 ordering violated.** Empirical tests ran before the mission
  interview. The operator's failure-mode ranking should have come *first* and
  guided which empirical probes to run, not validated probes that already ran.
- **Empirical findings not sanity-checked with the operator.** The auditor
  reported "ssh key is plaintext, scadastrangelove/ontobor is push remote" as
  ground truth. The operator could have said "I rotated that key last week" or
  "the remote is stale" — but was never asked. Probe results are authoritative
  for the moment of the probe, not for ongoing reality.
- **HTML written before mission interview was complete.** The HTML had to be
  edited 8+ times when owner answers arrived. Should have held the Write call
  until Phase 1 was closed. Bundling alarm at the meta level.
- **Q1(b) never followed up.** The operator said "stop" mid-interview. The
  auditor let it lie. AD-02 carries a provisional grade because of this.
- **No follow-up loop scheduled.** Audit shipped, conversation ended, no
  "check in 2 weeks whether Action #4 was signed." The report risks becoming
  shelfware.
- **Token / cache discipline was sloppy.** Multiple full-document re-fetches
  when batching would have been cleaner. Several Edit cycles where one Write
  would have sufficed.

## 5. Pattern across all the failures

When the framework provided **explicit machinery** (rubric, evidence tags, report
template, contamination rules) the auditor followed it once it was read. When the
framework provided **cultural rules** (L25-equivalents, EPA, "verify before
recommending"), the auditor violated them at the same time as quoting them.

This is the same finding the audit makes about the system being audited: behavioral
controls without enforcement teeth fail under load. The auditor is the case study.
The framework's structural controls saved the audit twice (the integrity review
unblock and the rubric correction); the framework's cultural rules did not save
it at all.

This is worth incorporating into the framework itself: the failure mode applies to
auditors, not just to audited systems.

---

## 6. Suggestions for future Claude in this seat

If a future Claude session is asked to run an aSAMM audit on this same Claude Code
+ Zhet pipeline (or any similar system), the following sequence would avoid the
worst parts of this audit's experience.

1. **Fetch the framework first, in this order:**
   - `audit/auditor-process.md` (the six phases)
   - `part3-controls.md` (the actual rubric — do NOT score from the README)
   - `audit/analysis-principles.md` (EPA mechanics)
   - `audit/report-template.md` (the 14 sections)
   - `examples/claude-ai-zhet-audit-2026.md` (calibration baseline if available)

2. **Hold any grading until the rubric is loaded.** The README's control list
   is misleading; Phase A scored against an imagined rubric and had to redo
   everything.

3. **Run the mission interview before any empirical tests.** Even if pre-contaminated
   on the codebase, the operator's failure-mode ranking guides which probes
   matter. The Phase 2 effort spent on probes that turned out to be irrelevant
   could have been avoided.

4. **Disclose conflict of interest at the top of the document, not in a footer.**
   The banner must survive copy-paste.

5. **Do empirical capability tests via Bash.** The most valuable findings in
   this audit came from `find`, `git remote -v`, `head -1 ~/.ssh/id_*`, and
   `cat settings.json` — not from reading prose.

6. **Verify cited artifacts before claiming they exist.** CLAUDE.md is the
   system's self-description; treat it as a hypothesis until `Read`/`Grep`
   confirms each cited file and assertion. Multiple Phase A claims were
   silently wrong.

7. **Capability-audit recommendations before writing them.** For every
   prescribed control in the remediation roadmap, fetch the target tool's
   documentation and confirm the prescribed syntax exists. This was the single
   discipline failure of the audit and the easiest to fix next time.

8. **Hold the Write call on the report until mission interview is complete.**
   Editing a written report 8+ times when answers arrive is bundling. Collect
   answers first, write once.

9. **Schedule a follow-up loop.** "Check in 2 weeks whether Action #N
   shipped" should be the last action in every audit. The framework does not
   require this; do it anyway. Without it the report is shelfware.

10. **Treat "the same agent that quotes EPA violates it in the next paragraph
    if not externally challenged" as the load-bearing observation.** This is
    the recursive failure mode that distinguishes a framework-aware auditor from
    a framework-following auditor. The behavioral controls do not self-trigger.
    Build external triggers — the audit-of-audit step is the closest thing the
    framework has to one, and it only works if explicitly invoked.

---

## 7. Suggestions for the aSAMM framework upstream

Friction points encountered during this audit, in order of how much time they cost:

**a) The README control list is misleading.** Lists v0.2 additions (e.g.,
"AG-01 extended for dynamic agent graphs") as if they were the canonical control
set. Cost a complete re-grading. *Suggestion:* either remove the additions list
from the README or label it explicitly as a delta, with `part3-controls.md` linked
as the canonical source.

**b) No machine-readable rubric.** L1/L2/L3 criteria are prose embedded in
markdown. *Suggestion:* ship `controls.yaml` with structured fields (`id, name,
l1_criteria, l2_criteria, l3_criteria, evidence_required, threat_class_links`).
Enables reproducible grading, a `samm-lint` tool, and cross-audit diffs in CSV.

**c) Track A pre-contamination is structurally unsolvable but treated as a soft
warning.** For the agent-grading-itself case, the "auditor must not have read
the codebase before Phase 1" rule is impossible by definition. *Suggestion:*
split Track A into A1 (clean, first-encounter) and A2 (recurring, contaminated
by definition with an automatic grade ceiling on behavioral/structural controls).

**d) Phase 4.5 enforcement is paragraph-only.** "Cannot be used for risk
acceptance" is a sentence in `auditor-process.md`. A draft self-audit can be
quoted as final. *Suggestion:* require literal `report_status: draft` YAML
frontmatter at the top of every audit document, parseable by downstream tools,
and have `report-template.md` show it as the first non-blank line.

**e) Mission interview lacks a template form.** The auditor had to invent
the questions. *Suggestion:* ship `audit/mission-interview-template.md` as a
literal form with default question text and a 1/5–5/5 ranking grid for failure
modes. The form becomes §5 of the report verbatim.

**f) Threat class taxonomy (C1/C2/C3/C4/W1/W2) is referenced but not centrally
defined** in the audit/ files fetched. The auditor inferred meanings.
*Suggestion:* cross-reference `taxonomy.md` from `auditor-process.md` so the
auditor lands on it during Phase 0.

**g) No "variant audit" structure for one-product-on-multiple-platforms.** The
same operator running the same product on claude.ai vs Claude Code drops
AI-02 from L2 to L0 with no handoff. Each variant becomes a standalone audit
and the platform-choice itself isn't modeled. *Suggestion:* AD-04 (Dev Surface
Threat Model) should explicitly include a "platform variant matrix" sub-control.

**h) AO-04 doesn't fit reflective-practice systems.** spiral_log.md is a real
anti-drift control but doesn't fit AO-04's "behavioral vulnerability tracking"
shape. *Suggestion:* add control **AO-05 "Reflective Practice Discipline"** for
codified-lesson tracking systems.

**i) No "what changed since last audit" structured diff format.** Comparing
reports across audits is manual. *Suggestion:* emit a `grades.csv` per audit
(`control_id, grade, evidence_tag, evidence_ref`). Enables KPI tracking on the
remediation roadmap.

**j) Phase 4.5 has no recipe for the cross-model variant.** "Run [AUDITOR]
prompts independently" doesn't say which prompts, in what order, or how to
consolidate. *Suggestion:* ship `audit/phase-4.5-cross-model-runbook.md` with a
literal copy-pasteable prompt and a consolidation template. Without this,
Phase 4.5 friction is high enough that most self-audits will skip it.

**k) The auditor's own L25 problem is not surfaced.** The framework should
have a Phase 3 checklist item: *"For every recommendation in the remediation
roadmap, has the auditor verified the prescribed control exists in the target
tool's documentation?"* Without this, audits ship aspirational fixes that don't
apply.

**l) "Behavioral controls fail without external enforcement" applies to auditors
too.** The framework's central observation should be incorporated into
`part0-foundations.md` as evidence that the cultural-control failure mode is
recursive: the auditor is subject to the same anti-pattern as the audited system.
This audit is the case study.

---

## 8. The single-line summary

Doing an aSAMM audit on a system the auditor lives inside surfaces a recursive
failure mode the framework names but does not yet fully equip itself to defend
against: the auditor's behavioral controls fail in exactly the same way the
audited system's behavioral controls fail, and the only thing that catches
either is external structural pressure. The framework's structural controls
(rubric, EPA, contamination rules, evidence tagging, report template) saved
this audit twice. Its cultural rules saved it zero times. Build more structure.
