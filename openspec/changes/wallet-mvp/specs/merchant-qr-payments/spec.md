# Spec Delta

## Purpose

Lets a merchant request a wallet payment through an expiring QR code and verify the linked transaction's authoritative status before fulfilling an order.

## ADDED Requirements

### Requirement: Merchant can create an expiring QR payment request
The system MUST let an authorized merchant create a payment request with a positive amount, configured currency, and expiry, and provide a QR code that identifies that request.

#### Scenario: Valid request is created
- **WHEN** a merchant submits a valid amount and expiry
- **THEN** the system creates a pending request and provides its QR code and request reference

#### Scenario: Invalid request is submitted
- **WHEN** a merchant submits a non-positive amount, unsupported currency, or invalid expiry
- **THEN** the system rejects the request and explains which details must be corrected

### Requirement: Payer can review a QR request before paying
The system MUST show the payer the merchant identity, amount, currency, and request validity before the payer authorizes payment.

#### Scenario: Payer scans a valid request
- **WHEN** an active wallet user opens a valid QR payment request
- **THEN** the system displays the merchant identity, exact amount, currency, and expiry for review before payment authorization

#### Scenario: Payer opens an expired or unavailable request
- **WHEN** a payer opens a QR request that is expired, cancelled, or already paid
- **THEN** the system explains that the request cannot be paid and does not initiate a payment

### Requirement: QR payment request is linked to a single payment
The system MUST associate a QR request with at most one payment and MUST prevent payment against a request after expiry, cancellation, or successful payment.

#### Scenario: Payment is authorized for an open request
- **WHEN** an active payer authorizes an open QR request before its expiry
- **THEN** the system creates a payment linked to that request and updates the request with the payment's current status

#### Scenario: Concurrent or repeated authorization targets one request
- **WHEN** the same QR request is authorized more than once
- **THEN** the system associates at most one payment with the request and returns its existing payment status for subsequent attempts

#### Scenario: Request expires or is cancelled before authorization
- **WHEN** a payer attempts authorization after a request has expired or been cancelled
- **THEN** the system rejects authorization and creates no payment

### Requirement: Merchant can verify payment status for its own requests
The system MUST let an authorized merchant view the latest status and reference of payments linked to that merchant's requests, and MUST prevent the merchant from viewing another merchant's requests.

#### Scenario: Merchant checks its request
- **WHEN** a merchant requests the status of one of its QR payment requests
- **THEN** the system returns the request and linked payment's latest status and transaction reference, if one exists

#### Scenario: Merchant attempts to view another merchant's request
- **WHEN** a merchant requests a QR payment request owned by a different merchant
- **THEN** the system denies access without revealing its payment details

### Requirement: Merchant fulfills only after payment confirmation
The system MUST communicate that a QR code or payment request is not proof of payment and that fulfillment is permitted only when its linked payment is confirmed.

#### Scenario: Linked payment is confirmed
- **WHEN** the merchant views a QR request whose linked payment is definitively confirmed
- **THEN** the system displays a clear confirmed status and indicates that the merchant may fulfill the order

#### Scenario: Payment is pending, rejected, or unknown
- **WHEN** the merchant views a request whose linked payment is not confirmed
- **THEN** the system displays the current non-confirmed status and indicates that the merchant must not fulfill the order based on that request
