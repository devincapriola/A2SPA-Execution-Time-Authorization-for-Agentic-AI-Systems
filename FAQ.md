# A2SPA FAQ
## Execution-Time Authorization for Agentic AI Systems
---
### What problem does A2SPA actually solve?
A2SPA solves the problem of **assumed trust at execution time**.
Most systems authenticate a request, then assume the action remains safe when it executes.
In agentic systems, execution may happen later, asynchronously, or in a different context.
A2SPA ensures actions are **cryptographically re-verified at execution**, or they do not run.
---
### How is this different from TLS?
TLS secures data **in transit**.
It does not prove:
who authorized a specific action
what constraints applied
whether the payload was altered
whether execution is still valid
A2SPA operates **after TLS**, at the execution boundary.
---
### How is this different from RBAC, OAuth, or MFA?
RBAC, OAuth, and MFA authenticate **who may request** an action.
They do not cryptographically bind:
the exact action
parameters
constraints
freshness
to execution itself.
A2SPA binds authorization to the **action payload**, not the identity session.
---
### Is A2SPA required for all AI systems?
No.
A2SPA becomes necessary when AI systems:
act autonomously
execute asynchronously
perform irreversible actions
operate without human-in-the-loop approval
Low-risk, read-only, or advisory systems may not require it.
---
### What happens if verification fails?
The action **must not execute**.
Failure conditions include:
invalid signature
expired payload
nonce replay
constraint violation
payload mutation
Execution-time authorization is binary: **verify or deny**.
---
### Is A2SPA a replacement for existing security controls?
No.
A2SPA complements:
TLS
IAM
monitoring
logging
detection systems
It fills the gap **between decision and execution**.
---
### Is A2SPA an implementation or a concept?
A2SPA defines:
a protocol
a control plane
an architectural pattern
Implementations may vary, but the enforcement model is consistent:
**cryptographic authorization at execution time**.
---
### Why does this matter now?
Because AI systems are moving from:
**“suggest” → “execute”**
When agents can move money, change infrastructure, or grant access,
execution becomes the attack surface.
---
### What proof does A2SPA produce?
A2SPA produces a verifiable execution artifact proving:
who authorized the action
what was authorized
under which constraints
when it executed
This enables auditability, compliance, and forensic analysis.
---
### Is this the same as policy engines or workflow approvals?
No.
Policy engines often evaluate rules **before execution**.
A2SPA enforces cryptographic verification **at execution**.
It is designed for autonomous, distributed, and delayed execution contexts.
