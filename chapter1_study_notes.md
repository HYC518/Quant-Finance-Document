# Chapter 1: Asset Pricing Basics — Study Notes

**FIN 4370 & 5370 — Advanced Derivative Securities**
**Professor Jian Cai / Spring 2026**

---

## Table of Contents

1. [Fundamental Concepts](#1-fundamental-concepts)
2. [State Prices](#2-state-prices)
3. [Probabilities and Numeraires](#3-probabilities-and-numeraires)
4. [State Price Densities](#4-state-price-densities)
5. [Continuum of States](#5-continuum-of-states)
6. [Numeraires and Probability Measures](#6-numeraires-and-probability-measures)
7. [Fundamental Pricing Formula](#7-fundamental-pricing-formula)
8. [Option Pricing Applications](#8-option-pricing-applications)
9. [Incomplete Markets](#9-incomplete-markets)

---

## 1. Fundamental Concepts

### 1.1 Longs and Shorts

- **Long**: The owner of an asset
- **Short**: A person who owes an asset to someone else

**Examples:**
- Borrow money to buy stocks → short cash + long stocks ("buying on margin" / "levered" investing)
- Borrow stocks and sell them → short stocks + long cash ("short selling" or "shorting")

### 1.2 Margin Requirements

- **Initial Margin**: Minimum amount investor must have at trade initiation
- **Maintenance Margin**: Minimum amount that must always be maintained
- **Margin Call**: Investor must add funds when account drops below maintenance margin

**Example:** Invest \$600, borrow \$400, buy \$1,000 of stock
- Margin = \$600 (investor's own funds)
- If stock drops too far, may trigger a margin call

### 1.3 Options

**Key European Option Elements:**
- Underlying asset
- Exercise/Strike price
- Expiration/Maturity date
- Contract size
- Option premium

**Types:**
- **Call**: Buyer has the right (not obligation) to **buy** at strike price
- **Put**: Buyer has the right (not obligation) to **sell** at strike price
- **European**: Exercise only at maturity
- **American**: Exercise at any time up to maturity

**Important Rules:**
- European options: Exercise at maturity iff in-the-money
- **American calls** on non-dividend-paying assets: **Never** optimal to exercise early
- **American puts**: Early exercise CAN be optimal (regardless of dividends)

### 1.4 Continuous Compounding

If annual rate is $r$, compounding $n$ times per year: \$1 grows to $(1 + r/n)^n$

As $n \to \infty$: $(1 + r/n)^n \to e^r$ (continuous compounding)

**Differential equation:**
$$\frac{dx(t)}{dt} = x(t) r(t)$$

**Solution:**
$$x(t) = x(0) \exp\left(\int_0^t r(s) ds\right)$$

**Special case:** If $r(t) = r$ constant, then $x(t) = x(0) e^{rt}$

---

## 2. State Prices

### 2.1 One-Period Binomial Model

**Setup:**
- Stock price $S$ today, becomes $S_u$ (up) or $S_d$ (down) at time $T$
- Risk-free rate $r$

**No-arbitrage condition:**
$$\frac{S_u}{S} > e^{rT} > \frac{S_d}{S} \quad (1)$$

### 2.2 Arrow Securities and State Prices

**Arrow Security**: Pays \$1 in one specific state, \$0 in all others

By replication (invest $x$ in risk-free, $y$ in stock):

$$\pi_u = \frac{S - S_d e^{-rT}}{S_u - S_d}, \quad \pi_d = \frac{S_u e^{-rT} - S}{S_u - S_d}$$

**$\pi_u$ and $\pi_d$ are called state prices.**

### 2.3 General Pricing Formula

For any security with payoff $(P_u, P_d)$:
$$P = \pi_u P_u + \pi_d P_d$$

**Key identities:**
$$S = \pi_u S_u + \pi_d S_d \quad (2)$$
$$1 = \pi_u e^{rT} + \pi_d e^{rT} \quad (3)$$

### 2.4 Key Conclusion

> **In the absence of arbitrage opportunities, there exist positive state prices such that the price of any security equals the sum across states of its payoff multiplied by the state price.**

---

## 3. Probabilities and Numeraires

### 3.1 Motivation

We want to reinterpret the sums of state prices as **expectations** (probability-weighted averages). This gives us two useful expectations:
1. Corresponding to the risk-free asset
2. Corresponding to the stock

### 3.2 Risk-Neutral Probabilities

**Define:** $p_u = \pi_u e^{rT}$, $p_d = \pi_d e^{rT}$

By equation (3): $p_u + p_d = 1$, and $p_u, p_d > 0$ → these are valid probabilities!

**Risk-neutral pricing formula:**
$$P = e^{-rT}(p_u P_u + p_d P_d) \quad (4)$$

**Why "risk-neutral"?** Because the discount rate is always the risk-free rate, as if investors don't care about risk.

### 3.3 Risk-Free Asset as Numeraire

Consider risk-free security paying $e^{rT}$: $R = 1$, $R_u = R_d = e^{rT}$

Equation (4) can be rewritten:
$$\frac{P}{R} = p_u \frac{P_u}{R_u} + p_d \frac{P_d}{R_d} \quad (5)$$

**Meaning:** Using risk-free asset as numeraire, the ratio of asset prices is a martingale under the risk-neutral measure.

> **A numeraire is a "unit of measurement"** — using one asset's price to measure another asset.

### 3.4 Stock as Numeraire

**Define:** $q_u = \pi_u S_u / S$, $q_d = \pi_d S_d / S$

$$\frac{P}{S} = q_u \frac{P_u}{S_u} + q_d \frac{P_d}{S_d} \quad (6a)$$
$$1 = q_u + q_d \quad (6b)$$

### 3.5 Summary: Numeraires and Probability Measures

> **If there are no arbitrage opportunities, then for each non-dividend-paying asset, there exists a probability measure such that the ratio of any other non-dividend-paying asset's price to the (numeraire) asset's price is a martingale.**

| Numeraire | Probability Measure | Martingales |
|-----------|---------------------|-------------|
| Risk-free $R$ | $p_u, p_d$ (risk-neutral) | $C/R$, $S/R$ |
| Stock $S$ | $q_u, q_d$ | $C/S$, $R/S$ |

### 3.6 Key Terminology

- **Probability Measure**: An assignment of probabilities to events
- **Martingale**: A variable that changes randomly over time, but **the expected future value always equals the current value**

---

## 4. State Price Densities

Let $\text{prob}_u, \text{prob}_d$ be **actual probabilities**. Define:

$$\phi_u = \frac{\pi_u}{\text{prob}_u}, \quad \phi_d = \frac{\pi_d}{\text{prob}_d}$$

**$\phi_u, \phi_d$ are state price densities (state price per unit of probability).**

The pricing formula becomes:
$$P = \text{prob}_u \cdot \phi_u P_u + \text{prob}_d \cdot \phi_d P_d = E[\phi P]$$

> **Under no arbitrage, there exists a state price density function such that the price of any security equals the expectation of (density × payoff) under the actual probability measure.**

---

## 5. Continuum of States

### 5.1 Equation (7): Fundamental Pricing Equation

For a non-dividend-paying security with random price $P(T)$ at time $T$:

$$\boxed{P(0) = E[\phi(T) P(T)] \quad (7)}$$

where $\phi(T)$ is the **state price density** (a positive random variable).

**This is one of the core equations of the chapter!** It tells us:
- Today's price of any asset = expectation of (future price × state price density)
- This formula holds for **any** non-dividend-paying asset

### 5.2 Extension to Time t

Equation (7) naturally extends: for any $t < T$,
$$\phi(t) P(t) = E_t[\phi(T) P(T)]$$

**This means $\phi(t) P(t)$ is a martingale under the original measure!**

---

## 6. Numeraires and Probability Measures

### 6.1 Notation

- $E^{\text{num}}$: Expectation under probability measure with "num" as numeraire
- $\text{prob}^{\text{num}}(A)$: Probability of event A using "num" as numeraire

### 6.2 Deriving Equations (8) and (9)

**Starting point:** From equation (7) applied to the asset num:
$$\text{num}(0) = E[\phi(T) \text{num}(T)]$$

**Key insight: Radon-Nikodym derivative**

Define a new measure $\mathbb{P}^{\text{num}}$ with density relative to $\mathbb{P}$:
$$Z = \frac{d\mathbb{P}^{\text{num}}}{d\mathbb{P}} = \frac{\phi(T) \text{num}(T)}{\text{num}(0)}$$

#### Why is this Z valid?

**Condition (a): $Z \geq 0$** ✓
Because $\phi(T) > 0$ (state price density is positive) and $\text{num}(T) > 0$ (asset prices are positive)

**Condition (b): $E[Z] = 1$** (ensures it defines a valid probability measure)

**Why do we need $E[Z] = 1$?**

Any probability measure $\mathbb{Q}$ must satisfy $\mathbb{Q}(\Omega) = 1$ (probability of full sample space = 1).

If we use $Z$ to define a new measure: $\mathbb{P}^{\text{num}}(A) = E[1_A \cdot Z]$

Setting $A = \Omega$: $\mathbb{P}^{\text{num}}(\Omega) = E[1_\Omega \cdot Z] = E[Z]$

So requiring $\mathbb{P}^{\text{num}}(\Omega) = 1 \Leftrightarrow E[Z] = 1$.

**Verifying $E[Z] = 1$:**
$$E[Z] = \frac{E[\phi(T)\text{num}(T)]}{\text{num}(0)} = \frac{\text{num}(0)}{\text{num}(0)} = 1 \checkmark$$

**This is precisely why equation (7) is crucial!** It guarantees $Z$ is a valid probability density.

### 6.3 Deriving Equation (8)

Apply Radon-Nikodym: $E^{\text{num}}[X] = E[X \cdot Z]$

Plug in indicator $X = 1_A$:

$$\boxed{\text{prob}^{\text{num}}(A) = E^{\text{num}}(1_A) = E\left[1_A \phi(T) \frac{\text{num}(T)}{\text{num}(0)}\right] \quad (8)}$$

### 6.4 Deriving Equation (9)

For a general random variable $X$:

$$\boxed{E^{\text{num}}(X) = E\left[X \phi(T) \frac{\text{num}(T)}{\text{num}(0)}\right] \quad (9)}$$

### 6.5 Intuition

Think of it this way:
- Under the original measure, event A has "weight" $\text{prob}(A)$
- Under the num-measure, event A has "weight" $\text{prob}^{\text{num}}(A)$
- The two are connected via the conversion factor $Z = \phi(T)\text{num}(T)/\text{num}(0)$

If num(T) is large in some state, that state has "more weight" under the num-measure — because we're using num as the unit of measurement.

---

## 7. Fundamental Pricing Formula

### 7.1 Equation (10)

For any $t < T$, for any non-dividend-paying asset $P$:

$$\boxed{\frac{P(t)}{\text{num}(t)} = E_t^{\text{num}}\left[\frac{P(T)}{\text{num}(T)}\right] \quad (10)}$$

Or equivalently:
$$P(t) = \text{num}(t) \cdot E_t^{\text{num}}\left[\frac{P(T)}{\text{num}(T)}\right]$$

**This is the "fundamental pricing formula" — a present value relation!**

### 7.2 Proof: Density-Based Approach

**Goal:** Show that $\frac{P(t)}{\text{num}(t)}$ is a martingale under the num-measure.

#### Step 1: Conditional version of equation (9) (Bayes' formula)

$$E_t^{\text{num}}[X] = \frac{E_t\left[X \cdot \phi(T)\frac{\text{num}(T)}{\text{num}(0)}\right]}{E_t\left[\phi(T)\frac{\text{num}(T)}{\text{num}(0)}\right]}$$

#### Step 2: Simplify the denominator

By equation (7) applied to asset num at time $t$:
$$\phi(t)\text{num}(t) = E_t[\phi(T)\text{num}(T)]$$

So denominator $= \frac{\phi(t)\text{num}(t)}{\text{num}(0)}$

#### Step 3: Conditional expectation formula

$$E_t^{\text{num}}[X] = \frac{E_t[X \cdot \phi(T)\text{num}(T)]}{\phi(t)\text{num}(t)}$$

#### Step 4: Plug in $X = P(T)/\text{num}(T)$

$$E_t^{\text{num}}\left[\frac{P(T)}{\text{num}(T)}\right] = \frac{E_t[\phi(T) P(T)]}{\phi(t)\text{num}(t)}$$

#### Step 5: Apply equation (7) again, this time to asset P

$$\phi(t) P(t) = E_t[\phi(T) P(T)]$$

#### Step 6: Final result

$$E_t^{\text{num}}\left[\frac{P(T)}{\text{num}(T)}\right] = \frac{\phi(t) P(t)}{\phi(t)\text{num}(t)} = \frac{P(t)}{\text{num}(t)} \checkmark$$

### 7.3 Beautiful Symmetry

| Measure | Martingale |
|---------|------------|
| Original measure $\mathbb{P}$ | $\phi(t) P(t)$ |
| num-measure $\mathbb{P}^{\text{num}}$ | $\frac{P(t)}{\text{num}(t)}$ |

**These two martingale properties are essentially the same thing — just viewed from different measures!**

### 7.4 About $\mathcal{F}_t$ (Conditioning Information)

$\mathcal{F}_t$ is the **filtration** — represents **all information known up to time t**:
- E.g., the historical path of the stock price
- $E[X | \mathcal{F}_t]$ means "conditional expectation of X given info up to time t"
- Often abbreviated as $E_t[X]$

---

## 8. Option Pricing Applications

### 8.1 Share Digital Call

**Payoff:** $SD(T) = S(T) \cdot 1_{\{S(T) \geq K\}}$ (pays $S(T)$ if $S(T) \geq K$, else 0)

**Using S as numeraire:**
$$SD(t) = S(t) E_t^S\left[\frac{SD(T)}{S(T)}\right] = S(t) E_t^S[1_{\{S(T)\geq K\}}] = S(t) \text{prob}^S(S(T) \geq K)$$

### 8.2 Digital Call

**Payoff:** $D(T) = 1_{\{S(T) \geq K\}}$ (pays \$1 if $S(T) \geq K$, else 0)

**Using risk-free asset R as numeraire:**
$$D(t) = R(t) E_t^R\left[\frac{D(T)}{R(T)}\right] = e^{-r(T-t)} \text{prob}^R(S(T) \geq K)$$

### 8.3 Standard Call

**Payoff:** $c(T) = (S(T) - K) \cdot 1_{\{S(T) \geq K\}} = SD(T) - K \cdot D(T)$

**Price decomposition:**
$$c(t) = SD(t) - K \cdot D(t)$$
$$= S(t) \text{prob}^S(S(T) \geq K) - K e^{-r(T-t)} \text{prob}^R(S(T) \geq K)$$

**This is the general form of the Black-Scholes formula!**

### 8.4 Standard Put

**Payoff:** $p(T) = (K - S(T)) \cdot 1_{\{S(T) \leq K\}}$

By similar analysis:
$$p(t) = K e^{-r(T-t)} \text{prob}^R(S(T) \leq K) - S(t) \text{prob}^S(S(T) \leq K)$$

---

## 9. Incomplete Markets

### 9.1 Trinomial Model Example

Asset price takes three values: $S_u > S_m > S_d$

State prices $\pi_u, \pi_m, \pi_d$ must satisfy:
$$S = \pi_u S_u + \pi_m S_m + \pi_d S_d \quad (11)$$
$$1 = (\pi_u + \pi_m + \pi_d) e^{rT} \quad (12)$$

### 9.2 The Problem

**Two equations, three unknowns → infinitely many solutions!**

This means:
- Multiple risk-neutral probabilities exist
- Calls and puts have multiple arbitrage-free prices
- These options **cannot be replicated**

### 9.3 Complete vs. Incomplete Markets

- **Complete market**: Every state-contingent claim can be replicated by trading the marketed assets
- **Incomplete market**: Some claims cannot be replicated
  - Only replicable securities can be priced by arbitrage
  - Equilibrium approach is generally needed

---

## Key Formulas Quick Reference

| # | Formula | Meaning |
|---|---------|---------|
| (1) | $\frac{S_u}{S} > e^{rT} > \frac{S_d}{S}$ | Binomial no-arbitrage condition |
| (2) | $S = \pi_u S_u + \pi_d S_d$ | State prices price the stock |
| (3) | $1 = (\pi_u + \pi_d) e^{rT}$ | State prices price the risk-free asset |
| (4) | $P = e^{-rT}(p_u P_u + p_d P_d)$ | Risk-neutral pricing |
| (5) | $\frac{P}{R} = p_u \frac{P_u}{R_u} + p_d \frac{P_d}{R_d}$ | R as numeraire |
| (6) | $\frac{P}{S} = q_u \frac{P_u}{S_u} + q_d \frac{P_d}{S_d}$ | S as numeraire |
| **(7)** | **$P(0) = E[\phi(T) P(T)]$** | **State price density pricing (foundational)** |
| **(8)** | **$\text{prob}^{\text{num}}(A) = E[1_A \phi(T) \frac{\text{num}(T)}{\text{num}(0)}]$** | **Measure change (event probability)** |
| **(9)** | **$E^{\text{num}}(X) = E[X \phi(T) \frac{\text{num}(T)}{\text{num}(0)}]$** | **Measure change (general expectation)** |
| **(10)** | **$\frac{P(t)}{\text{num}(t)} = E_t^{\text{num}}\left[\frac{P(T)}{\text{num}(T)}\right]$** | **Fundamental pricing formula (martingale)** |

---

## Key Concepts Review

### Radon-Nikodym Derivative

**Purpose:** "Exchange rate" between two probability measures

**Form:** $Z = \frac{d\mathbb{Q}}{d\mathbb{P}}$ satisfies $E^{\mathbb{Q}}[X] = E^{\mathbb{P}}[X \cdot Z]$

**Validity conditions:**
1. $Z \geq 0$
2. $E^{\mathbb{P}}[Z] = 1$

**In our context:** $Z = \frac{\phi(T)\text{num}(T)}{\text{num}(0)}$

### Martingale

**Definition:** $X$ is a martingale if $E_t[X(T)] = X(t)$ for all $t < T$.

**Intuition:** A "fair game" — given current information, the expected future value equals the current value.

**Two core martingales:**
1. Under original measure: $\phi(t) P(t)$
2. Under num-measure: $P(t)/\text{num}(t)$

### State Price Density $\phi(T)$

- A **positive random variable**
- The "density" form of state prices
- Acts as a **generalized discount factor** — more general than $e^{-rT}$ because it accounts for risk

---

## FAQ

### Q1: In what sense does equation (7) "hold for any t"?

Equation (7) is the special case at t = 0. In a no-arbitrage market, the same logic holds for any $t < T$:
$$\phi(t) P(t) = E_t[\phi(T) P(T)]$$

Equivalently, $\phi(t) P(t)$ is a martingale.

### Q2: Why do we need the no-arbitrage assumption?

No-arbitrage guarantees that the state price density $\phi$ exists and is positive. This is the **fundamental theorem of asset pricing**:

> No arbitrage ⟺ Existence of equivalent martingale measure ⟺ Existence of positive state price density

### Q3: Is the risk-neutral measure just one of many measures?

**Yes!** The risk-neutral measure is the special case when num = risk-free asset R. Each different numeraire corresponds to a different probability measure, and all these measures are equivalent (same events have positive probability).

### Q4: Why use different numeraires?

Different derivatives are easier to price under different numeraires:
- **Digital options** → use R (risk-free asset)
- **Share digital options** → use S (stock)
- **Interest rate derivatives** → use zero-coupon bonds (forward measure)

Choosing the right numeraire can dramatically simplify the pricing formula!

---

## Chapter Summary

This chapter establishes the **theoretical foundation** of derivative pricing:

1. **Starting from state prices** → derives the essence of no-arbitrage pricing
2. **Introducing probability measures** → reinterprets state prices as expectations
3. **The numeraire concept** → different numeraires correspond to different equivalent martingale measures
4. **State price density** $\phi(T)$ → the core tool in continuous state space
5. **Fundamental pricing formula** → any asset price / numeraire price = expectation of future ratio under num-measure

**These tools will be used in later chapters to derive the Black-Scholes model and price more complex derivatives.**
