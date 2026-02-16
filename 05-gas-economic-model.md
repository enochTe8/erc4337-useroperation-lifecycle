# 05 - Gas Accounting & Economic Model

Gas is the lifeblood of Ethereum, and **EIP‑4337** introduces a refined accounting model to ensure fairness, sustainability, and resilience against abuse. This section explains how gas is measured, charged, and settled across the lifecycle of a `UserOperation`.

---

## 5.1 preVerificationGas
- **Definition**: The gas consumed by the bundler to validate the `UserOperation` before execution.
- **Purpose**:
  - Covers calldata size and signature verification costs.
  - Ensures bundlers are compensated for the overhead of including operations.
- **Impact**: Larger calldata or complex signatures increase `preVerificationGas`, incentivizing efficient encoding and aggregation.

---

## 5.2 maxFeePerGas / maxPriorityFeePerGas
- **maxFeePerGas**: The absolute maximum fee the sender is willing to pay per unit of gas.
- **maxPriorityFeePerGas**: The tip offered to incentivize bundlers to include the operation.
- **Mechanics**:
  - Bundlers select operations where `maxFeePerGas` covers network base fees.
  - Priority fees reward bundlers for inclusion, similar to EIP‑1559 dynamics.
- **Outcome**: Ensures competitive inclusion while protecting users from overpayment.

---

## 5.3 Deposit System
- **EntryPoint Deposits**:
  - Accounts and paymasters must maintain deposits in the `EntryPoint`.
  - Deposits are used to settle gas costs during validation and execution.
- **Stake Requirements**:
  - Paymasters must stake funds to prevent griefing attacks.
  - Stake acts as collateral, slashed if malicious behavior is detected.
- **Economic Safety**: Deposits guarantee that gas liabilities are covered, even if execution fails.

---

## 5.4 Who Pays When Validation Fails
- **Self‑Funded Accounts**:
  - Gas consumed during validation is deducted from the account’s deposit.
- **Sponsored Operations**:
  - Paymasters cover validation gas, even if the operation fails.
- **Mitigation of DoS Risks**:
  - Malicious actors cannot endlessly submit invalid operations without cost.
  - Deposits and stakes ensure economic accountability at every stage.

---

## 5.5 Summary
The gas accounting model in **EIP‑4337** balances efficiency and security:
- `preVerificationGas` compensates bundlers for overhead.
- Fee parameters (`maxFeePerGas`, `maxPriorityFeePerGas`) align incentives with EIP‑1559.
- Deposits and stakes guarantee settlement and prevent abuse.
- Validation failures are still charged, ensuring denial‑of‑service attacks remain economically infeasible.

This economic framework is the backbone of account abstraction, ensuring that every `UserOperation` is not only validated and executed, but also paid for in a way that sustains the network’s integrity.
