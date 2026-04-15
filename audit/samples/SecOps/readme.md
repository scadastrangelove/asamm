# ASAMM Track A+C Audit: SecOps Cybersecurity Platform MVP with Embedded Agent

> A self-audit of SecOps — a cybersecurity platform MVP with a deeply embedded agentic component (Onotolle, LangChain/LangGraph over self-hosted LLM) — plus an agentic development workflow (Claude Code + PM-assistant + MCP). First ASAMM audit of a pre-production multi-tenant system where the agent operates on client vulnerability data. Conducted by Claude Opus 4.6 under Track A + Track C discipline.

---

## Context

**Auditor:** Claude Opus 4.6 (Track A shell access + Track C evidence discipline)
**Owner:** Alex (freekoder@gmail.com)
**Track:** Track A (self-audit, shell access) + Track C discipline (evidence hygiene, integrity review)
**Target:** SecOps cybersecurity platform — Onotolle embedded agent + Claude Code dev workflow
**Framework:** [ASAMM v0.2](https://github.com/scadastrangelove/asamm)
**Date:** 2026-04-15
**Status:** READY FOR INDEPENDENT VERIFICATION (Phase 4.5 outstanding)

---

## What makes this audit different

This is the first ASAMM audit of a **production-bound multi-tenant system with an embedded agent**. Previous audits (Ouroboros, Claude Code + Zhet) covered development tools and autonomous agents. SecOps is different in three ways:

| Dimension | Previous audits | This audit |
|---|---|---|
| System type | Dev tool / autonomous agent | Multi-tenant SaaS platform with embedded agent |
| Data sensitivity | Developer's own code | PII, credentials, threat intel |
| Agent integration | External (API) or self-contained | Deeply embedded — agent tools call use-case layer directly |
| Blast radius at prod | Developer inconvenience | Cross-tenant data exposure, client-facing notifications |
| Kill switch | Platform-level (stop the tool) | Must be application-level (agent is inside the app) |

The audit also demonstrates **agentic development surface** (Claude Code + PM-assistant + MCP) operating alongside an **agentic product surface** (Onotolle) — two independent agent layers in the same codebase with different risk profiles.

---

## Artifacts

| File | Description |
|---|---|
| `asamm-audit-2026-04-15-public.html` | Full HTML audit report, v3.0 (integrity-reviewed, secrets redacted). 18 findings, 19/19 control matrix, evidence appendix, 3-horizon remediation roadmap |
| `readme.md` | This file |

---

## Key findings

### Critical (mission-owner-derived)

**F-1 — Comprehensive ACL bypass of the entire Onotolle tool surface.**
API endpoints enforce `user.has_access_to_project()`; use-case layer does not. Agent tools call use-cases directly, bypassing ACL for read, write, create, and publish. The owner's non-negotiable ("no cross-tenant data access") has zero technical enforcement on the agent path. This is an architectural pattern, not a point bug — every new tool will inherit the bypass unless enforcement moves to the use-case layer.

**F-3 — No kill switch.** `is_enabled()` returns hardcoded `True`. No config path, no feature flag, no rate limiter. The only way to stop the agent is to stop the entire backend or the upstream LLM host.

**F-18 — System prompt has zero tenant-isolation instructions.** Even the behavioral floor is absent for the mission-critical constraint. The agent has no instruction — technical or behavioral — to restrict itself to the current user's projects.

### High (6 findings)

Credentials exposed in multiple locations (git history, disk, settings files, archive transport). Wide bash permissions in Claude Code dev environment. Prompt-injection surface wide and unsanitized. Engine search as unscoped external egress vector. Redis checkpoint silent fallback to in-memory.

### Medium (5 findings)

SKILL.md vs config contradictions, non-draft MR defaults, subagent tool inheritance without per-invocation scoping, stale workstation paths, partial MCP version pinning.

**Maturity: 15×L0, 4×L1, 0×L2, 0×L3** — effectively the null baseline for an agentic security programme.

---

## Methodology innovations

This audit introduced several practices not yet formalized in ASAMM:

### 1. Tiered interview with conservative severity bounds

Mission interview questions were split into three tiers (Core 3/3, Scope 3/3, Deepening 0/12). When the owner answered only 6 of 18 questions, the audit continued under the rule: "Conservative wins. No answer → take the less favorable claim." Each finding dependent on an unanswered question carries an explicit severity bound: `F-4: High [bounded by C1]`.

### 2. Sub-agent evidence downgrade

First-pass audit used sub-agents for data collection. On integrity review, all sub-agent-originated claims were systematically downgraded from `[empirical]` to `[inferred]` until re-verified by the primary auditor. This produced 4 grade withdrawals (§0.4).

### 3. Explicit claims withdrawal section

§0.4 "Claims that did not survive re-verification" documents 4 specific downgrades with rationale. This makes integrity review a concrete, auditable artifact rather than a checkbox.

### 4. Application-layer ACL bypass as finding class

F-1 identifies a bug class not explicitly named in ASAMM: agent tools bypass the same authorization checks that API endpoints perform. This is distinct from tool abuse (AI-03) — the tools work as designed, but the design creates an authorization model divergence between human-facing and agent-facing paths.

### 5. Pre-prod readiness gate concept

§12 proposes a "readiness to accept client data" control: collect L1-floor across families before any rollout beyond single-user dev. ASAMM currently assumes an operating system; this audit shows the need for a formal gate at the dev→staging boundary.

---

## Framework gaps identified (§12)

| Gap | Description | ASAMM coverage |
|---|---|---|
| Authorization model parity | Agent tools should not bypass checks that API endpoints perform | AI-03 covers scope, not API-layer inheritance |
| Dev-time agentic workflows | Autonomous commit/push/MR, shared mutable MEMORY.md, per-skill claims | AD-04 is thin on specifics |
| MCP supply chain anti-patterns | `uvx xxx@latest` as common unversioned dependency | AG-02 conceptually, no dedicated check |
| Shared mutable memory | Dual-write surfaces (user + agent) need explicit write-log | AI-04 requirement should be explicit |
| Pre-prod readiness gate | "Ready for client data" control collecting L1-floor | Not covered |

---

## Relation to other ASAMM audits

This is the third environment type in the ASAMM audit corpus:

| Audit | Track | Environment | Agent type | Key contribution |
|---|---|---|---|---|
| Ouroboros (dual-agent) | C | Chat | Self-modifying autonomous agent | Cross-model methodology convergence |
| Claude Code + Zhet | A | Terminal | Dev tool (Claude Code) | Bash probes, adjacent-repo discovery |
| **SecOps** | **A+C** | **Shell** | **Embedded product agent + dev workflow** | **Multi-tenant, authorization bypass, pre-prod gate** |

The three audits together show ASAMM operating across single-agent, multi-agent, dev-tool, and embedded-product architectures. Each surfaced gaps invisible from the others.

---

## How to run this audit yourself

Follow the same methodology on your own agentic system:

1. Clone the ASAMM repo: `git clone https://github.com/scadastrangelove/asamm`
2. Read the audit prompt: [`audit/samples/ouroboros`](https://github.com/scadastrangelove/asamm/tree/main/audit/samples/ouroboros#how-to-run-this-audit-yourself)
3. Give the prompt to any LLM with your project URL
4. Request an integrity review — this catches the most common failure mode
5. Conduct the mission interview — blast radius cannot be correctly assigned without it

**Before sharing your audit report:** strip all literal secrets, tokens, internal hostnames, and PII. An audit that discovers credential leaks must not become one. See §2.7 of the framework for the full sanitization checklist.

---

## Owner's three-point response

After reviewing the audit, the owner identified three actions that determine whether the system can move to staging:

1. **F-1 is architectural.** Fix at use-case layer, not per-caller. One day of work vs 90 minutes, but the difference between "fixed" and "won't break again."
2. **The system is in the ideal window.** Pre-prod, no client data, blast radius bounded to dev DB. Horizon 0 is 4 hours. No reason not to do it today.
3. **F-18 is cheaper than it looks.** One line in `system_v1.prompt` creates the last-line behavioral defense that currently doesn't exist at all. Technical enforcement (F-1) + behavioral floor (F-18) = defense in depth. Either alone = single point of failure.

---

*Audit conducted 2026-04-15. Report version 3.0 (integrity-corrected, secrets redacted). Status: ready for independent verification, Phase 4.5 outstanding.*
*Framework source: [github.com/scadastrangelove/asamm](https://github.com/scadastrangelove/asamm)*
