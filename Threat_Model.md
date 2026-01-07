# A2SPA Threat Model
## Execution-Time Authorization for Agentic AI Systems
---
## Threat Surface Shift
Traditional application security assumes:
execution follows immediately after authorization
context remains stable
payloads are not modified
Agentic AI breaks these assumptions.
Execution may be:
delayed
asynchronous
chained across systems
autonomous
human-unobserved
This shifts the primary attack surface to **execution itself**.
---
## Threats Addressed by A2SPA
### 1. Payload Mutation
**Threat**:
Authorized payload is modified after approval but before execution.
**Mitigation**:
Cryptographic signatures bind payload integrity.
Any mutation invalidates execution.
---
### 2. Replay Attacks
**Threat**:
Previously authorized actions are replayed to trigger unintended execution.
**Mitigation**:
Nonces and freshness windows enforce single-use or time-bound execution.
---
### 3. Delayed Execution with Stale Context
**Threat**:
An action executes long after authorization under changed conditions.
**Mitigation**:
Expiration timestamps and constraint validation at execution time.
---
### 4. Spoofed Agent Actions
**Threat**:
Malicious agents impersonate legitimate execution paths.
**Mitigation**:
Signatures bind authorization to a specific issuer and scope.
---
### 5. Unauthorized Escalation
**Threat**:
Agents exceed their intended permissions during execution.
**Mitigation**:
Explicit constraints are enforced cryptographically at execution.
---
### 6. Assumed Trust in Autonomous Workflows
**Threat**:
Systems assume upstream checks guarantee safe execution.
**Mitigation**:
Execution is gated on real-time verification, not assumptions.
---
## Non-Goals
A2SPA does not attempt to:
secure model training
prevent hallucinations
replace IAM systems
perform behavioral monitoring
Its purpose is **execution control**, not intelligence control.
---
## Security Guarantees
A2SPA enforces:
non-repudiation
integrity
freshness
constraint enforcement
execution accountability
If verification fails, execution must not occur.
---
## Summary
Agentic systems fail safely only when execution is verified, not assumed.
A2SPA formalizes execution-time authorization
as a required control plane for high-stakes autonomous systems.
