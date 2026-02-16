# 04 - Execution Flow

Validation is the gatekeeper, but **execution is where intent becomes reality**. In EIP‑4337, the execution flow is carefully engineered to balance flexibility for accounts, accountability for paymasters, and resilience for the network. The `EntryPoint` contract orchestrates this stage through its `handleOps` function, ensuring that every validated `UserOperation` is processed fairly, securely, and economically.

---

## 4.1 handleOps — The Conductor
Think of `handleOps` as the conductor of an orchestra:
- **Batch Inclusion**: Bundlers submit a set of `UserOperations` in one transaction, maximizing efficiency.
- **Sequential Execution**: Each operation is processed in order, but failures are isolated — one bad note does not ruin the entire symphony.
- **Atomic Guarantees**: The batch is executed atomically at the protocol level, ensuring deterministic outcomes.

---

## 4.2 Call Execution — The Account’s Voice
Once inside `handleOps`, the account itself speaks:
- The `EntryPoint` invokes the account’s `execute` logic.
- This can be as simple as a token transfer or as complex as a multi‑contract orchestration.
- Execution consumes gas, and responsibility for that gas is already determined during validation.

---

## 4.3 PostOp Logic — Paymaster Accountability
Execution is not the end. If a paymaster sponsored the operation:
- The `EntryPoint` calls `postOp` to finalize sponsorship.
- **Modes of PostOp**:
  - `opSucceeded`: The operation executed successfully.
  - `opReverted`: The operation failed, but the paymaster still pays.
- This ensures that sponsorship is binding — paymasters cannot escape liability by hoping for failure.

---

## 4.4 Gas Settlement — Economic Closure
Gas is the heartbeat of Ethereum, and EIP‑4337 ensures it is accounted for:
- **Self‑Funded Accounts**: Gas is deducted from the account’s deposit in the `EntryPoint`.
- **Sponsored Operations**: Gas is deducted from the paymaster’s deposit.
- **Failure Liability**: Even reverted operations consume gas, and the responsible entity pays.  
This design prevents denial‑of‑service attacks by making spam economically unsustainable.

---

## 4.5 The Bridge to Economics
Execution does not exist in isolation. Its gas settlement feeds directly into the **Gas Accounting & Economic Model** (Section 5), where parameters like `preVerificationGas` and fee caps define the broader sustainability of the system.

---

## 4.6 Summary
The execution flow transforms validated intent into on‑chain action:
- `handleOps` orchestrates batch execution.
- Accounts perform their logic through `execute`.
- Paymasters finalize responsibility via `postOp`.
- Gas settlement enforces accountability.

This stage is where **account abstraction proves its worth**: operations are executed securely, sponsors are held accountable, and the network remains resilient against abuse. Execution is not just a technical step — it is the protocol’s promise that validated intent will be honored.
