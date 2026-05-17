<div align="center">

# Nulloss Markets

### *Predict everything. Lose nothing.*

Nulloss Markets is a lossless prediction market protocol built on Arc. Users deposit stablecoins to predict asset price movements. Their principal is never at risk. The yield generated on deposits during each prediction round becomes the prize pool. Winners earn it, losers reclaim their full deposit.

**Loss is null. Hence the name.**

<br>

[![Status](https://img.shields.io/badge/Status-Active%20Development-e8d44d?style=flat-square&labelColor=0a0a0a)](https://github.com/nulloss)
[![Chain](https://img.shields.io/badge/Chain-Arc-e8d44d?style=flat-square&labelColor=0a0a0a)](https://testnet.arcscan.app)
[![Language](https://img.shields.io/badge/Solidity-0.8.28-e8d44d?style=flat-square&labelColor=0a0a0a)](https://github.com/nulloss)
[![Builder](https://img.shields.io/badge/Builder-@jadeofwallstreet-e8d44d?style=flat-square&labelColor=0a0a0a)](https://twitter.com/jadeofwallstreet)

</div>

---

<div align="center">

## The Problem With Prediction Markets Today

</div>

Prediction markets are one of the most intellectually honest financial instruments ever designed. They aggregate dispersed information, surface real-time consensus on uncertain outcomes, and have repeatedly outperformed expert forecasts on everything from election results to macroeconomic indicators.

Yet they have consistently failed to scale.

The reason is structural: in a traditional prediction market, losers forfeit capital. This creates a hard psychological and financial ceiling on participation. Users who lose money stop playing. Casual participants — the people whose aggregated views make prediction markets accurate — exit after their first bad round. What remains is a thin market of committed speculators, not the broad participation that makes price discovery meaningful.

Polymarket proved the category is real. It did not solve the loss problem.

**Nulloss does.**

---

<div align="center">

## The Lossless Mechanic

</div>

Every deposit in a Nulloss prediction pool is supplied to **Morpho Blue** on Arc, where it earns yield during the prediction round. At settlement, the oracle reads the final price. Winners take the yield. Losers take their principal back — every dollar of it.

```
User deposits 1,000 USDC → Predicts Long or Short
             ↓
     Deposited to Morpho Blue (earns yield)
             ↓
     Oracle settles round at expiry
             ↓
  If correct:  1,000 USDC principal + share of yield pool
  If wrong:    1,000 USDC principal (full return, no penalty)
```

The only thing at stake is the yield. Principal is structurally protected — not by a guarantee, not by insurance, but by the architecture of the protocol itself.

This mechanic removes the primary barrier to prediction market participation. Users who lose are not penalized. They simply did not win this round. They stay. They participate again. The market deepens.

---

<div align="center">

## Why Arc

</div>

Nulloss is built natively on Arc, Circle's stablecoin-native Layer 1, and the infrastructure fit is precise.

**USDC as native gas.** Every interaction on Nulloss is denominated in USDC. Deposits, withdrawals, yield claims, and gas fees are all paid in the same asset. Users never need to acquire or manage a volatile gas token. This is a meaningful UX improvement over every prediction market built on general-purpose chains.

**Sub-second deterministic finality.** Arc's Malachite BFT consensus finalizes transactions in under one second with certainty. For a prediction market, this means settlement is instant and unambiguous. There is no probabilistic waiting period, no reorg risk, no settlement ambiguity.

**Multi-currency native support.** Arc's StableFX infrastructure enables USDC and EURC pools on the same settlement layer. Nulloss can serve US and European participants in their native currency without bridges or wrapped assets.

**Compliance-ready architecture.** Arc's opt-in compliance tools enable Nulloss to offer institutional pool tiers with appropriate access controls — a product class that has been impossible to build on permissionless general-purpose chains.

**Morpho Blue on Arc.** Morpho is a confirmed Arc ecosystem partner. Nulloss uses Morpho Blue's isolated market design, with one Morpho market per prediction pool, meaning yield positions are completely isolated across pools. One pool's risk profile cannot contaminate another.

---

<div align="center">

## Protocol Architecture

</div>

### Pool Lifecycle

Each Nulloss prediction pool has three phases:

**Open phase:** Users deposit USDC and select their prediction (Long or Short). All deposits are immediately supplied to the pool's dedicated Morpho Blue market and begin earning yield. Users can swap sides or withdraw during this phase.

**Locked phase:** The deposit window closes. Capital remains in Morpho Blue earning yield. Users can still exit but cannot change their prediction.

**Settlement:** The oracle reads the asset price. Winners are determined. Yield is distributed proportionally among winners. All users — winners and losers alike — withdraw their full principal plus any yield they earned.

---

### Pool Tokens

Each pool issues three ERC-20 tokens representing positions:

| Token | Prefix | Minted When |
|-------|--------|-------------|
| Long Token | `lg-` | User predicts price closes **above** target |
| Short Token | `st-` | User predicts price closes **below** target |
| Sponsor Token | `sp-` | Sponsor deposits liquidity to boost pool yield |

Tokens are valued at 1:1 with the underlying collateral at deposit. After settlement, winning token NAV increases proportionally to yield earned. Tokens can be redeemed at any time for their current underlying value.

This token design enables auto-compounding: a winner's tokens automatically carry their increased NAV into the next round without any transaction.

---

### Yield Layer: Morpho Blue

Nulloss uses **Morpho Blue** as its primary yield source — a minimal, immutable lending protocol (650 lines of audited code) that creates isolated markets per asset pair.

Each Nulloss prediction pool corresponds to one Morpho Blue market:

- **Loan token:** USDC
- **Collateral:** USDC (pure lending, no borrow risk)
- **Oracle:** Chainlink price feed for the market's asset pair
- **Supply cap:** Governance-controlled per pool

Yield accrues continuously as Morpho's share price appreciates. At settlement, the protocol withdraws the full position, calculates yield as the difference between withdrawn amount and principal deposited, and distributes accordingly.

> Aave serves as a fallback yield source if Morpho supply caps are reached for a given pool.

---

### Oracle Strategy

Settlement prices are sourced from multiple independent oracle providers, all confirmed on Arc:

| Role | Provider | Description |
|------|----------|-------------|
| Primary | **Chainlink Data Streams** | Pull model, aggregated from multiple sources |
| Secondary | **Pyth Network** | Sub-second first-party price feeds |
| Fallback | **Stork / RedStone** | Emergency fallback layer |

Settlement uses the median of two independent readings and rejects if divergence exceeds a governance-set threshold. This dual-oracle approach significantly reduces the manipulation surface compared to single-oracle settlement.

---

### Protocol Fees and Sustainability

Nulloss is designed for long-term sustainability with multiple revenue streams:

**Yield fee** — The protocol takes a percentage (governance-controlled, initially 8%) of yield distributed to winners. This fee is invisible to losers and represents a small reduction in winners' returns.

**Market creation fee** — A flat USDC fee is charged to create a new prediction pool. This prevents spam and generates revenue from protocols creating partnership markets.

**MetaMorpho Vault curator fee** — Nulloss operates as a MetaMorpho Vault curator, earning a percentage of all yield across vault deposits.

**Treasury yield** — Protocol-owned USDC is held in Morpho Vaults, earning yield continuously on idle capital.

---

<div align="center">

## Institutional Tier: USYC Pools

</div>

Arc supports USYC, Circle's yield-bearing stablecoin backed by short-duration US Treasury securities.

For institutional participants, Nulloss offers USYC-denominated prediction pools:

- Deposits earn US Treasury yield (~4–5% APY) passively during the prediction round
- Winners receive Treasury yield + their share of Morpho yield
- Losers receive their full USYC back, with Treasury yield intact

This means even losing predictions in a USYC pool are financially positive relative to holding cash. The lossless mechanic becomes genuinely **loss-plus**: the worst outcome is earning Treasury yield. The best outcome is amplified returns.

> USYC pools require institutional KYC via Arc's compliance infrastructure and are subject to Circle's USYC eligibility requirements.

---

<div align="center">

## Multi-Currency Support

</div>

Arc's StableFX infrastructure enables native onchain FX between stablecoins. Nulloss launches with:

| Pool | Currency | Audience |
|------|----------|----------|
| USDC pools | USD | Primary retail and institutional markets |
| EURC pools | EUR | European participants |
| USYC pools | USD (yield-bearing) | Institutional |

Users on other chains can bridge USDC to Arc via Circle's CCTP (Cross-Chain Transfer Protocol, Domain 26) in a single transaction, enabling participation from Ethereum, Base, Polygon, and other CCTP-supported networks.

---

<div align="center">

## Governance Token: $NULL

</div>

`$NULL` is the Nulloss governance token.

**Utility:**
- Vote on protocol fee percentage (range: 3–15% of yield)
- Vote on approved Morpho vault curators and risk parameters
- Vote on supported collateral assets and oracle configurations
- Revenue sharing: `$NULL` stakers receive a portion of protocol fee revenue paid in USDC

**Design principles:**
- Token holders receive real yield in USDC — not inflationary token emissions
- Emission schedule is milestone-based, not time-based mining
- No continuous farming inflation — a deliberate departure from 2021-era token design

---

<div align="center">

## Current Status

</div>

Nulloss Markets is in active development, targeting deployment on Arc testnet.

**✅ Completed**
- Protocol architecture designed
- Yield layer selected (Morpho Blue on Arc)
- Oracle strategy defined (Chainlink + Pyth dual-oracle)
- Smart contract interface specifications drafted
- Arc testnet environment configured

**🔄 In Progress**
- Smart contract implementation in Solidity 0.8.28
- Morpho Blue yield adapter
- Chainlink + Pyth oracle adapter
- Pool factory and pool template contracts
- Governance token contract

**📋 Planned**
- Arc testnet deployment
- Full round lifecycle testing
- External security audit
- Arc mainnet launch *(targeting summer 2026)*
- Frontend application

---

<div align="center">

## Technical Stack

</div>

| Component | Choice | Rationale |
|-----------|--------|-----------|
| Chain | Arc (Chain ID: 5042002) | Stablecoin-native, sub-second finality, USDC gas |
| Yield | Morpho Blue | Isolated markets per pool, 650-line immutable core |
| Yield fallback | Aave | Secondary source if Morpho supply caps hit |
| Oracle primary | Chainlink Data Streams | Aggregated, decentralized, Arc-confirmed |
| Oracle secondary | Pyth Network | Sub-second updates, matches Arc finality |
| Stablecoin | USDC + EURC + USYC | Circle-native, no wrapping required |
| Smart contracts | Solidity 0.8.28 | Modern, built-in overflow protection |
| Framework | Foundry | Industry standard for 2026 Solidity development |
| Cross-chain | CCTP (Domain 26) | Native USDC bridging across supported chains |

---

<div align="center">

## Arc Testnet Configuration

</div>

```
Network Name:  Arc Testnet
Chain ID:      5042002
RPC URL:       https://rpc.testnet.arc.network
Explorer:      https://testnet.arcscan.app
Faucet:        https://faucet.circle.com
Gas Token:     USDC
```

**Native Contract Addresses (Arc Testnet)**

```
USDC:     0x3600000000000000000000000000000000000000
EURC:     0x89B50855Aa3bE2F677cD6303Cec089B5F319D72a
USYC:     0xe9185F0c5F296Ed1797AaE4238D26CCaBEadb86C
Permit2:  0x000000000022D473030F116dDEE9F6B43aC78BA3
```

---

<div align="center">

## Research Foundation

</div>

The lossless prediction market mechanic is an original design by the Nulloss team, built from first principles around a single thesis: **the loss barrier is why prediction markets don't scale, and yield-backed principal protection is the structural fix.**

The core insight is straightforward. Prediction markets fail at the participation layer, not the mechanism layer. Their information aggregation properties are well-documented and proven. What kills them is churn — users who lose capital exit, the market thins, price discovery degrades. Fix the loss problem and the participation problem solves itself.

Nulloss is the protocol that executes on this thesis natively — built on infrastructure purpose-designed for stablecoin-native finance, with a sustainable revenue model baked into the architecture from the start, launching in 2026 when prediction markets are a proven category with demonstrated institutional interest.

The prediction market that solves the loss problem, deployed on the chain built for stablecoins, with a business model that sustains it. **That is Nulloss.**

---

<div align="center">

## Links

[![GitHub](https://img.shields.io/badge/GitHub-nulloss-e8d44d?style=flat-square&logo=github&logoColor=white&labelColor=0a0a0a)](https://github.com/nulloss)
[![Twitter](https://img.shields.io/badge/Builder-@jadeofwallstreet-e8d44d?style=flat-square&logo=x&logoColor=white&labelColor=0a0a0a)](https://twitter.com/jadeofwallstreet)
[![Explorer](https://img.shields.io/badge/Arc%20Explorer-testnet.arcscan.app-e8d44d?style=flat-square&labelColor=0a0a0a)](https://testnet.arcscan.app)

<br>

*Nulloss Markets is under active development.*

</div>
