# Stacks-ArmorFi -  DeFi Insurance Pool Smart Contract

A decentralized, DAO-governed insurance pool implemented in Clarity for the [Stacks blockchain](https://www.stacks.co/). This contract allows users to create insurance pools, stake funds, submit claims, and vote on claims via decentralized governance.

---

## 📜 Overview

This smart contract provides the building blocks for a decentralized insurance protocol, where:

* Users can **create and join insurance pools** by staking STX.
* **Premiums and coverage limits** are set during pool creation.
* **Claims** can be submitted by any participant and must pass a **staking-weighted voting process**.
* A **DAO governance model** ensures fair voting on claim approvals.
* Claims that meet the **70% approval threshold** are automatically paid out.
* Stakeholders are protected against malicious claims via quorum-based voting.

---

## 🏗 Architecture

### 💾 Data Maps

| Name               | Description                                           |
| ------------------ | ----------------------------------------------------- |
| `InsurancePools`   | Stores metadata and configuration per insurance pool. |
| `PoolStakes`       | Tracks individual stake per user per pool.            |
| `InsuranceClaims`  | Stores submitted claims and their statuses.           |
| `StakerTotalStake` | Tracks total stake per user across all pools.         |

---

## 🔧 Core Concepts

### Insurance Pools

* Created only by the **contract owner**.
* Must define:

  * `coverage-limit`: Max payout per claim.
  * `premium-rate`: Fee rate for joining or coverage.
* Pools can be in 3 states:

  * `ACTIVE`
  * `PAUSED`
  * `LIQUIDATED`

### Staking

* Users stake STX into a pool to provide coverage.
* Stake weight determines voting power in claims.

### Claims

* Must be submitted with:

  * `pool-id`
  * `amount`
  * `evidence` (string describing or pointing to off-chain proof)
* Must pass:

  * Voting quorum of `10` or more total weighted votes.
  * Approval rate ≥ `70%`.

### Governance

* DAO-style staking-based voting on claims.
* Voting period: \~24 hours (144 blocks).
* Weighted by amount staked in that pool.

---

## 🚀 Functions

### Public Functions

| Function                | Description                                       |
| ----------------------- | ------------------------------------------------- |
| `create-insurance-pool` | Deploy a new insurance pool. Only contract owner. |
| `stake-in-pool`         | Stake STX into a pool.                            |
| `unstake-from-pool`     | Withdraw part or all stake from a pool.           |
| `submit-claim`          | Submit a claim request.                           |
| `vote-on-claim`         | Vote for or against a claim using stake weight.   |
| `process-claim`         | Resolve a claim post voting deadline.             |

### Read-only Functions

| Function           | Returns                      |
| ------------------ | ---------------------------- |
| `get-pool-info`    | Pool details                 |
| `get-stake-info`   | Staker's info for a pool     |
| `get-claim-info`   | Claim details                |
| `get-staker-total` | Total stake across all pools |

---

## ✅ Example Flow

1. **Create Pool (Admin):**

```clojure
(create-insurance-pool "HealthCover" u1000000 u5)
```

2. **Stake in Pool:**

```clojure
(stake-in-pool u0 u50000)
```

3. **Submit Claim:**

```clojure
(submit-claim u0 u20000 "Hospital visit, receipt #123456")
```

4. **Vote on Claim:**

```clojure
(vote-on-claim u0 true) ;; true to approve, false to reject
```

5. **Process Claim After Voting Period:**

```clojure
(process-claim u0)
```

---

## 🔐 Security Notes

* Only contract owner can create pools.
* Claim approval requires minimum votes to mitigate sybil or low-activity attacks.
* Voters cannot vote twice.
* Vote weight is directly tied to stake size.

---

## 📊 Parameters

| Parameter            | Value                |
| -------------------- | -------------------- |
| `MIN-VOTES-REQUIRED` | `10`                 |
| `APPROVAL-THRESHOLD` | `70%`                |
| `VOTING-PERIOD`      | `144` blocks (\~24h) |

---

## 🔍 Audit Checklist

* [ ] Proper bounds on voting durations.
* [ ] Overflow/underflow checks for uint math.
* [ ] Prevent claims during `PAUSED` or `LIQUIDATED` state.
* [ ] Ensure uniqueness in voter list (already done).
* [ ] STX transfers are `try!`-wrapped.
* [ ] Pool coverage limits respected.

---

## 📘 License

MIT License – open source for public use and further development.

---
