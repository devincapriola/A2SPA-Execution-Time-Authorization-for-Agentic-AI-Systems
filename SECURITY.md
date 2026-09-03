# Security Policy

## Scope

This repository contains a **reference specification** for A2SPA
(Agent-to-Secure Payload Authorization).

It does **not** contain:
- production code
- executable implementations
- SDKs or libraries
- deployed services

As such, there are no runtime vulnerabilities, dependencies, or attack
surfaces associated with this repository itself.

---

## Vulnerability Reporting

This repository does **not** operate a bug bounty program.

If you believe you have identified a **conceptual security issue** or
specification-level concern related to execution-time authorization,
you may report it responsibly by contacting the maintainers through
appropriate private channels.

Please do not open public issues for speculative vulnerabilities.

---

## Implementation Security

Security of any A2SPA-based implementation depends on:
- cryptographic choices
- key management practices
- execution environment hardening
- deployment architecture
- operational controls

These concerns are **explicitly out of scope** for this repository.

A2SPA defines **what must be verified at execution time**, not how
verification must be implemented.

The current public A2SPA `0.1.3` compatibility profile uses RSA PKCS#1 v1.5 with SHA-256 over Signed Bytes V1, with optional ML-DSA where the verifier and sender key support it. Unsupported or mismatched profiles must fail closed rather than silently downgrade.

---

## Fail-Closed Behavior and Error Codes

The canonical specification defines a fail-closed invariant: if authorization cannot be verified, execution must not proceed.

Section 11 of [`spec/A2SPA_Core_Protocol_Specification_v0.9.2.md`](spec/A2SPA_Core_Protocol_Specification_v0.9.2.md) describes current decision and HTTP handling. Integrations must accept only `success: true` plus receipt decision `authorized`; malformed responses, timeouts, verifier errors, `refer_hold`, and `denied` are not execution authorization.

---

## Disclaimer

This repository is provided for architectural, research, and technical review
purposes only.

The authors make no guarantees regarding the security, completeness,
or fitness of downstream implementations.

Implementers are responsible for conducting their own security reviews,
threat modeling, and compliance assessments.
