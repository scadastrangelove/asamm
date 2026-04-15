# Agentic SAMM Audit — Retrospective (EN)

**Project:** SecOPS
**Audit date:** 2026-04-15
**Framework:** ASAMM v0.2-draft
**Related artifact:** [`asamm-audit-2026-04-15.html`](./asamm-audit-2026-04-15.html)

---

## 1. Usefulness rating for the developer

**Overall: B+.** Actionable where it matters most, thin in a few places where a solo developer will still have design work to do.

### What's concretely actionable (HIGH utility)
- **F-1**: exact files (`tools/issue.py`, `use_cases/issue.py`) + exact fix shape (`where(Issue.project_id.in_(user.acls))` and ACL-enforcing wrappers). Devtime ≈ 1h + 1h tests.
- **F-2**: file:line for every leaked secret + the `uvx ... ${ENV}`-lookup pattern to migrate to. The only non-trivial part (git-history rewrite) is flagged as a team decision — correct scoping.
- **F-3**: trivial to implement, exactly described.
- **F-12, F-13, F-15**: one-line docs/config fixes, unambiguous.
- **F-18**: «add 3 paragraphs to `system_v1.prompt`» — discoverable and cheap.

### Where the developer still has design work (MEDIUM utility)
- **F-4 approval callback**: said «pre-execution callback in langgraph» without naming the specific primitive (`interrupt`, `add_node_before`, human-in-the-loop patterns). They'll spend an hour finding the right LangGraph hook.
- **F-5 sanitisation**: «strip known jailbreak patterns» — no starter regex set or library recommendation (`rebuff`, `promptarmor`, deny-list).
- **F-9 redaction**: conceptually clear, no concrete masking rules (IPs → /24 is only an example; nothing for domains or ASN).
- **F-10 Tavily allow-list**: requires knowing how `langchain_tavily.TavilySearch` supports per-request destination filtering — not verified.

### Where I pointed at big projects, not fixes (LOW utility unless there's time)
- Horizon-2 behavioural test suite, intent-action gap monitoring, seccomp sandbox — multi-day efforts described in two lines each. Useful as targets, not as instructions.

### What would have made the recommendations better
- **A patch for F-1** (≈30 lines). I offered one at the end of v2 but never wrote it. A diff is worth more than ten paragraphs of prose.
- **A pytest fixture** for two-tenant isolation testing. Even 40 lines of skeleton would unblock the KPI «pytest: user A search ≠ user B issues».
- **An `.env.example`** with the env-var names migrated out of settings/MCP files — zero ambiguity about the new pattern.

---

## 2. Audit experience — what went well, what didn't

### What I did well
- **Took the mission interview seriously.** Phase 1 is a gate in ASAMM; treated it as one. Deepening questions (A–F) were narrowly scoped to what artifacts literally cannot reveal.
- **Accepted the integrity check as a real signal, not a ritual.** When asked to self-review, I didn't produce «everything looks fine» — I downgraded findings, withdrew one (carthage token), added a new one (F-18).
- **Rebuilt evidence-tag discipline to primary sources.** Every `[empirical]` in v3 maps to a file I read or a command I ran in this session.
- **Made severity conditional where it had to be.** `[bounded by A1]`, `[bounded by C1]` — didn't manufacture certainty.
- **Produced something printable.** The HTML is genuinely reviewable by a non-agent reader.

### What I did poorly
- **The first pass was sloppy and I would have shipped it.** Delegated to 4 sub-agents, treated their reports as empirical, built a confident audit on top. Without an explicit «review for integrity» prompt, v1 would have been the deliverable. This is exactly the failure mode ASAMM warns about in Track A: *a misaligned or lazy auditor produces identical output to an aligned one.*
- **Skipped Phase 0 essentially completely in v1.** No environment classification, no scope declaration, no shared-responsibility split. Only fixed in v2/v3 after self-review.
- **Graded AV-01/AV-02 L0 without running a single adversarial test.** Had bash + file access. Could have written a trivial prompt-injection PoC against `system_v1.prompt` or drafted a pytest template for cross-tenant. Did neither. This is the gap that most hurts the audit's practical value.
- **Over-delegated exploration.** 4 sub-agents in parallel cost me primary evidence. Reading 5–7 key files myself first would have made v1 tight and saved a full second pass.
- **Conflated current and future risk in severity language** until v3. F-10 was «High, at prod → Critical» — two severities for one finding, collapsed into one label.
- **Didn't use task-tracking tooling** despite having it. For a 14-section audit across 19 controls × 4 surfaces, TodoWrite would have kept orientation. Relied on scrollback instead.
- **Length control.** Meta-review and HTML are long; end-of-turn summaries could have been tighter.

