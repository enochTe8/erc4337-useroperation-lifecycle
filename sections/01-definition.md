# 1. Formal Definition of a UserOperation

## 1.1 Protocol Context

A UserOperation is a structured, off-chain transaction object defined by ERC-4337 that represents a user's intent to execute an action through a Smart Account.

Unlike a legacy Ethereum transaction submitted directly to the canonical mempool, a UserOperation is:

- Broadcast to a specialized Account Abstraction (AA) mempool
- Collected by a bundler
- Validated and executed via the EntryPoint contract

The UserOperation introduces an intent-based execution model where the externally owned account (EOA) is no longer required to directly initiate transactions. Instead, Smart Accounts define their own validation logic and execution rules.

---

## 1.2 Architectural Motivation

The UserOperation exists to solve the following limitations of legacy Ethereum transactions:

1. Rigid signature validation (ECDSA-only model)
2. Mandatory ETH payment for gas
3. Lack of programmable account logic
4. Poor onboarding experience for non-technical users

By separating transaction intent from execution inclusion, ERC-4337 enables:

- Custom signature schemes (e.g., multisig, social recovery, passkeys)
- Gas sponsorship via paymasters
- Programmable validation logic
- Improved wallet UX abstraction

The UserOperation is therefore not merely a transaction alternative, but an abstraction layer over Ethereum’s execution model.

---

## 1.3 Off-Chain vs On-Chain Responsibilities

The lifecycle of a UserOperation is split between off-chain infrastructure and on-chain contracts.

### Off-Chain Phase

- Constructed by a wallet
- Signed according to the Smart Account’s rules
- Submitted to a bundler
- Simulated via `simulateValidation()`

### On-Chain Phase

- Included inside `handleOps()` by the bundler
- Validated via `validateUserOp()` in the Smart Account
- Executed via the EntryPoint contract

This separation ensures:

- Deterministic validation
- DoS resistance
- Bundler payment guarantees

---

## 1.4 High-Level Lifecycle Overview

The simplified flow of a UserOperation is:

1. User signs UserOperation
2. Bundler receives and simulates validation
3. Bundler includes it in a bundle
4. EntryPoint validates and executes
5. Gas is settled and refunds processed

A detailed lifecycle breakdown is provided in Section 03.

