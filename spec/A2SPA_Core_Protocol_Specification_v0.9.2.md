# A2SPA Core Protocol Specification

**Document Version:** 0.9.2

**Status:** Independent Technical Working Draft

**Implementation Snapshot:** public A2SPA app/API and helper packages `0.1.3`

**Date:** September 3, 2026

**Review Status:** External technical review requested

## Abstract

Agent-to-Secure Payload Authorization (A2SPA) is an execution-time authorization protocol for structured autonomous-agent actions. A2SPA is designed to determine whether a specific signed payload should be accepted immediately before execution-time consequence.

A2SPA complements identity systems, policy systems, transport security, monitoring, and pre-deployment agent analysis. It does not attempt to validate natural-language prompts, model alignment, semantic correctness, ethics, legality, or business desirability. Its narrow purpose is deterministic validation of structured execution intent at the runtime boundary.

This draft is implementation-aligned with the public A2SPA app/API and helper packages `0.1.3`. It is not an IETF document, not an adopted standard, not a certification program, and not a security audit.

## 1. Status and Scope

This repository is an independent technical working draft for external review.

This document intentionally corrects several claims from earlier draft material. The current public implementation does not use the earlier PDF's IETF working-group status language, Go/Rust reference implementation claim, Ed25519 mandatory baseline, RFC 8785 baseline signed-byte profile, EEP-generated challenge nonce handshake, or execution-token state model.

A2SPA addresses:

- Signed structured payloads
- Deterministic signed-byte construction
- Sender and target binding
- Timestamp and nonce replay controls
- Policy evaluation
- Optional continuity evidence
- Signed execution receipts
- Fail-closed caller and enforcement behavior

A2SPA does not address:

- Prompt quality
- Model alignment
- Legal or business correctness
- Natural-language intent interpretation
- Full target-side exactly-once commit semantics
- Pre-deployment vulnerability discovery logic

## 2. Product Boundary

Agent Verify and A2SPA are complementary controls in the agent-security lifecycle.

- Agent Verify is a pre-deployment analysis product that finds execution vulnerabilities in agents and workflows before launch.
- A2SPA is an execution-time authorization protocol that blocks or records unauthorized runtime execution attempts.
- This specification covers A2SPA only.

The intended lifecycle is simple: find execution vulnerabilities before deployment, then stop unauthorized execution at runtime.

## 3. Terminology

The key words MUST, MUST NOT, REQUIRED, SHALL, SHALL NOT, SHOULD, SHOULD NOT, RECOMMENDED, MAY, and OPTIONAL are to be interpreted as described in BCP 14, RFC 2119, and RFC 8174 when they appear in all capitals.

- Subject Agent: The agent, workflow, service, or automation process that produces a structured execution payload.
- Authorization Verifier: The verifier that checks the signed payload, identity binding, timestamp, nonce, policy, and continuity evidence.
- Execution Enforcement Point: The caller-side, gateway, sidecar, runtime, or target-side component that refuses to continue unless A2SPA authorization succeeds.
- Target System: The downstream system that would receive the consequence of the action.
- Signed Payload: The structured payload plus signature submitted to the verifier.
- Execution Receipt: A signed verifier artifact that records the authorization decision and key payload bindings.

## 4. Core Invariant

No signature, no execution.

An execution request MUST NOT be accepted as authorized unless the verifier validates the exact signed payload, sender identity, target binding, timestamp, nonce, signature profile, policy constraints, and any required continuity evidence.

If an integration uses A2SPA as a caller-side verification gate, the caller MUST fail closed unless the returned decision is explicitly authorized. Helper functions such as `requireAuthorizedResult` exist to prevent integrations from treating non-authorized `200` responses as authorized execution.

## 5. Public API Snapshot

The current public API base is:

```text
https://api.aimodularity.com/A2SPA
```

The current bare API alias is:

```text
https://api.aimodularity.com/api/*
```

Current runtime endpoints include:

- `POST /api/verify_payload`
- `POST /api/continuity_state`
- `POST /api/state_continuity`
- `GET /api/receipts/<receipt_id>`

The public helper packages are:

- Python: `a2spa==0.1.3`
- Node.js: `a2spa@0.1.3`

## 6. Request Envelope

The `POST /api/verify_payload` request body has this shape:

```json
{
  "payload": {
    "agent_id": "user_sender",
    "target_agent_id": "user_receiver",
    "timestamp": "2026-09-03T12:00:00Z",
    "nonce": "unique-nonce-0001",
    "hash": "64 lowercase hex sha256 digest",
    "input": {"action": "deliver", "workflow_scope": "messages:deliver"},
    "output": {"status": "ready"},
    "alert_threshold": 10,
    "crypto_profile": "a2spa.classical.rsa_pkcs1v15_sha256.v1"
  },
  "signature": "hex encoded signature"
}
```