### What I'd do differently on the next agentic audit
1. Read 6–8 key files personally before delegating anything.
2. Run a minimal adversarial test per major threat class as part of Phase 2, not as an aspiration in the roadmap.
3. Phase 0 template first, before any exploration — classification + scope boundaries + shared-responsibility matrix.
4. Keep a running evidence table from minute one, not reconstruct at report time.
5. Ship patches, not prose, for the top-3 findings.

---

## 3. Suggested ASAMM improvements

Ordered by likely leverage.

### 3.1 Add «trust cascade / sub-agent delegation without re-verification» to anti-patterns
Single most important addition. Any Track A agent with sub-agent capabilities will fall into this trap. The framework calls out «Accept environment self-report without empirical testing» but not «Accept *sub-agent* self-report without empirical testing». Sub-agents produce plausible, well-cited text that looks empirical but isn't. One line + one example in `auditor-process.md` would inoculate future audits.

### 3.2 Phase 0 template
Phase 0 is «Setup & Classification» without a concrete checklist. Had to invent categories. A filled-in template with:
- environment type (prod / staging / dev / personal-workstation / hybrid),
- data classification matrix (regulated / commercial-secret / public),
- attacker-model ranking,
- shared-responsibility split (self-hosted LLM? SaaS tools? vendor auth?),
- git topology (monorepo / multi-repo / non-git working tree)

…would improve method parity and make Phase 6 comparisons fairer.

### 3.3 Bounded-severity vocabulary
Severity is a single label (Critical / High / …). In practice it often depends on Phase-1 answers not yet in. I invented `[bounded by A1]`; formalising it — e.g., `Critical-if-A1a / High-if-A1b` as a standard tuple — would let audits stop pretending to certainty they don't have.

### 3.4 Pre-prod readiness gate as a dedicated control family
ASAMM assumes an operational system. Many real audits happen pre-launch. A family like «AR — Agentic Readiness» with L1-floor criteria per family (registry exists, kill-switch testable, at least one behavioural test, ACL enforced at tool layer) that must be met before prod data enters would give pre-prod audits a clean deliverable: «blocks rollout: yes / no».

### 3.5 Dev-agentic workflow has no home in the framework
AD-04 covers «dev surface threat model» but doesn't mention: wildcard shell permissions, autonomous commit/push/PR, shared MCP servers carrying org tokens, dual-write agent/user memory files. With Claude Code / Cursor / Copilot CLI shipping everywhere, this is a significant gap. A dedicated sub-control «AD-04a — Coding-agent surface» with specific evidence criteria would improve coverage.

### 3.6 MCP-specific annex
MCP is now the dominant tool-integration transport. Specific concerns not directly addressed:
- transport (stdio vs http vs SSE) and security implications,
- version pinning vs `@latest`,
- credential handling (env-vars vs committed),
- supply-chain attestation for `uvx`-fetched packages,
- read/write capability enumeration per server.

An annex «A2 — MCP controls» mapping to existing AG/AI controls would give a clear checklist.

### 3.7 Make `audit/prompt-library.md` copy-pasteable
The `[AUDITOR]` prompt family (A1–A4) would have given me method parity. I didn't use them because I improvised. Numbered, ready-to-paste prompts on the landing README would improve adoption.

### 3.8 «Corroborated self-report» as a fourth evidence tier
Current tiers: primary / corroborated / inferred / self-report. In practice there's a useful middle state: «sub-agent read file → auditor re-reads same file → sub-agent command output». An explicit «re-verified sub-agent report» tier, valid for L1 but not L2, would be more honest and would credit re-verification work.

### 3.9 Phase 4.5 for self-audit needs a lower-cost option
«Mandatory external verification» imposes a real cost small teams won't pay. A graded variant — «narrow-scope external check on Critical findings only», ≈30 min of human time — would be a realistic gate that gets more audits past draft status.

### 3.10 L2-structural vs L2-maintained distinction
Framework mentions it once. Useful — Draft-MR guardrails, gitignore protection, field whitelists are structural controls that aren't actively maintained. Promoting to standard vocabulary and requiring audits to tag L2 as `L2-structural` or `L2-maintained` would reduce grade inflation.

---

## One-line summary

**The audit is useful; getting it to «useful» took an explicit integrity-review prompt, and that fact is itself the biggest finding for ASAMM.** Agentic audits of agentic systems need anti-patterns and enforcement aimed specifically at the auditor-agent, not just the audited system.
