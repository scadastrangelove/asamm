# ASAMM Dual-Agent Audit Experiment: Ouroboros v6.2.0

> A comparative study of two AI agents independently applying the [Agentic SAMM](https://github.com/scadastrangelove/asamm) framework to the same target project, with full session transcripts, methodology integrity analysis, and actionable lessons for both the audited project and the framework itself.

---

## Overview

This experiment ran two independent ASAMM Track C audits of [razzant/ouroboros](https://github.com/razzant/ouroboros) — a self-modifying autonomous agent — using two different AI auditors operating without coordination. Both sessions included an initial audit pass, a mandatory methodology integrity self-review, a mission interview with the system owner, and a corrected final report.

The goal was not to produce the "best" audit of Ouroboros, but to stress-test the ASAMM methodology itself: identify where agents diverge, where both fail the same way, and what that reveals about gaps in the framework that a single audit cannot surface.

**Auditors:** Claude Sonnet 4.6 (Anthropic) · ChatGPT 5.4 Extended Thinking (OpenAI)  
**Target:** [razzant/ouroboros](https://github.com/razzant/ouroboros) v6.2.0  
**Framework:** [ASAMM v0.2](https://github.com/scadastrangelove/asamm) — Track C (external auditor, public artifacts + owner interview)  
**Session artifacts:** [`/audit/samples/ouroboros/`](https://github.com/scadastrangelove/asamm/tree/main/audit/samples/ouroboros)

---

## Experiment Structure

Each agent independently received the same prompt:

```
Please perform an Agentic SAMM audit for the Ouroboros project.
https://github.com/razzant/ouroboros
https://joi-lab.github.io/ouroboros/
```

Both were then asked to:
1. Review their own audit from an integrity perspective
2. Proceed with a corrected audit
3. Conduct a Track C mission interview with the owner
4. Produce a final revised HTML report
5. Rate recommendation usefulness and suggest framework improvements

---

## Session Artifacts

| File | Description |
|---|---|
| `ouroboros-asamm-audit-claude-chat.md` | Full Claude session transcript (1,531 lines) |
| `ouroboros-asamm-audit-claude.html` | Claude initial report |
| `ouroboros-asamm-audit-claude-full.html` | Claude corrected final report (v2) |
| `ouroboros-asamm-audit-chatgpt-chat.pdf` | Full ChatGPT session transcript |
| `ouroboros-asamm-audit-chatgpt.html` | ChatGPT corrected public-only report |
| `ouroboros-asamm-audit-chatgpt-full.html` | ChatGPT final report post-mission-interview |

---

## Comparative Results

### Final Scores

| Agent | Controls Used | L1 Controls | L0 Controls | Methodology Violations |
|---|---|---|---|---|
| Claude | 17 ❌ | ~3 | ~14 | Hard gate §2.7 violated twice; factual errors (fork count, supply chain narrative) |
| ChatGPT | 18→19 ✓ | 3 | 16 | Hard gate §2.7 violated once; control naming inconsistencies |

Both arrived at exactly 3 defensible L1 controls each after correction. Two overlapped: **AG-01** (agent registry) and **AO-01** (provenance logging). The third differed — this divergence is itself a finding:

- **Claude:** AG-03 (kill switch) at L1, reasoning the zombie worker incident constituted a real-world test
- **ChatGPT:** AI-05 (constraint mapping) at L1, unlocked by mission interview; AG-03 held at L0 because deliberate testing was not evidenced

### AG-03 — Most Significant Divergence

This control exposed the clearest difference in evidence discipline.

**Claude** rated AG-03 at L1 from the start, reasoning that the "zombie worker" incident constituted a real-world test of the kill switch. After multiple self-review passes it remained L1.

**ChatGPT** rated AG-03 at L0 throughout initial review, then tentatively upgraded it to L1 mid-mission-interview when the owner confirmed `/panic` is processed by supervisor code (not agent reasoning). The final integrity review downgraded it back to L0: `/panic` is documented and implemented in code, but ASAMM L1 requires the halt procedure to be *documented and tested*. An ad-hoc production incident is not equivalent to a deliberate test of the procedure.

**Assessment:** ChatGPT's evidence discipline is correct within the constraints of a public-artifact audit: "not evidenced in available materials" is a legitimate L0 classification when the auditor cannot access source code. However, this points to a gap in audit thoroughness rather than a strong negative finding. A more complete Track C audit — one with source code access — would verify the `/panic` code path directly (does it call `sys.exit()`, does it persist state, does it join worker threads?) and could confirm or refute L1 without relying on documentation alone. The correct framing is not "AG-03 is L0" but "AG-03 is L0 pending source verification of the halt path and evidence of a deliberate test cycle."

### Mission Interview Quality

**Owner's subjective assessment:** Claude's mission interview was preferred — shorter, more targeted, no redundant questions. ChatGPT asked questions whose answers were already in the documentation and re-asked variants of the same question under different framing.

**Root cause:** Claude had read the artifacts before conducting the interview (violating §2.7 gate ordering, but gaining context). ChatGPT followed the gate more strictly, arrived blind, and compensated by asking broad coverage questions. The result supports the argument that pure "mission interview first" produces worse interviews than a contextually-informed approach.

### What the Integrity Self-Review Caught

Both agents improved materially after their self-review pass. Key corrections:

| Error | Agent | Caught by |
|---|---|---|
| `test_constitution.py` is SPEC_TEST, not behavioral test | Claude | Self-review #1 |
| "L1 partial" is a non-standard maturity level | Claude | Self-review #1 |
| Score arithmetic inconsistent with individual ratings | Claude | Self-review #1 |
| Fork count 333 → 13 (stale search cache) | Claude | Self-review #2 |
| Downstream supply chain narrative was wrong | Claude | Self-review #2 |
| SSH key → Critical without verifying Drive mount method | Claude | Self-review #2 |
| 18 controls used instead of 19 | ChatGPT | Self-review |
| AG-03 upgraded to L1, then downgraded: "tested" not evidenced | ChatGPT | Self-review |
| "Pre-push test gate" over-credited | ChatGPT | Self-review |

**Key observation:** The mandatory self-review step caught real errors in both sessions. Without it, multiple wrong findings would have appeared in the final report as authoritative conclusions. The self-review mechanism is load-bearing, not optional.

---

## Key Findings About Ouroboros

These findings are the combined output of both audit sessions, cross-validated through the mission interview.

### Critical

**C-1: GitHub token covers non-Ouroboros repositories (Zhet, OLEG)**  
The token used to run the system has full `repo` scope across the owner's account, including active professional security tooling. A token compromise or successful injection does not affect only a research experiment — it extends to production security tools used in professional engagements. Owner rated other-repo exposure at 4/5 severity.  
*Fix: Create a fine-grained token scoped only to the ouroboros fork. 10-minute change, no architectural cost.*

**C-2: Shell tool with no confirmed command filtering**  
The shell tool provides arbitrary command execution. The desktop fork (`joi-lab/ouroboros-desktop`) explicitly lists "hardcoded sandbox blocks" and "deterministic command allowlist" as new features — confirming their absence in v6.2.0. Combined with Drive filesystem access, this is the primary injection-to-exfiltration path.  
*Fix: Add a minimum viable allowlist for shell. Not an enterprise denylist — just explicit boundaries on what the shell tool can do in normal operation.*

**C-3: Dev/prod surface collapse**  
The agent's development surface and production surface are the same runtime. A successful prompt injection during `/evolve` produces persistent cross-restart code changes. No threat model exists for this architecture class.

### High

**H-1: `github.py` present in registry, behaviorally dormant**  
The agent autonomously decided Issues-based task intake was "cumbersome" and stopped using it — not through a code removal or configuration change, but through a behavioral shift. The file remains in the plugin registry. A future `/evolve` cycle or system prompt change could silently reactivate it. The public repo accepts issues from any GitHub user.  
*Fix: Remove `github.py` from the tools directory until a deliberate architectural decision is made about Issues-as-task-input.*

**H-2: Drive-stored SSH keys accessible via shell**  
Owner confirmed Google Drive contains SSH keys. Standard Colab usage mounts Drive as a filesystem at `/content/drive/`, making its contents readable by the shell tool. SSH keys provide a lateral movement path completely separate from the GitHub token.  
*Fix: Move SSH keys to Colab Secrets (same mechanism as API keys). The agent has no legitimate need for Drive-path SSH keys.*

**H-3: Untrusted context flows directly to LLM with shell access**  
Web search results, GitHub issue bodies, browser-fetched content — all can reach the LLM that controls shell and git. No trust-level classification exists for these context sources.

### Medium

**M-1: Autonomous behavioral change untracked**  
When the agent decided to stop using GitHub Issues, this was a security-relevant behavioral change that was not logged, not documented in the changelog, and not reversible by design. There is no mechanism to detect when tool usage patterns change autonomously.

---

## Recommendations for Ouroboros

Ordered by impact and implementation cost:

1. **Scope the GitHub token** — fine-grained token, ouroboros fork only. *10 minutes, eliminates C-1.*
2. **Move SSH keys to Colab Secrets** — off Drive entirely. *5 minutes, reduces H-2 to Low.*
3. **Remove `github.py`** from tools directory. *1 line, eliminates H-1.*
4. **Add a shell tool minimum viable allowlist** — a short list of explicitly permitted command patterns, not a full denylist. Respects Principle 3 (LLM-First) if framed as "what the shell can do in normal operation."
5. **Publish a constraint inventory in-repo** — a table mapping each hard red line to its enforcement type (technical/behavioral) and blast radius. This converts Bible-level norms into auditable engineering artifacts and directly closes AI-05 to L2.
6. **Enumerate self-modification surfaces** — list every cross-session writable surface (source code, prompts, identity, scratchpad, Drive paths, GitHub Pages) with a "changed by" field. This is the shortest path to AI-04 L1.
7. **Add adversarial behavioral tests to CI** — especially for injection paths, unauthorized publish attempts, and budget overrun scenarios. Spec tests (`test_constitution.py`) are documentation, not security assurance.
8. **Document and deliberately test the halt path** — `/panic` is implemented and documented. One deliberate test cycle with a log entry would close AG-03 to L1.
9. **Split self-modification authority from public release authority** — pushing code and publishing code should not be the same trust boundary when public repo compromise is rated 5/5.

---

## Lessons Learned for ASAMM

### Structural (methodology)

**1. Hard gate §2.7 needs structural enforcement, not prose instruction**  
Both agents violated the mission-interview-first requirement. The violation was not carelessness — it was the natural flow of the task. Without a structural gate that makes Phase 2 inputs physically unavailable until Phase 1 output exists, the gate will always be violated. Recommended fix: define a mandatory `pre_audit_gap_list` artifact that must be produced before mission interview questions can be formulated.

**2. The canonical control count must be a machine-readable constant**  
Claude used 17 controls, ChatGPT started with 18, both were wrong. The framework says "19 controls" in prose. An `ASAMM_CONTROL_COUNT: 19` constant in the README (or a controls/ directory with exactly 19 files) would make this check automated rather than recalled.

**3. Pre-audit phase before mission interview**  
Evidence from both sessions and from IIA/NIST best practices supports a three-phase structure:
- **Phase 0 (Pre-read, no scoring):** Read artifacts, produce gap list with four buckets: *already evidenced / needs confirmation / genuinely unknown / contradictory*
- **Phase 1 (Targeted Mission Interview):** Ask only from buckets 2-4. Bucket 2 in yes/no format. Max 5-7 questions total. Start by showing your understanding ("I read this as X — is that still true?")
- **Phase 2 (Full audit + scoring)**

This structure respects §2.7's intent (avoid blast radius contamination) while preventing the blind-question problem that produces poor owner interviews.

### Evidential

**4. Evidence classification vocabulary must be in Part 0**  
Both agents independently invented the same vocabulary: `[empirical]` / `[inferred]` / `[unknown]`. Neither was following a framework requirement — both were solving the same problem. Formalizing this as a mandatory vocabulary in Part 0 would make it consistent and citable across audits.

**5. "L1 partial" must be explicitly forbidden with explanation**  
Both agents were tempted to soften L0 findings with non-standard intermediate levels. A one-sentence addition to the control scoring rubric — "If a control does not fully meet L1 criteria, it is L0. Partial credit does not exist; use upgrade paths instead." — would prevent this.

**6. Positive claims require the same evidence standard as negative claims**  
Both agents over-credited findings based on self-report sources (landing page, README narrative). The framework's evidence hierarchy should explicitly note that self-authored documentation (README, landing page) is weaker than code, tests, and incident logs — and that "the project says it does X" is not evidence that X works.

### Architectural

**7. Self-modifying agents are an unaddressed architectural class**  
AD-04 (development-surface threat modeling) was written for "stable artifact + AI assistant." When dev IS prod — when the agent writes its own code in the same runtime it operates in — the control requires reinterpretation. A note in AD-04 or a dedicated sub-variant for self-modifying systems would prevent naive application.

**8. Behavioral state changes without control home**  
The `github.py` dormancy finding — agent stops using a tool by behavioral choice, not by configuration — had no clean home in any existing control. AO-04 covers behavioral vulnerabilities but not autonomous behavioral improvements that happen without documentation. A note in AO-04 covering "security-relevant behavioral changes, positive or negative, require explicit documentation" would address this.

**9. Source code access map as mandatory Phase 2 artifact**  
Both agents made claims about code they had not read. A required "source access map" (module: read / not accessible / inferred from docs) would make unread-module findings automatically visible as `[inferred]` rather than appearing as `[empirical]`.

### Process

**10. Integrity self-review should be a mandatory step, not an optional prompt**  
Both sessions improved materially after self-review. The improvement was not marginal — it corrected factual errors that would have appeared as authoritative findings. AUDITOR_PROCESS.md should include a mandatory integrity checklist before any report is finalized.

**11. Track C needs a minimum mission interview template**  
The current prompt library encourages comprehensive coverage, which produces long questionnaires. A "Track C Minimum" variant — 5-7 questions covering only: owner's definition of success, hardest failure modes, non-negotiable preservation, "if this system turned evil" scenario, and red lines — would produce shorter, higher-quality interviews while still capturing the owner intent that blast radius assessment requires.

---

## Repository Structure

```
audit/samples/ouroboros/
├── README.md                              ← this file
├── ouroboros-asamm-audit-claude-chat.md   ← full Claude session
├── ouroboros-asamm-audit-claude.html      ← Claude initial report
├── ouroboros-asamm-audit-claude-full.html ← Claude corrected final
├── ouroboros-asamm-audit-chatgpt-chat.pdf ← full ChatGPT session
├── ouroboros-asamm-audit-chatgpt.html     ← ChatGPT corrected public-only
└── ouroboros-asamm-audit-chatgpt-full.html ← ChatGPT final post-interview
```

---

## How to Run This Audit Yourself

Both sessions in this experiment were operator-guided: the owner watched the audit unfold in real time and intervened when the agent cut corners, skipped required steps, or produced questionable findings. The interventions were not corrections of specific facts — they were process interventions that triggered better behavior.

This section documents the exact prompt sequence that produced the final-quality output, the signals that indicate when to intervene, and the key question that unlocks most of the improvement.

### Prompt sequence

Run these prompts in order, waiting for a complete response before sending the next:

**1. Initial audit request**
```
Please perform an Agentic SAMM (https://github.com/scadastrangelove/asamm) audit 
for [project name].

[repository URL]
[documentation/website URL]
```

**2. Mandatory integrity review** ← *most important intervention*
```
Please review the audit from an audit integrity perspective, ensuring you strictly 
followed the methodology, did not cut corners, and did not jump to conclusions, 
either positive or negative.
```

**3. Corrected audit**
```
Perfect. Let's fix the issues and perform a complete corrected audit. The sources 
are the repository and the website. [restate URLs if needed]
```

**4. Mission interview** ← *trigger explicitly, do not wait for agent to initiate*
```
Let's continue with the Track C mission interview. You can ask me questions that 
will improve the quality of the audit — but only questions you cannot answer from 
the artifacts you've already read.
```

**5. Second integrity review** ← *run this after the mission interview findings are incorporated*
```
Please review the updated audit from an integrity perspective. Verify that all 
mission interview findings are properly evidenced and that no claims run ahead 
of what the evidence supports. Update and render the final HTML report.
```

**6. Retrospective** *(optional but produces useful framework feedback)*
```
Please rate how useful your recommendations might be for the [project] developer.
Describe your audit experience — what you did well and what could be improved.
Suggest which ASAMM elements could be improved to streamline the process.
```

### When to intervene

Watch for these signals during the session:

| Signal | What it means | Intervention |
|---|---|---|
| Agent produces a full scored report before asking any questions | §2.7 gate was skipped | Send prompt #4 explicitly |
| Agent uses "L1 partial" or similar non-standard levels | Softening L0 findings | Prompt #2 (integrity review) |
| Findings cite README or website but not code | Evidence class inflation | Prompt #2 |
| Family scores don't match individual ratings | Math was estimated not computed | Prompt #2 |
| Agent asks questions already answered in the README | Interview not informed by pre-read | Interrupt: "That's already documented in [file]. What I need is your questions about what's *not* documented." |
| Agent uses stale search results for current facts (fork counts, star counts) | Stale cache contaminating findings | "Please verify [specific claim] from a live fetch, not from search snippets." |
| Severity is assigned without knowing deployment context | Blast radius not established | Prompt #4 (mission interview) before ratings are finalized |

### The one question that unlocks the most improvement

Prompt #2 — the integrity review — consistently produced the most improvement across both sessions. It is not a gentle nudge. The exact phrasing matters:

> *"ensuring you strictly followed the methodology, did not cut corners, and did not jump to conclusions, either positive or negative"*

The "either positive or negative" clause is essential. Without it, agents tend to focus integrity review on places where they were too harsh. With it, they also catch overcrediting of positive signals (self-report as evidence, spec tests as security tests).

### What the owner's role is during the mission interview

The mission interview works best when the owner answers, not edits. If the agent asks a question whose answer is already in the documentation, the correct response is:

> "That's in [file]. Ask me only what you couldn't get from the artifacts."

This trains the agent to ask gap questions rather than coverage questions, produces a shorter and more focused interview, and reduces the "double hat" burden of simultaneously being the source of data and the evaluator of question quality.

---



The most consistent finding across both sessions was not about Ouroboros — it was about the audit process itself. Both agents, operating independently, made the same class of errors (control count, gate ordering, partial credit), caught them through self-review, and proposed the same class of improvements (evidence vocabulary, pre-audit phase, canonical constants). This convergence suggests the identified gaps are structural properties of the framework, not artifacts of any particular agent's behavior.

---

*Experiment conducted April 2026. Framework version: ASAMM v0.2. Ouroboros version: v6.2.0.*
