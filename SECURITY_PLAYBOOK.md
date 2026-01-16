# SECURITY_PLAYBOOK.md
**A2SPA — Agent-to-Secure Payload Authorization**

## Purpose

This document defines how to reason about security in the A2SPA system.

A2SPA exists to close a specific and dangerous gap in modern AI and agentic systems: **execution without cryptographic authorization**.

Most systems today authenticate *who* is calling a service, but do not cryptographically prove *what* was authorized **at the moment an action executes**. A2SPA is designed to enforce authorization at execution time, not before and not after.

This playbook explains:
- The threat model A2SPA is built to address
- The security boundaries and assumptions
- Required decision gates
- What must always be verified
- What must never happen

This document is intentionally **public-safe**. It contains no secrets, private endpoints, credentials, or internal-only diagrams.

---

## Core Security Principle

> **Identity ≠ Intent.  
Execution is the attack surface.**

A2SPA treats execution as the moment of highest risk and enforces cryptographic authorization *at that moment*.

---

## What A2SPA Is (Security View)

A2SPA is an **execution-time authorization layer** that:
- Verifies a signed payload immediately before execution
- Ensures the payload has not been altered, replayed, or exceeded its scope
- Enforces permissions, timing, and constraints at runtime
- Produces a verifiable audit trail for every authorized or rejected action

A2SPA does **not** decide *what* an agent should do.  
It decides **whether an action is allowed to execute right now**.

---

## Threat Model

A2SPA assumes the following threats are **real and common**:

### 1. Payload Mutation
An authorized instruction is modified between intent creation and execution.

### 2. Replay Attacks
A previously valid instruction is reused outside its intended time or context.

### 3. Scope Escalation
An agent executes actions beyond what was explicitly authorized.

### 4. Delayed Execution Abuse
An instruction is executed later than intended, under different conditions.

### 5. Agent Impersonation
An attacker or compromised agent submits instructions that appear valid but were not authorized.

### 6. Orchestration Bypass
Logic executes even when verification fails or is skipped.

### 7. Post-hoc Auditing Fallacy
Systems that log actions after execution but cannot cryptographically prove authorization beforehand.

---

## Explicit Non-Goals

A2SPA does **not** attempt to:
- Replace IAM, OAuth, or key management systems
- Perform user authentication or identity verification
- Decide business logic or agent reasoning
- Prevent all bugs, misconfiguration, or bad prompts
- Secure models themselves (weights, training data, alignment)

A2SPA secures **execution**, not intelligence.

---

## Security Boundaries

### Inside the A2SPA Boundary
- Payload hash verification
- Signature validation
- Permission enforcement
- Nonce / replay protection
- Expiry and timing checks
- Execution authorization decision
- Deterministic logging of outcomes

### Outside the A2SPA Boundary
- Agent logic and reasoning
- Prompt generation
- Model inference
- UI/UX layers
- Downstream system correctness

If something executes, A2SPA assumes it **must be verified first**.

---

## Required Execution Decision Gates

Before **any action executes**, all of the following must be true:

1. **Payload Integrity**
   - Payload hash matches the signed hash
   - No mutation is tolerated

2. **Signature Validity**
   - Signature cryptographically matches the payload
   - Signing identity is recognized

3. **Permission Scope**
   - Action requested is explicitly allowed
   - Directional permissions (send / receive / execute) are enforced

4. **Temporal Validity**
   - Timestamp is valid
   - Payload has not expired

5. **Replay Protection**
   - Nonce has not been used before
   - Reuse results in rejection

6. **Agent State**
   - Agent is enabled and authorized
   - Disabled agents cannot execute

If **any gate fails**, execution **must not occur**.

---

## Failure Handling Rules

- Verification failure **must block execution**
- Failures must be logged
- Failures must be deterministic and explainable
- Silent fallback execution is forbidden
- “Best effort” execution is forbidden

---

## Logging & Audit Expectations

Every authorization attempt should produce a record containing:
- Payload identifier (or hash reference)
- Verification result (pass / fail)
- Reason for failure (if applicable)
- Timestamp
- Agent identity
- Permission scope evaluated

Logs are security artifacts, not analytics.

---

## Common Anti-Patterns (What Must Never Happen)

❌ Executing before verification  
❌ Executing if verification fails  
❌ Allowing unsigned or partially signed payloads  
❌ Reusing payloads without nonce checks  
❌ Treating logs as proof of authorization  
❌ Assuming transport security equals execution security  
❌ Bypassing authorization for “trusted” agents  

If you see any of the above, it is a **security bug**, not a feature.

---

## How to Reason About Changes

When proposing or reviewing changes, always ask:

1. Does this change affect **when or how execution occurs**?
2. Could this allow execution without verification?
3. Does this weaken any decision gate?
4. Does this introduce ambiguity in authorization?
5. Would an attacker benefit from this change?

If the answer is unclear, assume **risk** until proven otherwise.

---

## Security Review Triggers

A security review is required if a change:
- Alters payload structure
- Alters signing or verification logic
- Changes permission semantics
- Touches replay protection or timing logic
- Introduces new execution paths
- Adds “temporary” bypasses

---

## Design Intent Summary

A2SPA exists because:
- Execution is power
- Power without authorization is a vulnerability
- Authorization must be cryptographic
- Authorization must occur at execution time
- Verification must be mandatory, not optional

If execution happens without A2SPA verification, the system is **operating outside its security model**.

---

## Final Rule

> **If it runs, it must be authorized.  
If it isn’t authorized, it must not run.**

That rule is non-negotiable.
