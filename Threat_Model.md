# A2SPA Threat Model  
## Execution-Time Authorization for Agentic AI Systems

---

## 1. Scope and Assumptions

This threat model applies to systems that execute actions which produce
irreversible side effects, including but not limited to state changes,
external API calls, financial transactions, infrastructure modification,
or capability issuance.

This threat model assumes:
- actions may be executed asynchronously or after delay
- execution may occur across agent, tool, or system boundaries
- agents, intermediaries, and orchestration layers are not inherently trusted
- transport- and identity-layer controls (e.g., TLS, IAM) may already be in place

This threat model does **not** assume continuous human oversight at execution time.

---

## 1.5 System Boundary and Trust Assumptions

A2SPA defines a strict system boundary at the **execution point**—the moment
an action produces irreversible side effects.

The following components are considered **out of scope for trust**:
- agent reasoning and planning logic
- message brokers, queues, and schedulers
- orchestration layers and retry mechanisms
- intermediate agents or tools forwarding execution requests

Only the **execution-time verification decision** is trusted to gate action
execution. All upstream components are treated as potentially compromised,
misconfigured, or malicious.

---

## 2. Threat Surface Shift

Traditional application security assumes that:
- authorization occurs immediately before execution
- execution context remains stable
- authorized payloads are not modified
- upstream checks imply downstream safety

Agentic and autonomous systems violate these assumptions.

Execution may be delayed, delegated, retried, chained across systems,
or initiated by agents without direct human observation.

As a result, the primary attack surface shifts from request handling
to **unauthenticated execution**.

Without explicit execution-time verification, systems implicitly trust that
authorization intent survives unchanged until execution—an assumption that
does not hold in autonomous workflows.

---

## 3. Threat Categories

### 3.1 Payload Mutation

**Threat**  
An action is authorized, then modified before execution such that the
executed action differs from what was approved.

**Risk Without A2SPA**  
Execution proceeds based on assumed trust in upstream authorization.

**A2SPA Control**  
Cryptographic binding of authorization to the exact action payload.
Any mutation invalidates verification at the execution boundary.

**Outcome**  
Mutated actions are denied at execution time.

---

### 3.2 Replay Attacks

**Threat**  
Previously authorized actions are replayed to trigger unintended execution.

**Risk Without A2SPA**  
Systems cannot distinguish fresh execution from reused authorization.

**A2SPA Control**  
Freshness validation through nonces and validity windows enforced at execution.

**Outcome**  
Replayed actions fail verification and do not execute.

---

### 3.3 Delayed Execution with Stale Context

**Threat**  
An action executes long after authorization under conditions that no longer
match the original intent, constraints, or permissions.

**Risk Without A2SPA**  
Execution occurs even when authorization context is outdated.

**A2SPA Control**  
Expiration and constraint validation enforced at the execution boundary.

**Outcome**  
Stale or expired actions are denied at execution time.

---

### 3.4 Spoofed Agent Execution

**Threat**  
An agent or intermediary triggers execution while impersonating a legitimate
execution path or authorized actor.

**Risk Without A2SPA**  
Downstream systems trust agent identity or call provenance without verifying
that the specific execution was authorized.

**A2SPA Control**  
Authorization is cryptographically bound to a specific authorizing identity,
action, and execution scope, independent of the executing agent.

**Outcome**  
Spoofed executions fail verification and are blocked.

---

### 3.5 Unauthorized Escalation

**Threat**  
An action executes beyond the permissions or constraints originally approved.

**Risk Without A2SPA**  
Permissions are enforced at access time, not execution time.

**A2SPA Control**  
Explicit constraints are verified at execution, not inferred from identity.

**Outcome**  
Escalated actions are denied at execution time.

---

### 3.6 Assumed Trust in Autonomous Workflows

**Threat**  
Systems assume upstream validation guarantees safe execution downstream.

**Risk Without A2SPA**  
Execution occurs based on assumptions rather than verification.

**A2SPA Control**  
Execution is gated on real-time verification at the execution boundary.

**Outcome**  
Execution is permitted only when verification succeeds.

---

## 4. Non-Goals

A2SPA does **not** attempt to:
- secure model training or inference
- prevent hallucinations or reasoning errors
- replace identity and access management systems
- perform behavioral monitoring or anomaly detection

A2SPA addresses **execution control**, not intelligence control.

---

## 5. Security Properties

When correctly implemented, A2SPA provides:
- explicit intent verification at execution time
- resistance to payload mutation
- resistance to replay attacks
- stale-context execution prevention
- constraint enforcement at execution
- cryptographically provable execution accountability

If verification fails, execution **MUST NOT** occur.

---

## 6. Residual Risk

A2SPA does not eliminate all system risk.

Residual risks include:
- incorrect or overly permissive authorization definitions
- compromised key material or signing authorities
- logic errors in verification implementations
- failures in downstream systems after authorized execution

A2SPA ensures that **only explicitly authorized actions execute**.
It does not guarantee that authorized actions are correct or safe.

---

## 7. Summary

Agentic systems fail safely only when execution is verified, not assumed.

A2SPA formalizes execution-time authorization as a required control plane
for systems where autonomous or delayed actions carry material risk.
