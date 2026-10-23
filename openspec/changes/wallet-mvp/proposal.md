# Proposal

## Why

The project has only a starter structure, while its MVP goal depends on users and merchants understanding whether a small payment can proceed and whether it completed. This change defines a coherent, demonstrable wallet flow without connecting to real-money payment services.

## What Changes

- Introduce a short wallet onboarding flow with simulated account activation before the user can pay.
- Define low-value transfers between wallet users with outcomes that distinguish in-progress, confirmed, rejected, and unknown results; prevent duplicate processing and provide safe guidance after timeouts.
- Introduce merchant QR payment requests with amount and expiry, and a trustworthy transaction status that governs when a merchant may fulfill an order.
- Establish a proposed implementation baseline of a smartphone-adaptable web experience, a Python/FastAPI backend, PostgreSQL persistence, and Docker Compose for local services.
- Keep payment processing simulated and deterministic in the MVP. Exclude real-money movement, real identity verification/KYC, and AI agents in the payment decision path.

## Capabilities

### New Capabilities
- `wallet-onboarding`: Guided account registration and simulated activation, with payment access gated on activation.
- `payment-confirmation`: Low-value simulated transfers, definitive result states, duplicate prevention, and safe handling of unknown outcomes.
- `merchant-qr-payments`: Expiring QR payment requests and merchant-visible transaction status for fulfillment decisions.

### Modified Capabilities

None.

## Impact

This change establishes requirements and design for the currently empty application structure. Expected implementation impact includes the Python dependencies, Docker Compose services, backend/API, smartphone-adaptable user and merchant interfaces, persistence schema, tests, and demonstration evidence. No existing API or payment integration is present to preserve. Currency and the configurable low-value threshold must be selected as part of implementation configuration before payment flows can be exercised.
