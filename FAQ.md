# A2SPA FAQ
## Execution-Time Authorization for Agentic AI Systems

---

## What problem does A2SPA solve?

A2SPA solves the security gap that occurs **between authorization and execution**
in agentic and autonomous systems.

Modern AI systems increasingly:
- plan actions ahead of time
- delegate execution across agents
- execute actions asynchronously or after delay

Existing security layers authorize requests or identities **before execution**.
They do not re-verify that a specific action is still authorized **when it actually runs**.

A2SPA enforces authorization at the execution boundary.

---

## What is execution-time authorization?

Execution-time authorization is the cryptographic verification that:
- a specific action was explicitly authorized
- the action has not been modified
- the authorization is still valid and fresh
- execution remains within approved constraints

This verification occurs **immediately before execution**, not when a request is received.

If verification fails, execution does not occur.

---

## How is A2SPA different from IAM or RBAC?

IAM and RBAC answer the question:

> “Who is allowed to access or request something?”

A2SPA answers a different question:

> “Is this exact action authorized to execute right now?”

IAM controls access.
A2SPA controls execution.

IAM decisions may occur minutes, hours, or systems away from execution.
A2SPA verifies authorization at the moment irreversible side effects occur.

---

## How is A2SPA different from TLS?

TLS secures data **in transit** between systems.

TLS does not:
- bind authorization to a specific action
- prevent payload mutation after transport
- enforce freshness at execution
- validate constraints at runtime

TLS terminates before execution.
A2SPA operates at execution.

They are complementary, not interchangeable.

---

## Is A2SPA an agent framework?

No.

A2SPA does not:
- define agent architectures
- control planning or reasoning
- orchestrate workflows
- manage agent lifecycles

A2SPA is a **security control plane**, not an agent framework.

It applies to any system that executes actions—agentic or otherwise.

---

## Does A2SPA replace MCP or A2A protocols?

No.

- **A2A** defines how agents communicate.
- **MCP** defines how tools and capabilities are exposed.

Neither protocol enforces execution authorization.

A2SPA applies **after communication and discovery**, immediately before execution.

Tool visibility or message receipt does not imply permission to execute.

---

## Why is execution-time authorization necessary for agents?

Agentic systems violate assumptions of traditional security models.

Actions may be:
- delayed
- delegated
- retried
- chained across systems
- executed by different components than those that requested them

Authorization performed earlier cannot guarantee safe execution later.

A2SPA ensures intent is verified **at the moment it matters**.

---

## What kinds of actions should be protected by A2SPA?

Any action that produces irreversible side effects, including:
- external API calls
- financial transactions
- state changes
- infrastructure modification
- capability issuance
- permission grants

Internal reasoning steps do not require execution-time authorization.
Actions do.

---

## What happens if verification fails?

Execution **must not** occur.

A2SPA mandates a deny-by-default posture:
- no silent degradation
- no fallback execution
- no assumed trust

Failure must be explicit and auditable.

---

## Does A2SPA prevent hallucinations or bad decisions?

No.

A2SPA does not evaluate correctness, intelligence, or intent quality.

It ensures that **whatever action executes** was explicitly authorized
and remains valid at execution time.

Reasoning quality is an upstream concern.

---

## Does A2SPA require human approval for every action?

No.

Authorization may be:
- human-approved
- policy-approved
- system-approved
- agent-approved within defined constraints

A2SPA enforces verification, not who performs approval.

---

## Is A2SPA tied to a specific cryptographic algorithm?

No.

A2SPA defines **what must be verified**, not **how it is implemented**.

Cryptographic primitives, key management, and serialization formats
are implementation choices left to adopters.

---

## Is A2SPA a standard or an implementation?

This repository defines a **reference protocol and specification**.

It is intended to:
- guide implementations
- support security reviews
- inform standards discussions
- establish consistent terminology

It is not a software library.

---

## Who should care about A2SPA?

A2SPA is relevant to:
- security architects
- AI platform teams
- agent framework builders
- infrastructure engineers
- organizations deploying autonomous or asynchronous systems

If a system executes actions without re-verification at execution time,
it has an execution-time security gap.

---

## What is the core invariant of A2SPA?

**No action executes without explicit, verifiable authorization
at the moment of execution.**

That is the entire protocol.
