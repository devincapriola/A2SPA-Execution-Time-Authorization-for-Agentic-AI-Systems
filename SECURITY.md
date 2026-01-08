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

---

## Disclaimer

This repository is provided for architectural, research, and standards
discussion purposes only.

The authors make no guarantees regarding the security, completeness,
or fitness of downstream implementations.

Implementers are responsible for conducting their own security reviews,
threat modeling, and compliance assessments.
