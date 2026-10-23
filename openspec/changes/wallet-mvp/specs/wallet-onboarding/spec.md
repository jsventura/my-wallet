# Spec Delta

## Purpose

Lets a smartphone user create and activate a wallet account through a guided flow, and clearly communicates when the account is ready to make payments.

## ADDED Requirements

### Requirement: User can register a wallet account
The system MUST let a user submit the minimum required registration details and clearly identify missing or invalid details without creating an unusable account.

#### Scenario: Registration details are accepted
- **WHEN** a user submits all required valid registration details
- **THEN** the system creates an account in an inactive state and presents the next activation step

#### Scenario: Registration details are incomplete or invalid
- **WHEN** a user submits registration details that are missing or invalid
- **THEN** the system identifies the fields that need correction and does not activate the account

### Requirement: User can activate a registered account
The system MUST provide a simulated activation step and communicate whether activation succeeded or what action remains.

#### Scenario: Activation succeeds
- **WHEN** the user completes the simulated activation step successfully
- **THEN** the account becomes active and the user is told that payments are available

#### Scenario: Activation is not completed
- **WHEN** the user leaves or fails the activation step
- **THEN** the account remains inactive and the system identifies activation as the next required action

### Requirement: Only active accounts can initiate payments
The system MUST prevent an inactive account from initiating a payment and MUST direct its owner to complete activation.

#### Scenario: Inactive user attempts payment
- **WHEN** a user with an inactive account attempts to initiate a payment
- **THEN** the system does not create a payment and directs the user to account activation

#### Scenario: Active user proceeds to payment
- **WHEN** a user with an active account initiates a payment
- **THEN** the system permits the request to continue to payment validation
