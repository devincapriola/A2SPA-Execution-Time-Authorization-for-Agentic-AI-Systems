# Changelog

## v0.9.2 - Implementation-Aligned Review Draft - September 3, 2026

Aligns the protocol repository with the public A2SPA app/API and helper packages `0.1.3`.

- Adds a Markdown specification as the current reviewable text.
- Clarifies that this repository is independent and is not an IETF working-group document.
- Clarifies the product boundary between Agent Verify pre-deployment analysis and A2SPA execution-time authorization.
- Replaces unverified draft claims about Go/Rust reference implementations, Ed25519 baseline requirements, RFC 8785 baseline canonicalization, EEP-issued challenge nonces, and execution tokens with wording that matches the current public implementation.
- Documents the current public payload envelope, signed-byte compatibility profile, RSA SHA-256 signature profile, optional ML-DSA profile, nonce/timestamp replay controls, state-continuity checks, signed execution receipts, public API aliases, and credit-based access behavior.
- Removes the `v0.9.1-clean` Word/PDF artifacts to avoid publishing stale protocol text as current guidance.

## v0.9.1 - External Technical Review Draft - June 28, 2026

Initial public external technical-review publication of the A2SPA Core Protocol Specification.

This version is historical. Use `v0.9.2` for current review against the public A2SPA `0.1.3` implementation.
