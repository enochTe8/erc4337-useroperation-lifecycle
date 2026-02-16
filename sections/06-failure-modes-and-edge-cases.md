# 06 - Failure Modes and Edge Cases

Even with rigorous validation and execution rules, **EIP‑4337** must anticipate failure modes and edge cases. This section documents how the protocol handles unexpected conditions, ensuring resilience against abuse and predictability for developers.

---

## 6.1 Validation Failures
- **Signature Invalid**: The account’s signature or aggregator proof fails verification.
- **Nonce Mismatch**: Nonce already used or incorrect, preventing replay attacks.
- **Paymaster Rejection**: Paymaster fails `validatePaymasterUserOp` due to insufficient deposit, stake, or policy violation.
- **Aggregator Failure**: Batched signature verification fails, invalidating the entire group.

**Outcome**:  
Gas consumed during validation is still charged to the responsible entity (account or paymaster), preventing denial‑of‑service attacks.

---

## 6.2 Execution Failures
- **Contract Revert**: The account’s `execute` logic reverts due to insufficient balance, invalid calldata, or failed external calls.
- **Out‑of‑Gas**: Operation exceeds allocated gas limit.
- **Forbidden Opcodes**: Attempted use of restricted opcodes during validation or execution (e.g., `CREATE`, `SELFDESTRUCT`).

**Outcome**:  
Execution failure does not revert the entire batch. The failing operation is isolated, and gas costs are still settled.

---

## 6.3 PostOp Edge Cases
- **Paymaster Liability**: Even if execution reverts, `postOp` is called with `PostOpMode.opReverted`, ensuring paymasters remain accountable.
- **Reentrancy Boundaries**: `postOp` cannot reenter `EntryPoint` or manipulate unrelated state, preserving atomicity.
- **State Consistency**: Paymasters must handle both success and failure paths without breaking deposit accounting.

---

## 6.4 Bundler Edge Cases
- **simulateValidation Divergence**: If off‑chain simulation passes but on‑chain validation fails, bundlers still pay for inclusion gas.  
- **Mempool Filtering**: Bundlers must reject operations that fail simulation to avoid wasted gas and DoS vectors.
- **Inclusion Strategy**: Bundlers prioritize operations with sufficient fee coverage and valid deposits.

---

## 6.5 Gas Liability in Failures
- **Validation Failures**: Gas consumed is deducted from deposits of the sender or paymaster.
- **Execution Failures**: Gas consumed is still charged, even if the operation reverts.
- **DoS Mitigation**: This ensures malicious actors cannot flood the network with invalid operations without incurring real costs.

---

## 6.6 Edge Case Examples
- **Expired Operation**: `validationData.validUntil` has passed → operation rejected.
- **Future Operation**: `validationData.validAfter` not yet reached → operation deferred.
- **Empty Batch**: Bundler submits no operations → `handleOps` executes trivially with no effect.
- **Aggregator Misbehavior**: Aggregator fails to provide valid proof → entire batch rejected.

---

## 6.7 Summary
Failure modes and edge cases are not exceptions but **integral parts of the EIP‑4337 design**:
- Validation failures isolate invalid operations without halting the batch.
- Execution failures charge gas but preserve batch integrity.
- Paymasters remain accountable through `postOp`.
- Bundlers mitigate risks via simulation and mempool rules.

By explicitly defining these behaviors, EIP‑4337 ensures predictability, fairness, and resilience against adversarial conditions.
