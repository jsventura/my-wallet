# Spec Delta

## Purpose

Lets a wallet user make a low-value transfer and understand reliably whether it is processing, confirmed, rejected, or unresolved without accidentally paying twice.

## ADDED Requirements

### Requirement: User can submit a low-value payment
The system MUST allow an active user to submit a payment to another registered wallet account in the configured currency up to the configured low-value limit, and MUST explain validation failures without representing them as completed payments.

#### Scenario: Payment is within the configured limit
- **WHEN** an active user submits a valid payment to another registered wallet account with an amount greater than zero and no greater than the configured limit
- **THEN** the system accepts it for processing and provides a transaction reference

#### Scenario: Payment exceeds the configured limit
- **WHEN** an active user submits a payment above the configured limit or in an unsupported currency
- **THEN** the system rejects the request before processing and explains that the payment was not made

#### Scenario: Recipient is not an eligible wallet account
- **WHEN** an active user submits a payment to an unknown or inactive recipient account
- **THEN** the system rejects the request before processing and explains that the recipient cannot receive the payment

### Requirement: User can distinguish payment outcomes
The system MUST present payment status using distinct processing, confirmed, rejected, and unknown outcomes, and MUST report confirmation only after the payment has a definitive successful result.

#### Scenario: Payment is definitively successful
- **WHEN** the simulated processor returns a definitive successful result
- **THEN** the transaction is shown as confirmed with its reference and amount

#### Scenario: Payment is definitively rejected
- **WHEN** the simulated processor returns a definitive rejection
- **THEN** the transaction is shown as rejected and the user is told that the payment did not complete

#### Scenario: Processing has not reached a definitive result
- **WHEN** processing is ongoing or the system cannot determine the result
- **THEN** the transaction is shown as processing or unknown, never as confirmed or definitively rejected

### Requirement: User can safely resolve an unknown payment outcome
The system MUST retain an unknown payment for status lookup and MUST NOT initiate a second transfer solely because the original result is unknown.

#### Scenario: User checks an unknown payment
- **WHEN** the user requests the status of a payment with an unknown result
- **THEN** the system returns the same transaction and its latest known status, and directs the user to check again rather than submit a replacement payment

#### Scenario: Unknown result is reconciled
- **WHEN** the system obtains a definitive result for an unknown payment
- **THEN** it updates that transaction to confirmed or rejected and presents the result to the user

### Requirement: Repeated submission does not create duplicate payments
The system MUST make payment submission idempotent so that repeating the same logical request does not create an additional transfer.

#### Scenario: Same request is submitted again
- **WHEN** a client retries a payment submission with the same idempotency key and request details
- **THEN** the system returns the existing transaction and does not process another transfer

#### Scenario: Idempotency key is reused with different details
- **WHEN** a client submits different payment details using an idempotency key already associated with another request
- **THEN** the system rejects the conflicting request and leaves the original transaction unchanged

### Requirement: User receives actionable payment feedback
The system MUST provide a clear next step for each payment outcome without implying success when the payment is not confirmed.

#### Scenario: Payment is confirmed
- **WHEN** the payment is confirmed
- **THEN** the system tells the user that the payment completed and provides the transaction reference

#### Scenario: Payment is rejected
- **WHEN** the payment is rejected
- **THEN** the system says the payment did not complete and gives a safe next step, such as reviewing the details before starting a new payment

#### Scenario: Payment outcome is unknown
- **WHEN** the payment outcome is unknown
- **THEN** the system tells the user not to submit a replacement payment and offers status lookup as the next step
