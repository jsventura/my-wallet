# Design

## Context

The repository is a starter skeleton: it has no application source, tests, API, or persistence schema. `requirements.txt` and `docker-compose.yml` are placeholders. The proposal and capability specs define the required behavior; this design establishes an initial implementation shape for them.

## Goals / Non-Goals

**Goals:**
- Keep payment and QR request state authoritative on the server.
- Make the MVP runnable locally and exercise success, rejection, duplicate, timeout/unknown, and reconciliation scenarios.
- Provide a mobile-adaptable user and merchant experience without introducing an unnecessary client build system.

**Non-Goals:**
- Connecting to a real payment processor or moving real money.
- Real KYC, identity proofing, or AI-driven payment decisions.
- Building a general-purpose multi-currency ledger or production-grade banking platform.

## Decisions

### One Python web application with server-rendered responsive pages

Use FastAPI for HTTP routes and server-rendered HTML with responsive CSS for wallet and merchant screens. Keep account, payment, and merchant-request operations in separate modules behind the routes, with shared validation and persistence boundaries.

This fits the existing Python and Docker placeholders and avoids maintaining a separate frontend build and deployment pipeline for a small demonstration. A separate JavaScript SPA can be reconsidered if the UI grows beyond the MVP.

### PostgreSQL is the system of record

Persist accounts, account activation state, merchant roles, payment requests, transactions, idempotency keys, and status transitions in PostgreSQL. Apply related updates in database transactions and enforce uniqueness for idempotency keys and the one-payment-per-QR-request relationship. Use Docker Compose for the local application and database.

An in-memory store would be simpler initially but could lose or disagree on transaction state across restarts and would not exercise the concurrency guarantees required for duplicate prevention.

### Simulated payment processing with explicit states

Use a deterministic processor adapter rather than a real provider. It must support repeatable outcomes for successful, rejected, still-processing, and unknown payments so the UI and tests can demonstrate each state. Keep unknown outcomes persisted; a later simulator reconciliation operation resolves the original transaction instead of submitting a replacement.

The simulator's outcome controls are for local demonstration and automated tests only. They must not be presented as real payment results. The server returns confirmation only after the persisted transaction reaches a definitive successful state.

### Idempotent transaction creation

Require an idempotency key for payment creation. Store the key with a fingerprint of the normalized request and the resulting transaction. A retry with the same key and details returns the original transaction; reusing the key with different details is a conflict. Database constraints and atomic transactions protect the behavior under concurrent requests.

### QR codes carry opaque request references, not proof of payment

Encode an opaque, non-guessable payment-request reference in each QR code; keep amount, merchant, expiry, and payment status in the database. Validate expiry and request state on the server at authorization time. Link no more than one transaction to a request, and derive the merchant-facing status from that transaction rather than from the QR scan or client state.

### Minimal authorization protects account and merchant boundaries

Use application-managed sessions for wallet users and merchants, with account ownership checked on every payment and request lookup. Merchant request creation and status access require the merchant role and are limited to that merchant's own requests. Simulated activation is separate from authentication and does not imply real identity verification.

Use a basic local credential flow for the MVP rather than an external identity provider. The exact minimum registration fields and demo merchant provisioning can be finalized during implementation setup without changing the payment-state contract.

### Currency and low-value limit are configuration

Make the supported currency and maximum low-value payment configurable, validate them server-side, and display the configured currency consistently. Choose the demonstration currency and limit before running payment scenarios; do not embed a guessed threshold in the payment logic.

## Risks / Trade-offs

- [A simulator may be mistaken for a real payment service] -> Label the environment and all simulated outcomes clearly; keep real provider integration out of scope.
- [Unknown outcomes can leave users uncertain] -> Persist the transaction reference, provide status lookup, and reconcile the same transaction rather than retrying payment creation.
- [Concurrent QR scans or retries could create duplicate transfers] -> Enforce idempotency and one-payment-per-request constraints in PostgreSQL transactions, and test concurrent submissions.
- [A compromised session could expose account or merchant data] -> Require server-side ownership and role checks on each read and write; do not rely on unguessable identifiers alone.
- [Configuration drift can make payment validation inconsistent] -> Load currency and amount limit from a single validated configuration source and use it for both UI display and server validation.

## Migration Plan

There is no existing application data or API to migrate. Add the initial database schema and migration, configure local services in Docker Compose, and start with an empty or explicitly seeded demonstration database. Rollback for this pre-release MVP is to stop the application and database and recreate the disposable local database from the prior schema; preserve any evidence needed before resetting demo data.
