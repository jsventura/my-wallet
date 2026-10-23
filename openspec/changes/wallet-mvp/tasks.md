# Tasks

## 1. Application Foundation

- [ ] 1.1 Create the FastAPI application, responsive page shell, and focused test setup; document local startup in `README.md` and verify the app health check and test command run successfully.
- [ ] 1.2 Configure PostgreSQL and Docker Compose, add the initial migration for accounts, wallet payments, QR requests, and idempotency data, and verify a clean database starts and migrates successfully.
- [ ] 1.3 Add validated configuration for session secrets, supported currency, and low-value limit, and define local demo account/merchant provisioning; document the selected defaults and verify invalid configuration fails at startup.

## 2. Wallet Accounts and Onboarding

- [ ] 2.1 Implement local account registration, authentication, and merchant role authorization with server-side ownership checks; add tests for valid and invalid credentials and role boundaries, and document the demo sign-in flow.
- [ ] 2.2 Implement simulated account activation and block payment initiation for inactive accounts; add tests for successful, incomplete, and failed activation and verify the user-facing next-step guidance.

## 3. Wallet Transfers and Confirmation

- [ ] 3.1 Implement recipient and amount validation plus idempotent transfer creation backed by PostgreSQL; test configured-currency and limit boundaries, ineligible recipients, identical retries, conflicting idempotency keys, and concurrent submission.
- [ ] 3.2 Implement the deterministic payment simulator and persisted processing, confirmed, rejected, and unknown outcomes, including reconciliation of the original unknown transaction; test every transition and verify an unknown result never creates a replacement payment.
- [ ] 3.3 Build the mobile-adaptable transfer and status screens with clear outcome-specific guidance; update `README.md` with the simulated payment behavior and verify the screens display transaction references and never label an unknown result as confirmed.

## 4. Merchant QR Payments

- [ ] 4.1 Implement creation and rendering of opaque QR payment requests with configured currency, amount, and expiry; test valid, invalid, expired, and cancelled requests and document the merchant request flow.
- [ ] 4.2 Implement atomic one-payment-per-request authorization and merchant-only status access; test repeated and concurrent scans, cross-merchant access denial, and ensure only a confirmed payment permits fulfillment.
- [ ] 4.3 Build the mobile-adaptable payer review and merchant status screens; verify merchant and amount details appear before authorization and that QR display or non-confirmed status never indicates fulfillment is allowed.

## 5. End-to-End Verification

- [ ] 5.1 Run the full automated suite and exercise onboarding, person-to-person transfer, QR payment, duplicate retry, rejection, and unknown-then-reconciled scenarios in Docker Compose; record commands, transaction references, and outcomes in `evidence.md`.
