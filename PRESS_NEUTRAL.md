# A2SPA Press and Neutral Description Guide
## Execution-Time Authorization for Agentic AI Systems

---

## Purpose of This Document

This document provides **neutral, factual language** for describing A2SPA
(Agent-to-Secure Payload Authorization).

It is intended for:
- journalists
- analysts
- researchers
- standards bodies
- third-party commentators

The goal is accuracy, not promotion.

---

## One-Sentence Description

A2SPA (Agent-to-Secure Payload Authorization) is a reference protocol that defines **execution-time authorization**—the cryptographic verification that a specific action is explicitly authorized, unmodified, valid, and permitted **at the moment it executes** in agentic and autonomous systems.

---

## Short Neutral Description

A2SPA introduces execution-time authorization as a distinct security control plane for agentic AI systems. It enforces verify-before-execute at the execution boundary, ensuring that autonomous or delayed actions are cryptographically validated immediately prior to producing irreversible effects. A2SPA complements existing transport and identity controls by addressing execution risk in asynchronous and delegated workflows.

---

## What A2SPA Does

A2SPA:
- defines execution-time authorization as a security control
- verifies authorization at the execution boundary
- binds authorization to a specific action payload
- enforces freshness and constraint validation
- produces explicit allow/deny execution outcomes

A2SPA focuses on **whether an action may execute**, not on how agents reason or communicate.

---

## What A2SPA Does Not Do

A2SPA does **not**:
- provide an agent framework
- orchestrate workflows
- manage identities or access roles
- secure transport or encrypt data
- evaluate model correctness or safety
- detect fraud or malicious intent
- monitor behavior or generate alerts
- replace existing security systems

A2SPA is intentionally narrow in scope.

---

## Relationship to Existing Security Layers

- **TLS** secures data in transit.
- **IAM** governs access and identity.
- **A2SPA** governs execution.

TLS and IAM operate prior to execution.
A2SPA operates **at the moment execution occurs**.

---

## Why Execution-Time Authorization Matters

In agentic and autonomous systems, actions may be:
- delayed
- delegated
- retried
- forwarded across agents
- executed without human oversight

Traditional authorization models assume immediate execution.
A2SPA addresses the risk introduced by this assumption.

---

## Common Mischaracterizations to Avoid

When describing A2SPA, avoid stating that it:

- replaces IAM or OAuth
- replaces TLS or encryption
- prevents all security incidents
- guarantees correct agent behavior
- is a model safety or alignment system
- governs agent reasoning or planning

A2SPA addresses **execution control**, not intelligence control.

---

## Intended Audience

A2SPA is relevant to:
- security architects
- platform and infrastructure teams
- agent framework developers
- organizations deploying autonomous systems
- researchers studying AI execution risk

---

## Status and Scope

A2SPA is published as a **reference protocol** and conceptual standard.

It defines required verification guarantees but does not mandate specific
cryptographic algorithms, implementations, or deployment models.

Implementation choices are left to adopters.

---

## Summary Statement

A2SPA defines execution-time authorization as a missing control plane in modern autonomous systems. It enforces the principle that **actions must be verified at the moment they execute**, not merely assumed safe because they were authorized earlier.

This document exists to ensure A2SPA is described clearly, accurately,
and without overstatement.
