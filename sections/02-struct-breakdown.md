# 2. UserOperation Struct Breakdown

## 2.1 Canonical Struct Definition

The `UserOperation` is an ABI-encoded struct processed by the `EntryPoint` contract. It represents a user’s intent and contains all data required for validation, execution, and gas settlement.

```solidity
struct UserOperation {
    address sender;
    uint256 nonce;
    bytes initCode;
    bytes callData;
    uint256 callGasLimit;
    uint256 verificationGasLimit;
    uint256 preVerificationGas;
    uint256 maxFeePerGas;
    uint256 maxPriorityFeePerGas;
    bytes paymasterAndData;
    bytes signature;
} 
```


## 2.2 Field-by-Field Analysis

---

### `sender`

The Smart Account address responsible for validation and execution.

**Responsibilities**
- Must implement `validateUserOp()`
- Defines execution context
- Receives prefund settlement logic

**Failure Conditions**
- Missing interface implementation
- Validation revert inside account logic

---

### `nonce`

Prevents replay attacks.

Smart Accounts may implement:
- Sequential nonces
- Parallel nonce lanes
- Key-scoped nonces

**Failure Conditions**
- Nonce mismatch
- Replay attempt
- Custom nonce rule violation

---

### `initCode`

Optional account deployment bytecode.

**Used When**
- Smart Account is not yet deployed
- First UserOperation performs account creation

**Execution Behavior**
- `EntryPoint` deploys contract before validation
- Deployment address must match `sender`

**Failure Conditions**
- Deployment revert
- Incorrect factory logic
- Address mismatch

---

### `callData`

ABI-encoded function call executed by the Smart Account.

Represents the user’s intent.

**Executed During**
- Execution phase after validation succeeds

**Failure Conditions**
- Malformed calldata
- Internal execution revert
- Out-of-gas during execution

---

### `callGasLimit`

Maximum gas allocated for the execution phase.

**Scope**
- Internal Smart Account call only

**Failure Conditions**
- Underestimation causing execution failure
- Excessive allocation affecting bundler profitability

---

### `verificationGasLimit`

Maximum gas allocated for validation logic.

**Covers**
- `validateUserOp()`
- Paymaster validation (if present)

**Failure Conditions**
- Insufficient gas during signature checks
- Complex validation exceeding limit

---

### `preVerificationGas`

Gas allocated for overhead prior to validation.

**Includes**
- Calldata cost
- Memory expansion
- Hashing operations
- Bundler overhead costs

**Important**
- Estimated off-chain
- Impacts bundler economic decisions

---

### `maxFeePerGas`

Maximum total gas price willing to be paid.

Similar to EIP-1559 `maxFeePerGas`.

Used in final gas settlement calculation.

---

### `maxPriorityFeePerGas`

Priority fee (tip) incentivizing bundler inclusion.

Bundlers evaluate:
- Base fee
- Competing UserOperations
- Profit margins

---

### `paymasterAndData`

Optional gas sponsorship configuration.

**Structure**
- Paymaster address
- Encoded validation data

**Behavior**
- Triggers paymaster validation logic

**Failure Conditions**
- Invalid paymaster signature
- Insufficient paymaster deposit
- Validation revert

---

### `signature`

Authentication payload verified inside `validateUserOp()`.

May represent:
- ECDSA signature
- Multisig approval
- Passkey-based auth
- Custom schemes

**Failure Conditions**
- Invalid signature
- Expired authorization
- Malformed encoding

---

## 2.3 Gas Accounting Model Differences

ERC-4337 introduces a three-layer gas accounting model:

1. **Pre-verification gas** (overhead and calldata cost)
2. **Verification gas** (account + paymaster validation)
3. **Execution gas** (actual user intent execution)

---

### Key Differences from Legacy Transactions

| Legacy Transaction | ERC-4337 UserOperation |
|--------------------|------------------------|
| Single gas pool | Segmented gas model |
| Immediate mempool inclusion | Off-chain simulation first |
| Fixed ECDSA validation | Programmable validation logic |
| User pays gas directly | Optional third-party sponsorship |

---

### Why This Matters

This separation enables:

- Programmable validation logic
- Deterministic simulation
- Bundler profit calculation
- DoS resistance mechanisms

However, it introduces:

- Estimation complexity
- Simulation dependency
- Economic modeling requirements for bundlers