Required payload fields are:

- `agent_id`
- `target_agent_id`
- `timestamp`
- `nonce`
- `hash`
- `input`
- `output`
- `alert_threshold`

Optional payload fields are:

- `crypto_profile`
- `state_continuity`

The `input` and `output` fields MUST be JSON strings or JSON objects. The `nonce` MUST be a bounded identifier string and MUST NOT contain path separators. The `hash` MUST be a SHA-256 hex digest of the verifier-compatible signable payload.

## 7. Signed Bytes V1 Compatibility Profile

Current A2SPA public helper packages use the Signed Bytes V1 Compatibility Profile.

The signed bytes are the UTF-8 bytes of a Python-compatible JSON string produced from the normalized signable fields using sorted keys. In the Python reference helper this is equivalent to:

```python
json.dumps(normalize_signable_payload(payload), sort_keys=True).encode("utf-8")
```

The normalized signable field set is:

```text
agent_id
target_agent_id
timestamp
nonce
input
output
alert_threshold
crypto_profile, if present and not null
state_continuity, if present and not null
```

The `hash` and `signature` fields are never part of the signed-byte input.

Implementers SHOULD use the public helper packages or compare against published test vectors before claiming compatibility. This profile is not RFC 8785 JCS. Future canonicalization profiles may be defined, but unsupported or mismatched profiles MUST fail explicitly and MUST NOT silently downgrade.

## 8. Signature Profiles

The active public default signature profile is:

```text
a2spa.classical.rsa_pkcs1v15_sha256.v1
```

This profile signs the Signed Bytes V1 payload using RSA PKCS#1 v1.5 with SHA-256.

The compatibility profile for older payloads is:

```text
a2spa.legacy.rsa_pkcs1v15_sha256.v1
```

The optional post-quantum profile is:

```text
a2spa.pq.mldsa.v1
```

The ML-DSA profile is available only where the verifier deployment and key record support it. If a payload declares a profile that the verifier or sender key does not support, verification MUST fail closed.

## 9. Freshness and Replay Controls

A2SPA uses payload-bound freshness controls:

- `timestamp` provides a bounded wall-clock validity window.
- `nonce` provides one-use replay resistance for the sender payload stream.
- `hash` binds the received payload to the signed canonical bytes.
- `signature` binds the sender key to the exact signable payload.

The current public verifier rejects payload timestamps outside the configured freshness window. The current default window is 120 seconds.

Nonce consumption is verifier-side and atomic for a given `agent_id` plus `nonce` tuple. A nonce accepted once MUST NOT be accepted again within the nonce retention window.

A2SPA replay protection prevents reused authorization payloads. It does not by itself guarantee target-side exactly-once business commit. Integrations that perform real-world side effects SHOULD bind a stable operation id or idempotency key to the target action and recover unknown commit status without duplicating the side effect.

## 10. Policy and Continuity Evidence

The verifier evaluates sender permission, sender enabled state, receiver availability, policy constraints, and optional state-continuity evidence.

Common policy inputs include:

- `action`
- `workflow_scope`
- `workflow_scopes`
- `amount_usd`
- `currency`
- `on_behalf_of`

State-continuity evidence can bind a payload to an authoritative state hash and sequence. The current public API supports continuity registration through `POST /api/continuity_state` and the alias `POST /api/state_continuity`.

## 11. Decisions and HTTP Handling

Current successful authorization returns:

```json
{
  "success": true,
  "receipt": {"receipt": {"decision": "authorized"}}
}
```

Current non-authorized receipt-bearing outcomes may return HTTP `200` with `success: false`, a decision such as `refer_hold` or `denied`, and a signed receipt. Integrations MUST check the authorization decision and MUST NOT treat every HTTP `200` as authorization.

Recommended caller logic is:

- Accept only `success: true` plus receipt decision `authorized`.
- Treat `refer_hold` as not authorized for execution.
- Treat `denied` as not authorized for execution.
- Treat malformed responses, timeouts, missing receipts, and verifier errors as fail-closed.

Common HTTP status behavior includes:

- `200` for authorized decisions and receipt-bearing non-authorized decisions.
- `400` for malformed payloads, hash mismatch, invalid timestamp, unsupported profile, or signature failure.
- `401` for missing or invalid API keys.
- `403` for permission, policy, target availability, or insufficient credits.
- `409` for replay and continuity-state conflicts.
- `500` for verifier-side processing or receipt-signing failures.

## 12. Execution Receipts

The current public implementation emits signed execution receipts for authorized and receipt-bearing non-authorized outcomes.

Receipt fields include:

- `schema_version`
- `receipt_id`
- `issued_at`
- `verifier_id`
- `decision`
- `agent_id`
- `target_agent_id`
- `payload_hash`
- `payload_nonce`
- `payload_timestamp`
- `metadata`

