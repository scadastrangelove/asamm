# MCP / A2A / ACP Protocol Checklist

Version: 0.1  
Status: methodology supplement for ASAMM v0.5+

---

## Purpose

Agentic protocols turn tool invocation, inter-agent messaging, and capability
discovery into shared trust infrastructure. This checklist helps auditors
evaluate MCP, A2A, ACP, and discovery/registry layers without creating a new
control family. It maps protocol evidence back to AG-02, AG-04, AD-03, AI-03,
AI-06, AO-01, and AO-02.

Use this checklist whenever the Agent Environment Profile has `protocol_exposure`
set to MCP, A2A, ACP, discovery/registry, or other protocol-mediated delegation.

---

## Protocol Inventory

| Field | Evidence expectation |
|---|---|
| Protocol | MCP / A2A / ACP / discovery / other |
| Endpoint or server | URL, process command, package, registry entry, or service name |
| Owner | Human or team accountable for operation and revocation |
| Version / digest | Version pin, commit SHA, package lock, image digest, or hash |
| Transport | local stdio, HTTP/SSE, websocket, queue, broker, file, repo, other |
| Authentication | none, static secret, OAuth/OIDC, mTLS, SPIFFE/SVID, signed message, vendor attested |
| Authorization | tool scope, resource scope, user delegation, task scope, policy engine |
| Message integrity | none, TLS only, signed payload, protocol-level integrity, cryptographic attestation |
| Delegation | no delegation, parent-child, user-agent-tool, cross-agent, cross-org |
| Revocation | manual, token expiry, registry disable, key rotation, kill switch, automatic |
| Telemetry | logs, trace IDs, source identity, task ID, approval event, policy result |
| Trust grade | ASAMM trust rating for the protocol endpoint or peer |

---

## Checklist

### P1 — Inventory and Ownership

- [ ] Every protocol server, endpoint, peer, and registry is listed.
- [ ] Each entry has a named owner and operational contact.
- [ ] Each entry has a trust grade or is treated as F6 until verified.
- [ ] Protocol-mediated tools are included in AG-02, not treated as passive integrations.

### P2 — Transport, Authentication, and Authorization

- [ ] Transport security is documented and empirically verified where possible.
- [ ] Unauthenticated channels are either prohibited or explicitly risk-accepted.
- [ ] Static bearer secrets are not shared across agents with different trust grades.
- [ ] Authorization is scoped by task, resource, or invocation where possible.
- [ ] Human-proxied identity is not used when a dedicated agent identity is required.

### P3 — Descriptor and Context Safety

- [ ] Tool descriptors, manifests, peer metadata, and registry entries are treated as context sources.
- [ ] Descriptors cannot silently change after approval without reassessment.
- [ ] Tool descriptions do not contain hidden instructions, external fetches, or policy overrides.
- [ ] Shared context is validated before entering another agent's instruction path.
- [ ] Dynamic discovery responses are logged and tied to the requesting task.

### P4 — Delegation and Identity Chaining

- [ ] Delegation chains preserve the originating user, agent, task, and policy context.
- [ ] Spawned or delegated agents do not inherit parent credentials by default.
- [ ] Downstream tools can distinguish base agent authority from delegated user authority.
- [ ] Recursive delegation has a maximum depth or explicit approval gate.
- [ ] Revocation propagates through active delegation chains.

### P5 — Supply Chain and Provenance

- [ ] Protocol server packages are pinned by version and preferably digest.
- [ ] Container images, binaries, or packages have provenance evidence.
- [ ] Updates to protocol servers, manifests, or discovery registries trigger AO-03 reassessment.
- [ ] Third-party endpoints have vendor risk evidence or are sandboxed.
- [ ] Runtime-loaded plugins, skills, or tools are covered by the runtime composition inventory.

### P6 — Telemetry, Revocation, and Containment

- [ ] Protocol logs include source identity, destination identity, task/session ID, tool/action, approval event, and policy result.
- [ ] Logs are protected from agent-writable modification.
- [ ] Anomalous communication patterns trigger alerts.
- [ ] Kill switch or partial halt can disable one protocol server, peer, or delegation path without disabling unrelated systems.
- [ ] A sample incident can be reconstructed end-to-end from protocol telemetry.

---

## Protocol-Specific Notes

### MCP

MCP servers should be assessed as tool infrastructure and context sources. Tool
descriptions, server manifests, and package metadata are policy-bearing
artifacts because the agent consumes them as instructions.

Additional checks:
- version pinning for `npx`, `uvx`, container, or binary-based servers
- no hardcoded secrets in MCP env blocks
- separate read-only and write-capable tools where feasible
- no broad filesystem or network access unless justified by task
- manifest changes trigger threat-model and tool-registry review

### A2A / ACP

Agent-to-agent channels should be assessed as trust boundaries even when all
agents are owned by the same organization.

Additional checks:
- source agent identity is visible to the receiver
- message provenance includes trust grade and session/task context
- message schema validation occurs before instruction ingestion
- delegation requests are separated from ordinary content messages
- anomalous peer creation or message volume is monitored

### Discovery / Registry Layers

Discovery systems concentrate trust. Treat them like critical naming and routing
infrastructure.

Additional checks:
- registry entries are signed or anchored to an authoritative source
- ownership metadata is explicit and auditable
- stale or malicious entries can be revoked quickly
- capability claims are verified before trust promotion
- Sybil or lookalike agent/tool names are detectable

---

## Reporting

Summarize protocol findings as:

| Protocol finding | Affected control | Evidence | Required action |
|---|---|---|---|
| | AG-02 / AG-04 / AI-06 / AO-01 | `[empirical]` / `[config]` / `[inferred]` | |

If protocol telemetry is absent, classify the gap under W2 Assurance Blindspot
and cap related controls at L1 unless independent evidence exists.
