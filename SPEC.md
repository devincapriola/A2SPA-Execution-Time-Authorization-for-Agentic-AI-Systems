# A2SPA Specification
## Execution-Time Authorization for Agentic AI Systems
### 1. Purpose
This specification defines **execution-time authorization** as a distinct security control
for agentic AI systems.
Execution-time authorization requires that every irreversible or side-effecting action
be cryptographically verified **at the moment of execution**, not merely at request time.
---
### 2. Problem Statement
Most modern systems enforce security through:
transport-layer encryption (e.g., TLS)
identity and access management (RBAC, OAuth, MFA)
post-execution monitoring and logging
These controls authenticate *who* may request an action,
but typically **assume trust once execution begins**.
In agentic systems with autonomous decision-making, asynchronous execution,
and reduced human oversight, this assumption creates a critical vulnerability.
---
### 3. Definition: Execution-Time Authorization
Execution-time authorization is the cryptographic enforcement that:
a specific action
with specific parameters
under explicit constraints
within a defined validity window
**is authorized at the exact point of execution**, or the action must not execute.
Authorization is bound to the action itself, not inferred from identity or transport context.
---
### 4. Architectural Gap Addressed
A2SPA addresses the gap where:
authorization occurs before execution
execution happens later, elsewhere, or asynchronously
payloads may be mutated, replayed, delayed, or escalated
This gap shifts execution into the primary attack surface.
---
### 5. Protocol Components
A2SPA defines the following components:
#### 5.1 Execution Payload
A structured payload containing:
action type
parameters
permissions and constraints
execution scope
expiration time
nonce or freshness value
#### 5.2 Cryptographic Signature
The execution payload is cryptographically signed,
binding:
who authorized the action
what action is authorized
under which constraints
for how long
#### 5.3 Freshness Guarantees
Nonces and/or timestamps ensure:
replay protection
context freshness
single-use or time-bound execution
#### 5.4 Verification at Execution Boundary
At the execution boundary, the system MUST:
extract the signature
verify payload integrity
validate freshness
enforce constraints
deny execution on failure
#### 5.5 Execution Artifact
Upon successful verification, the system emits
a verifiable execution artifact proving:
who authorized what
when execution occurred
under which constraints
This artifact is audit-ready and immutable.
---
### 6. Placement in the Stack
Execution-time authorization logically resides at:
API endpoints performing writes or side effects
workflow engines at job dequeue or start
infrastructure automation triggers
payment, settlement, or asset transfer execution
privilege or capability issuance paths
It gates the transition from **decision → execution**.
---
### 7. Security Properties
A2SPA enforces:
explicit intent verification
non-repudiation
replay resistance
constraint enforcement
execution accountability
---
### 8. Relationship to Existing Controls
A2SPA does not replace:
TLS (transport security)
IAM (identity and access management)
monitoring or detection systems
It complements them by enforcing authorization
**where those controls typically stop**.
---
### 9. Reference Diagrams
Reference execution flow and control plane diagrams
are provided in the repository `/diagrams` directory.