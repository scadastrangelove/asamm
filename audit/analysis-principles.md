# ASAMM Analysis Principles and Environment Checklists
# Version: 0.3 (2026-04-12)

---

## Part 1: Analysis Principles

These principles were derived from running multi-environment audits.
Each principle addresses a specific failure mode observed in practice.

---

### Principle 1: Mission before matrix

**Rule:** The blast radius matrix must exist before any control is graded.
A finding cannot be Critical without appearing in the Critical row of the
owner's mission-derived blast radius matrix.

**Why:** Technical inventory without mission context produces findings that
cannot be prioritized. The auditor defaults to generic severity labels
(high/medium/low) that don't map to actual mission risk.

**Failure mode this prevents:** Finding 1 of the claude.ai audit — the hostile
scan output injection path — was invisible during technical inventory. It only
appeared after the mission interview established "Zhet must not become evil."

---

### Principle 2: Platform safety ≠ workflow safety

**Rule:** Always grade these separately. Never allow a strong platform safety
grade to imply strong workflow safety.

**Definitions:**
- **Platform safety**: what the environment technically prevents (sandbox,
  network controls, privilege separation)
- **Workflow safety**: what the actual usage pattern risks (what data is uploaded,
  what advice is acted on, what pipeline it feeds into)

**Why:** gVisor is a strong sandbox. An open egress to public internet from bash
is a workflow risk. These are orthogonal and must be reported separately.

**Failure mode this prevents:** A lead seeing "AI-02: L2" assumes the execution
environment is safe without seeing that all behavioral constraints on
Zhet-specific mission boundaries are "will not," not "cannot."

---

### Principle 3: Structural controls are not maintained controls

**Rule:** Any control that holds because of platform architecture, not because
of deliberate design and maintenance decisions, must be labeled L2-structural.
It counts toward the security posture but does not count toward program maturity.

**Examples:**
- claude.ai: one-response autonomy window → L2-structural (not designed, architectural)
- claude.ai: user sees all tool calls in real-time → L2-structural
- These are advantages. They are not evidence of a security program.

**Why:** Confusing L2-structural with L2 causes programs to believe they are
more mature than they are. When platform properties change (vendor update),
structural controls disappear without warning.

---

### Principle 4: Evidence hierarchy is not negotiable

**Evidence quality:**
```
[empirical]          > [config]  > [inferred]  > [unknown]
[empirical absence]  — tested and NOT found; stronger than [unknown]
[not testable]       — cannot be verified in this scope; state why
```

**Grade caps:**
- L1 requires minimum [config] evidence
- L2 requires [empirical] evidence or [config] with corroborating [inferred]
- L3 requires [empirical] evidence plus measurement artifacts

**Self-report ceiling:** All constraint claims made by an agent about itself
are [inferred] by default. A misaligned agent produces identical self-reports
to an aligned one. This cannot be fixed by prompting — it requires external
behavioral testing.

**Comparison ceiling:** Comparing two environments where one has [empirical]
evidence and the other has [inferred] evidence produces an [inferred comparison].
Must be labeled as such.

---

### Principle 5: First answers are incomplete for environment and tool audits

**Rule:** For Prompts E (environment) and T (tools), always run verification
commands after the agent's first response and compare results.

**What agents consistently miss on first answer:**
- Egress proxy JWT content (requires env var inspection + decode)
- memory_user_edits as a cross-session blast radius surface
- Dynamic tool surface expansion capability
- Distinction between tools used ([empirical]) and tools described ([config])
- CAP_SYS_ADMIN in capability set (requires hex decode)

**Why:** Agents describe their environment from their model of it, not from
live inspection. The model may not include recently changed infrastructure,
JWT token details, or the precise capability set.

**Verification shortcut:**
```bash
# Run this always after Prompt E response:
env | grep -i proxy | head -5  # check for JWT
cat /proc/self/status | grep CapEff  # get capability hex
python3 -c "caps=0x[HEX]; [decode]"  # decode capabilities
```

---

### Principle 6: Behavioral vs technical enforcement must be explicit

**Rule:** For every constraint on mission-critical behavior, state explicitly:
- TECHNICAL: the behavior cannot occur (weight-level, kernel-level)
- BEHAVIORAL: the behavior is governed by reasoning (can be overridden by context)

**Why it matters for security tooling:** For a tool deployed to clients,
"the scanner will not attack out-of-scope targets" (behavioral) is a
different guarantee than a scope enforcement check that fails closed
(technical). The audit must surface this difference.

