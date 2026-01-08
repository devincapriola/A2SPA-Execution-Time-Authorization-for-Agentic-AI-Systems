# A2SPA Standard
## Terminology and Normative Definitions

---

## 1. Purpose

This document defines the normative terminology and conceptual boundaries
used throughout the A2SPA (Agent-to-Secure Payload Authorization) specification.

Its purpose is to ensure consistent interpretation of execution-time
authorization concepts across implementations, reviews, and standards
discussions.

Unless otherwise stated, key words such as **MUST**, **MUST NOT**, **SHOULD**,
and **MAY** are to be interpreted as described in RFC 2119.

---

## 2. Core Concepts

### 2.1 Execution-Time Authorization

**Execution-time authorization** is the verification, immediately prior to
execution, that a specific action was explicitly authorized, remains unmodified,
is still valid and fresh, and is permitted to execute under defined constraints.

Execution-time authorization evaluates **actions**, not requests or identities.

Authorization is enforced at the moment an action produces irreversible side
effects.

---

### 2.2 Execution Boundary

The **execution boundary** is the point at which a decision transitions into an
irreversible action.

Examples include:
- issuing an external API call
- modifying system or application state
- transferring value
- provisioning infrastructure
- granting capabilities or permissions

Authorization checks that occur prior to this boundary are insufficient to
guarantee safe execution in autonomous or asynchronous systems.

---

### 2.3 Action

An **action** is a discrete operation that produces irreversible effects.

An action is defined by:
- its semantic intent
- its execution parameters
- its scope and constraints
- its authorization context

Actions are distinct from:
- messages
- plans
- prompts
- reasoning steps
- internal model outputs

Only actions require execution-time authorization.

---

### 2.4 Authorization Payload

An **authorization payload** is the cryptographically protected representation
of an approved action.

At minimum, an authorization payload binds:
- the action definition
- the authorizing identity or authority
- execution constraints
- freshness parameters

The payload is the unit of verification at the execution boundary.

---

## 3. Verification Properties

### 3.1 Identity Binding

**Identity binding** ensures that an action is linked to a specific authorizing
entity.

Identity binding does not imply trust in the executing agent or system.
It establishes provenance of authorization only.

---

### 3.2 Integrity

**Integrity** ensures that the authorization payload has not been altered since
approval.

Any modification to the payload invalidates authorization.

---

### 3.3 Freshness

**Freshness** ensures that authorization is valid at the time of execution.

Freshness MAY be enforced via:
- expiration timestamps
- nonces
- monotonic counters
- validity windows

Stale authorizations MUST NOT execute.

---

### 3.4 Constraints

**Constraints** define the permitted scope of execution.

Constraints MAY include:
- parameter bounds
- resource limits
- environmental conditions
- execution context requirements

Execution outside approved constraints MUST be denied.

---

## 4. Control Planes

### 4.1 Transport Security (TLS)

Transport security protects data in transit between endpoints.

Transport security does **not** guarantee that:
- an action remains unchanged until execution
- authorization remains valid at execution time
- execution constraints are enforced

TLS terminates before execution.

---

### 4.2 Identity and Access Management (IAM)

IAM governs **who may request or access** systems and capabilities.

IAM does **not** guarantee that:
- a specific action is authorized
- execution context remains valid
- authorization survives delegation or delay

IAM terminates before execution.

---

### 4.3 Execution-Time Control Plane (A2SPA)

A2SPA defines an **execution-time control plane** that enforces authorization
at the execution boundary.

A2SPA operates independently of:
- transport protocols
- identity systems
- agent communication mechanisms

Execution MUST NOT occur unless verification succeeds.

---

## 5. Relationship to Agent Protocols

### 5.1 Agent-to-Agent (A2A)

Agent-to-Agent protocols define how agents communicate.

They do not define execution authorization.

A2SPA applies **after** communication, immediately before execution.

---

### 5.2 Model Context Protocol (MCP)

MCP defines how tools and capabilities are exposed to models and agents.

Tool visibility does not imply execution authorization.

A2SPA governs whether tool execution is permitted.

---

## 6. Deny-by-Default Execution

A2SPA mandates a deny-by-default execution posture.

If verification cannot be completed successfully:
- execution MUST NOT occur
- failure MUST be explicit
- execution MUST NOT degrade silently

Execution without verification is a protocol violation.

---

## 7. Out-of-Scope Concepts

A2SPA does not define:
- cryptographic algorithms
- key management systems
- payload serialization formats
- agent reasoning architectures
- orchestration frameworks
- logging or monitoring systems

These concerns are implementation-specific.

---

## 8. Summary

A2SPA standardizes execution-time authorization as a distinct security control
plane.

It enforces a single invariant:

**No action executes without explicit, verifiable authorization at the moment
of execution.**
