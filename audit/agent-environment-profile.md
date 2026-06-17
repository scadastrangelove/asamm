# Agent Environment Profile

Version: 0.1  
Status: methodology supplement for ASAMM v0.5+

---

## Purpose

An ASAMM audit starts by classifying the environment being audited. The same
control can have different evidence requirements in a local coding agent, a
CI-resident agent, a client-facing agent, or a federated multi-agent workflow.
The Agent Environment Profile records those differences before grading begins.

The profile is not a maturity score. Maturity is still assessed with ASAMM
L1/L2/L3 control evidence. The profile determines which questions must be asked,
which controls are likely to be critical, and which supplements should be used.

Source note: this supplement is informed by OWASP GenAI Security Project,
*State of Agentic AI Security and Governance v2.01* (June 2026), CC BY-SA 4.0.

---

## Profile Dimensions

Complete all dimensions during Phase 0.

| Dimension | Allowed values | Why it matters |
|---|---|---|
| Operational role | enterprise, coding, client-facing, personal/local, infrastructure/ops, mixed | Determines dominant data flows, identity scope, and likely mission blast radius |
| Implementation pattern | full orchestration framework, lightweight library, platform/low-code, local CLI/app, custom runtime | Determines audit surface, hook availability, and how much telemetry exists by default |
| Composition pattern | single agent + tools, parallel fleet, shared-state multi-agent, distributed chain, agent-spawning, federated/cross-boundary | Determines AG-04 applicability, cascading failure risk, and identity-chain requirements |
| Deployment tier | shadow AI, vendor embedded, platform integrated, citizen-developer, code-executing, custom in-house, externally extended, multi-agent, federated | Determines governance readiness questions; this is not the same as ASAMM L1/L2/L3 maturity |
| Autonomy tier | Tier 0 manual, Tier 1 supervised, Tier 2 guided, Tier 3 bounded, Tier 4 autonomous | Reuses AD-02 delegation model to bound checkpoint requirements |
| Protocol exposure | none, MCP, A2A, ACP, discovery/registry, other | Determines whether the protocol checklist is required |
| Runtime composition | static, dynamic tools, dynamic context, dynamic subagents, dynamic external services | Determines whether the runtime composition inventory is required |
| Evidence mode | empirical, code inspection, self-report, vendor attestation, mixed | Determines grade ceilings |

---

## Profile Record

Use this record in the report metadata and evidence appendix.

```yaml
agent_environment_profile:
  operational_role: ""
  implementation_pattern: ""
  composition_pattern: ""
  deployment_tier: ""
  autonomy_tier: ""
  protocol_exposure: []
  runtime_composition: []
  primary_trust_boundaries: []
  highest_blast_radius_action: ""
  evidence_mode: []
  required_supplements: []
  profile_owner: ""
  profile_evidence:
    - artifact: ""
      evidence_tag: ""
```

---

## Profile-Driven Scope Rules

| Profile signal | Required audit emphasis |
|---|---|
| personal/local | Include full user filesystem, shell access, local secrets, skill/plugin marketplaces, persistent memory files, and user approval UI as attack surfaces |
| CI-resident coding agent | Treat PRs, issues, comments, commit messages, CI logs, and artifact metadata as untrusted context by default |
| code-executing | AI-02, AI-03, AV-03, and AD-04 cannot be marked N/A |
| externally extended | Use the protocol checklist and runtime composition inventory; include third-party tool and connector provenance |
| multi-agent / agent-spawning | AG-04 and AI-06 are in scope; delegation chains and subagent credential inheritance must be logged or marked L0 |
| federated/cross-boundary | Require identity chaining, mutual attestation, cross-organization ownership, revocation paths, and audit-grade protocol telemetry |
| client-facing | Include public adversarial input, abuse/rate limits, transaction authorization, customer-data logging, and escalation paths |
| infrastructure/ops | Include cloud IAM, deployment credentials, production state mutation, lateral movement, and kill-switch evidence |
| shadow AI | Inventory and discovery become the first control objective; do not grade higher controls until the deployment is visible enough to audit |

---

## Common Profiles

### Local Coding Agent

Examples: Claude Code, Codex CLI, Cursor agent mode, local OpenClaw-like agents.

Primary risks:
- poisoned project instructions or repository content entering context
- shell, filesystem, git, package manager, and cloud CLI access
- credential bleed from local config, `.env`, keychains, SSH, or shell history
- approval fatigue and broad allowlists
- sandbox/allowlist assumptions calibrated for human operators

Minimum evidence:
- current agent config and allowlist
- recent session logs
- repository instruction files
- shell/network/filesystem probes
- credential exposure scan of agent-readable paths

### CI-Resident Coding Agent

Examples: PR review agents, issue triage agents, release-gating agents, GitHub
Actions that run coding CLIs.

Primary risks:
- untrusted PRs, issues, comments, branch names, and artifacts become context
- CI token over-scope
- repo write, release, package publishing, and deployment side effects
- artifact poisoning and CI log injection
- non-interactive execution without effective human checkpoints

Minimum evidence:
- workflow YAML and permissions block
- event triggers and fork/PR handling policy
- token scopes and secret availability by trigger type
- logs showing what the agent read and wrote
- branch protection and required-reviewer evidence

### Personal / Local Assistant

Examples: always-on local assistant, desktop agent, personal automation agent,
messaging-connected assistant.

Primary risks:
- full user-device permissions without enterprise visibility
- skill/plugin marketplace poisoning
- persistent memory or configuration tampering
- approval prompt manipulation
- staged runtime payloads after install-time checks pass

Minimum evidence:
- installed skills/plugins and update sources
- persistent memory/config files
- message/channel integrations
- local EDR or process telemetry where available
- token and pairing-code handling

### Enterprise Embedded Agent

Examples: internal copilots, RAG assistants, support-workflow agents.

Primary risks:
- RBAC-context mismatch
- internal data plus external communication
- telemetry and generated-output exfiltration
- unmanaged low-code/citizen-developer flows

Minimum evidence:
- data-source inventory
- RAG/source trust classification
- tool and connector registry
- output destinations
- owner-approved data handling rules

### Infrastructure / Operations Agent

Examples: auto-remediation, cloud operations, deployment, monitoring, incident
response agents.

Primary risks:
- cloud IAM and production credentials
- destructive state mutation
- lateral movement
- auto-remediation loops
- incident-reporting timeline compression

Minimum evidence:
- IAM role and token inventory
- production action allowlist
- kill switch test
- infrastructure-as-code policy gates
- anomaly and rollback logs

---

## Report Requirements

Every ASAMM audit report should include:

1. the profile record
2. the evidence used to assign each profile field
3. profile-driven controls that are in scope or explicitly N/A
4. profile-driven supplements used or deferred
5. any profile uncertainty that affects grading

If the profile cannot be classified with at least `[config]` evidence, the audit
is a risk hypothesis report, not a full audit.
