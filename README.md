# Proposal-Optimize-QVAULT-Smart-Contract

# Proposal: Optimize Qvault Smart Contract

Optimize the existing Qvault Smart Contract on Qubic.

## Available Options

- **Option 0:** No, do not approve the Qvault optimize.
- **Option 1:** Yes, approve the Qvault optimize.

## Context

Qvault already exists on-chain. This proposal is an optimize proposal, not an initial deployment proposal.

## Summary of Changes

### What we optimized?

- To prevent state bloat, reduce spamming of procedures, and improve the economic model of the contract, we introduced fees and stricter thresholds for staking, voting, and interacting with the smart contract.

### Staking and Unstaking Adjustments

- **Limits per Epoch:** A wallet address is now strictly allowed to perform exactly one `stake` and one `unStake` per epoch.
- **Minimum Stake Amount:** `constexpr uint32 QVAULT_MIN_STAKE_AMOUNT = 100;`. Users must stake a minimum of 100 QCAP per transaction.
- **Unstaking Fee:** `constexpr uint64 QVAULT_UNSTAKE_FEE = 100000;`. The invocator needs to pay the QVAULT_UNSTAKE_FEE when they invoke the `unStake` procedure. The Qvault SC will collect this fee from the `invocationReward` and automatically burn it.

### Voting and Purchasing Limitations

- **Voting Limit:** A wallet address is now limited to a maximum of exactly 2 votes per specific proposal ID. 
- **Minimum QCAP Purchase:** `constexpr uint32 QVAULT_MIN_BUY_QCAP_AMOUNT = 50;`. To prevent micro-transactions, users invoking `buyQcap` must purchase at least 50 QCAPs.

### External Transfer Threshold

- **Minimum Deposit:** `constexpr uint64 QVAULT_MIN_INCOMING_TRANSFER = 5000000;`. Any external standard transaction transferring QUBICs to the Qvault smart contract must send a minimum of 5,000,000 QUBICs. Transfers below this threshold will be ignored during `POST_INCOMING_TRANSFER`.

## Optimize the performance

- By limiting `stake`, `unStake`, and `voteInProposal` frequencies per epoch and per proposal, we significantly reduce the amount of state rewrites for `HashMap` and `Array` updates. Adding fees and minimum QCAP thresholds introduces a clear economic protection against spamming. Rejecting dust transfers and micro-interactions right at the beginning of the procedures drops the execution time to O(1) for invalid calls, preventing the contract's execution fee reserve from being drained by malicious spam transactions.

## Technical Implementation

**Developer:** [baoLuck](https://github.com/baoLuck)

Core implementation PR1: [https://github.com/qubic/core/pull/222/changes](https://github.com/qubic/core/pull/222/changes)

Core implementation PR2: [https://github.com/qubic/core/pull/365/changes](https://github.com/qubic/core/pull/365/changes)