Receipt signatures use RSA-PSS-SHA256 in the current public implementation. Receipt verification is separate from payload signature verification.

## 13. Deployment Models

A2SPA defines logical roles and verifier behavior, not a mandatory topology. Valid deployment patterns may include caller-side verifier gates, API gateways, sidecars, service mesh filters, workflow-engine plugins, MCP-style tool adapters, A2A-style agent adapters, and target-native enforcement hooks.

Any deployment that permits consequential execution without checking an A2SPA authorized decision is outside the intended security boundary.

## 14. Threat Model

A2SPA is designed to reduce runtime blast-radius risk from:

- Payload tampering
- Sender spoofing
- Replay attempts
- Stale timestamp reuse
- Unauthorized action or scope expansion
- Receiver or target mismatch
- Missing continuity evidence where continuity is required
- Caller integrations that confuse identity authentication with execution authorization

Important residual risks remain:

- A compromised private key can sign bad payloads.
- A bad policy can authorize bad behavior.
- A caller that ignores the decision can still execute unsafely.
- A target system can duplicate side effects unless it uses its own idempotency or commit recovery.
- A semantically wrong but signed and policy-compliant request can still be authorized.

## 15. Security Considerations

Private keys MUST be kept out of source control, logs, browser code, chat transcripts, and client-side bundles. Store private keys in server-side secrets, key-management infrastructure, or equivalent protected runtime storage.

Verifier availability MUST NOT weaken fail-closed behavior. If the verifier cannot answer, the caller or EEP must not proceed as if the action is authorized.

Profile negotiation MUST be explicit. Unsupported signature or serialization profiles must fail rather than downgrade silently.

Receipts are audit evidence, not a substitute for checking the live authorization decision before execution.

## 16. Conformance Levels

This draft defines two practical conformance levels for review.

### 16.1 Public Compatibility Conformance

An implementation is public-compatible if it:

- Produces the Signed Bytes V1 Compatibility Profile exactly.
- Supports `a2spa.classical.rsa_pkcs1v15_sha256.v1` payload signing or verification.
- Computes payload hashes over the same normalized signable field set.
- Enforces timestamp freshness and one-use nonce semantics.
- Rejects malformed, unsigned, replayed, mismatched, or unauthorized payloads.
- Treats only `authorized` decisions as execution authorization.

### 16.2 Enforcement Conformance

An integration is enforcement-conformant if it:

- Places A2SPA before the consequential target action.
- Fails closed on missing, malformed, timeout, denied, or refer-hold outcomes.
- Does not bypass the verifier path for equivalent target actions.
- Preserves enough receipt and decision evidence for audit.
- Uses target-side idempotency where side effects can be retried or duplicated.

## 17. Interoperability Guidance

Interoperability depends on matching signed bytes exactly. The safest approach is to use the official helper package for the runtime language.

Independent implementations should verify:

- The normalized signable field set.
- Sorted JSON object keys.
- Python-compatible JSON formatting for the Signed Bytes V1 profile.
- UTF-8 byte encoding.
- SHA-256 payload hash calculation.
- RSA PKCS#1 v1.5 SHA-256 signature verification for the classical profile.
- Exact decision handling for `authorized`, `refer_hold`, and `denied`.

## 18. Open Review Questions

The following topics are open for external technical review:

- Whether a future RFC 8785 or deterministic CBOR profile should become a new versioned profile.
- How best to publish cross-language test vectors for the current Signed Bytes V1 profile.
- Which deployment profiles should be treated as enforcement-conformant versus verifier-only integrations.
- How to specify target-side operation ids without overclaiming exactly-once side-effect guarantees.
- How to represent human review without allowing natural-language summaries to replace exact payload binding.

## 19. References

- RFC 2119: Key words for use in RFCs to Indicate Requirement Levels.
- RFC 8174: Ambiguity of Uppercase vs Lowercase in RFC 2119 Key Words.
- RFC 8017: PKCS #1 RSA Cryptography Specifications Version 2.2.
- RFC 6234: US Secure Hash Algorithms.
- FIPS 204: Module-Lattice-Based Digital Signature Standard.
- Python helper package: `a2spa==0.1.3`.
- Node.js helper package: `a2spa@0.1.3`.

## Appendix A. Earlier Draft Disposition

The earlier `v0.9.1-clean` draft contains useful review structure, including role separation, invariants, threat modeling, conformance framing, security considerations, and falsification criteria.

It should not be copied verbatim into current public documentation because it includes claims that do not match the current public A2SPA implementation. Specifically, it uses IETF-style status language, names a Go/Rust reference implementation, selects Ed25519 as mandatory baseline, treats RFC 8785 JCS as the baseline signed-byte profile, and describes EEP-issued challenge nonces and execution tokens. Those items are not current public A2SPA `0.1.3` behavior.