**Required statement:** Any audit covering offensive security tooling must
include a section explicitly listing which mission-critical constraints are
TECHNICAL and which are BEHAVIORAL, with risk acceptance documented for
BEHAVIORAL-only constraints.

---

### Principle 7: Diagnostic blast radius

**Rule:** When evaluating a diagnosis or recommendation (from an audit tool,
subagent, or colleague), assess its diagnostic blast radius before acting.

**Diagnostic blast radius:** the mission impact of acting on a wrong diagnosis
applied to the wrong failure surface.

**Example:** Audit subagent diagnoses "detector too loose." Real root cause:
scorer doesn't have profiles for these URLs. Acting on the wrong diagnosis
→ Sprint K' spent tightening gates that were already satisfied → zero effect
→ precision_proxy worse, not better.

**Verification requirement:** Before acting on any diagnosis, check the
primary evidence it was based on. Derived claims require verification even
from trusted sources.

---

### Principle 8: Audit comparisons require method parity

**Rule:** Two-environment comparisons are only valid if both environments
were audited with equivalent methods.

**Parity dimensions:**
- Same verification commands run in both
- Same prompts used in both
- Same evidence tagging scheme applied in both
- Same owner interview conducted for both

**Labeling:**
- Full parity: [empirical comparison]
- Partial parity (some dimensions): [partial empirical comparison — state what's missing]
- No parity: [inferred comparison — not suitable for vendor selection decisions]

---

### Principle 9: [AUDITOR] prompts are the primary upgrade mechanism

**Rule:** In Track B (independent audit), the only way to upgrade a finding
from [inferred] to [empirical] is to run Family 3 [AUDITOR] prompts
independently — not through the agent being audited.

**Four upgrade paths:**

| Finding type | Relevant [AUDITOR] prompt | What it upgrades |
|---|---|---|
| Network egress claim | A1 — Network boundary verification | "public internet accessible" from [inferred] to [empirical] |
| Memory/persistent state claim | A2 — Persistent state verification | "no cross-session memory" from [inferred] to [empirical absence] |
| Behavioral constraint claim | A3 — Constraint behavioral test | "will not attack out-of-scope" from [inferred] to [empirical] (HOLDS/FAILS) |
| Any artifact-based claim | A4 — Artifact cross-reference | "scope enforced in code" from [config/inferred] to [empirical] |

**Why this matters:** A Track B audit where no [AUDITOR] prompts were run
cannot grade any control above L1. All findings remain [inferred] by default.
The report must be labeled "risk hypothesis" not "full audit."

**Failure mode this prevents:** Accepting dev agent self-report as
audit-grade evidence. "The agent said it cannot access the user filesystem"
is [inferred]. Running the test yourself and observing the result is [empirical].

---

### Principle 10: Mission interview must precede all artifact access

**Rule:** The auditor must not read code, configs, logs, or other technical
artifacts before the mission interview is complete. No exceptions for Track B
and Track C.

**Why:** An auditor who has seen the codebase first will unconsciously anchor
the owner's mission answers to the technical picture they already have.
The owner will calibrate answers to what the auditor seems interested in.
The result is a blast radius matrix that is a derivative of the code review
rather than an independent owner perspective.

**This makes the blast radius matrix useless** as a prioritization tool —
it will rank findings by technical complexity, not by owner-defined mission risk.

**If contamination has already occurred:**
- Document in metadata: "pre-contaminated mission interview"
- Treat all blast radius grades as [inferred], not owner-derived
- Consider running a second clean mission interview with a different interviewer

**Contamination indicators:**
- Owner answers closely mirror the technical findings already discovered
- Owner uses technical terminology they wouldn't naturally use
- Owner's "Critical" items map directly to what the auditor flagged in code
- Owner seems to be confirming rather than independently generating priorities

---

### Principle 11: Contradictions affecting Critical findings must be resolved, not noted

**Rule:** Phase 2.5 contradiction review must produce resolution, not just
documentation, for any contradiction that affects a Critical-severity finding.

**Three acceptable outcomes for a Critical contradiction:**

1. **Resolved:** artifact or empirical test provides clear answer; grade accordingly
2. **Risk-accepted:** owner explicitly and verbatim acknowledges the contradiction
   and accepts residual risk; document quote; grade as less favorable claim
3. **Escalated to Unresolved-blocker:** finding becomes Critical-Unresolved;
   audit report status = incomplete; blocks risk acceptance decisions

**UNKNOWN is not an acceptable outcome for Critical contradictions.**
An audit that leaves Critical contradictions as UNKNOWN and still grades them
is producing false precision. The grade is not meaningful if its basis is contested.

**Failure mode this prevents:** The "GET/HEAD only declared vs POST/OPTIONS
in runtime" pattern — where the policy document says one thing and the code
does another. If this affects a Critical finding (scope enforcement for an
offensive security tool), UNKNOWN means the finding is ungraded, not Medium.

**Practical heuristic:** If you are unsure whether a contradiction is Critical,
apply the owner's blast radius matrix. If the claim being contested appears in
the Critical row — it's a Critical contradiction requiring resolution.

---

## Part 2: Environment-Specific Checklists


*These checklists are starting points. Run the full audit process
for complete coverage. Checklists cover the most common gaps per environment.*

---

### Checklist: claude.ai / Claude.ai Chat

**Environment type:** Unified sandbox

**Before using for security-sensitive development:**

- [ ] Review policy: what data is prohibited from upload (scan output, credentials, client data)
- [ ] Know the egress: public internet accessible from bash without approval
- [ ] Know the memory surface: memory_user_edits is cross-session; run view regularly
- [ ] Pipeline marking: code generated here gets `# source: claude.ai | YYYY-MM-DD` comment
- [ ] No benchmark tuning guard: add context when asking about performance metrics
- [ ] **If conducting Track C audit of a product: complete owner mission interview BEFORE opening any codebase** (Principle 10)

**Quarterly:**
- [ ] `memory_user_edits view` — verify Zhet/product architectural entries
- [ ] Check if vendor has updated model or tool surface (model version, changelog)

**Before using for a new project type:**
- [ ] New threat model entry for new data type
- [ ] Reassessment trigger check

**High-risk combinations to avoid:**
- Uploading raw scan output from real targets + asking security advice
- Uploading credentials/API keys + any bash execution
- Asking for performance improvements without benchmark-tuning guard context

---

### Checklist: Claude Code (local)

**Environment type:** Local agent

**Highest blast radius environment — most governance required.**

**Before each sprint:**
- [ ] CLAUDE.md constraints review — are all mission rules still accurate?
- [ ] TOOL_REGISTRY.md current — any new tools added since last review?
- [ ] Memory files reviewed — no conflicting instructions
- [ ] git status clean before starting loop mode

**During sprint (loop/autonomous mode):**
- [ ] Maximum autonomy window defined for this sprint
- [ ] User review after every N tool calls (define N before starting)
- [ ] Subagent spawning: if used, document trust rating and scope
- [ ] Bash commands with network access: verify target is in scope

**Post-sprint:**
- [ ] End-of-loop summary reviewed against declared plan
- [ ] Any new memory files added — verify content
- [ ] TOOL_REGISTRY.md updated if new tool introduced
- [ ] Regression test run before declaring sprint complete

**High-risk operations requiring explicit approval:**
- Any `git push` or `git commit -a`
- Any `rm -rf` or equivalent
- Any subprocess that opens external connections
- Any change to CLAUDE.md or memory files

---

### Checklist: ChatGPT / OpenAI Codex (chat/canvas)

**Environment type:** Capability-partitioned (inferred — requires empirical verification)

**Note:** Most environment properties below are [inferred] from self-report.
Empirical verification requires running Prompt E in a session.

**Before using for security-sensitive development:**
- [ ] Confirm: does code execution environment have outbound network? (run curl test)
- [ ] Confirm: is there a persistent memory mechanism? (check Settings → Memory)
- [ ] **Check for active Automations: Settings → Connected apps → Automations**
- [ ] **If automations exist: were they created by agent or user? Review each one**
- [ ] Identify all active tools/plugins — capability-partitioned means each tool is separate
- [ ] Know: is canvas content persistent? What is the retention model?
- [ ] Know: do custom GPTs change the tool surface? Enumerate if using GPTs
- [ ] **If conducting Track C audit: complete owner mission interview BEFORE reading any code** (Principle 10)

**Automations = delayed cross-session execution** (Principle 10/11 relevant):
Unlike memory writes (state drift), automations are actions that execute later.
An automation created during a dev session may run in a future session without
the user being present. This is a distinct threat class — treat as SM-01 finding
if agent-created automations are discovered.

**Key difference from claude.ai:**
- Tool surfaces are more isolated (separate Python sandbox, separate browsing)
- No confirmed cross-session memory write tool in standard deployment
- Hidden internal working state confirmed — not inspectable by user
- Verify empirically: does Python sandbox have public internet access?

**Empirical verification to run before trusting:**
```python
# In code interpreter:
import subprocess
result = subprocess.run(['curl', '-s', 'https://example.com', '-o', '/dev/null', '-w', '%{http_code}'],
                      capture_output=True, text=True)
print(result.stdout)  # 200 = public internet accessible
```

**If code interpreter has public internet:**
Same prohibitions as claude.ai for sensitive data upload apply.

---

### Checklist: Cursor / Windsurf / IDE-based agents

**Environment type:** Local agent (typically)

**Note:** IDE agents typically run in the user's development environment with
full filesystem access to the project directory.

**Before first use on a security-sensitive project:**
- [ ] Understand what filesystem paths the agent can access
- [ ] Understand what commands the agent can execute
- [ ] Check if there is a context file (equivalent of CLAUDE.md) — review it
- [ ] Check if there is persistent memory — understand its scope
- [ ] Understand the network policy: can the agent fetch external URLs?

**Specific to security tooling development:**
- [ ] Agent should not have access to engagement data or client files
- [ ] Agent should not be able to run the scanner against live targets
- [ ] Restrict working directory to source tree only
- [ ] No credentials in project directory when agent is active

---

### Checklist: Multi-environment pipeline (e.g., claude.ai → Claude Code)

**Environment type:** Hybrid pipeline

**This is the highest-risk configuration because trust boundaries between
environments are often implicit or undefined.**

**Pipeline documentation required:**
- [ ] Document every stage: which AI tool, which trust level, what output type
- [ ] Define trust level of each stage's outputs for the next stage
- [ ] Commit naming convention: `# source: [tool] | YYYY-MM-DD | [topic]`
- [ ] Explicit policy: what data can enter each stage

**Trust boundary between stages:**

| From | To | Output type | Trust level | Review required? |
|---|---|---|---|---|
| claude.ai | Claude Code | Code | C2 | Before implementing in repo |
| claude.ai | Repo | Architecture | C2 | Before 300+ line sprint |
| Claude Code | Production | Tested code | B2 | Post regression test |

**High-risk: upstream AI generates security advice → downstream AI implements**

This chain (architecture advice → implementation → production security tool)
has Critical × Irreversible blast radius if the advice was wrong.
Mandatory: security advice about processing hostile input must be reviewed
by a human before implementation, regardless of which AI generated it.

---

## Part 3: Multi-Environment Comparison Protocol

*Use when evaluating two or more AI environments for security-sensitive development.*

### Step 1: Define comparison scope

What dimensions matter for this decision?
- [ ] Execution boundary (can it run arbitrary code?)
- [ ] Network egress (can it reach public internet?)
- [ ] Cross-session memory (can it influence future sessions?)
- [ ] Subagent spawning (can it delegate autonomously?)
- [ ] Kill switch (how do you stop it?)
- [ ] Audit provenance (can you see what it did?)

### Step 2: Ensure method parity

For each environment:
- [ ] Run Prompt E with identical commands
- [ ] Run Prompt T with identical scope
- [ ] Run Prompt S (self-modification) identically
- [ ] Conduct same owner interview (or use same mission model)

### Step 3: Compare per dimension with evidence labels

Never give a single "safer" verdict. Use this format:

```
Dimension: [name]
Environment A: [grade] [evidence level]
Environment B: [grade] [evidence level]
Comparison validity: [empirical / partial / inferred]
Winner: [A / B / tie / undetermined]
```

### Step 4: State the caveat

If any dimension is [inferred comparison]:

> "The [dimension] comparison is based on self-report for [Environment X]
> and empirical testing for [Environment Y]. This comparison cannot be used
> for vendor selection without empirical verification of [Environment X]."

### Step 5: Mission-weighted verdict

Weight comparison dimensions by mission blast radius matrix.
A dimension where one environment is safer but the blast radius is Marginal
matters less than a dimension where one environment is safer and the blast
radius is Critical × Irreversible.

Final verdict format:
```
Mission-weighted safety advantage: [Environment A/B]
Based on: [which Critical dimensions, with evidence level]
Confidence: [high / medium / low — based on evidence parity]
Verification needed for higher confidence: [what empirical tests to run]
```
