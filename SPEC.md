# A2SPA Specification
## Execution-Time Authorization for Agentic AI Systems

---

## 1. Purpose and Scope

This specification defines **execution-time authorization** as a distinct security control
for agentic and autonomous systems.

A2SPA specifies **what MUST be verified at the moment an action executes** in order for that
action to be permitted to run.

This specification:
- defines execution-time authorization guarantees
- defines verification requirements at the execution boundary
- defines execution decision semantics (ALLOW / DENY)

This specification does **not** define implementation details, cryptographic primitives,
key management, orchestration logic, or agent frameworks.

---

## 2. Definitions

**Action**  
An operation that produces side effects, including state changes, external calls,
financial transfers, infrastructure changes, or capability issuance.

**Action Payload**  
The structured data describing a specific action, including parameters, constraints,
and validity conditions.

**Execution Boundary**  
The point at which a decision transitions into an irreversible action.

**Execution-Time Authorization**  
Cryptographic verification that a specific action payload remains authorized,
unmodified, valid, and within constraints **at the exact moment execution occurs**.

---

## 3. Problem Statement

Most modern systems enforce security through:
- transport-layer protection (e.g., TLS)
- identity and access management (e.g., IAM, RBAC, OAuth)
- post-execution monitoring and logging

These controls authenticate **who may request** an action, but typically **assume trust once execution begins**.

In agentic systems with autonomous decision-making, asynchronous execution,
delegation, retries, and reduced human oversight, this assumption creates a critical vulnerability:
actions may execute under conditions that no longer match the original authorization.

---

## 4. Execution-Time Authorization (Normative Definition)

Execution-time authorization requires that:

- a **specific action**
- with **specific parameters**
- under **explicit constraints**
- within a **defined validity window**

**MUST be cryptographically verified at the execution boundary**, or the action **MUST NOT execute**.

Authorization is bound to the **action payload itself**, not inferred from identity,
session, or transport context.

---

## 5. Architectural Gap Addressed

A2SPA addresses the gap where:
- authorization occurs before execution
- execution happens later, elsewhere, or asynchronously
- action payloads may be mutated, replayed, delayed, or escalated

This gap shifts execution into the primary attack surface.

---

## 6. Verification Model

At the execution boundary, the system **MUST** verify the action payload by enforcing:

- **Identity binding** — who authorized the action
- **Integrity** — what exact action was authorized
- **Freshness** — whether the authorization is still valid at execution time
- **Constraints** — whether execution is permitted under the approved scope

If any verification step fails, execution **MUST NOT** occur.

---

## 7. Execution Boundary Semantics

Execution-time authorization gates the transition from **decision → execution**.

The verification step **MUST occur immediately prior to side effects** and **MUST NOT**
be bypassed by retries, delegation, or agent-to-agent handoff.

Upon successful verification, the system **MAY** emit an execution artifact
suitable for audit and non-repudiation.

---

## 8. Security Properties

A2SPA provides the following security properties:

- explicit intent verification
- resistance to payload mutation
- resistance to replay attacks
- stale-context execution prevention
- constraint enforcement at execution time
- execution accountability

---

## 9. Relationship to Existing Controls

A2SPA does **not** replace:
- transport security (TLS)
- identity and access management (IAM)
- monitoring or detection systems

A2SPA complements these controls by enforcing authorization **where they typically stop**:
at the execution boundary.

---

## 10. Reference Diagrams

Reference execution flow and control-plane diagrams
are provided in the repository `./diagrams/` directory.
