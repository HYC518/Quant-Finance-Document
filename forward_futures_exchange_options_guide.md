# Forward, Futures, and Exchange Options — Complete Guide

Extensive explanation of FIN4370/5370 Chapter 7.
Washington University in St. Louis — Prof. Hong Liu

---

## Table of Contents

1. [Overview and Big Picture](#1-overview-and-big-picture)
2. [The General Pattern Behind All Formulas](#2-the-general-pattern-behind-all-formulas)
3. [Black-Scholes Formula Review](#3-black-scholes-formula-review)
4. [Margrabe's Formula — Exchange Options](#4-margrabes-formula--exchange-options)
5. [Black's Formula — Options on Forwards](#5-blacks-formula--options-on-forwards)
6. [Merton's Formula — Stochastic Interest Rates](#6-mertons-formula--stochastic-interest-rates)
7. [Deferred Exchange Options](#7-deferred-exchange-options)
8. [Greeks and Hedging](#8-greeks-and-hedging)
9. [Futures vs Forward Prices](#9-futures-vs-forward-prices)
10. [Futures Options Formula](#10-futures-options-formula)
11. [Time-Varying Volatility](#11-time-varying-volatility)
12. [Market Completeness](#12-market-completeness)
13. [VBA Implementation](#13-vba-implementation)
14. [Formula Cheat Sheet](#14-formula-cheat-sheet)

---

## 1. Overview and Big Picture

### What This Chapter Does

Standard Black-Scholes requires a **constant risk-free rate**. This chapter relaxes that assumption and derives three generalizations:

1. **Margrabe's Formula** — option to exchange one risky asset for another (no risk-free asset needed)
2. **Black's Formula** — options on forward/futures contracts (no constant risk-free rate needed)
3. **Merton's Formula** — standard calls/puts when the risk-free rate is random

### The Master Trick: Change of Numeraire

The key insight running through everything is **using one asset as numeraire** instead of cash. When you measure everything in units of a chosen asset, you can apply familiar Black-Scholes machinery even when there is no risk-free cash account.

---

## 2. The General Pattern Behind All Formulas

Every single formula in this chapter has the SAME structure:

```
Option Value = PV(asset received) × N(x) - PV(asset delivered) × N(y)
```

Where:
```
x = [log(PV_received / PV_delivered) + ½σ²T] / (σ√T)
y = x - σ√T
```

**What each term means:**
- PV(asset received): present value today of what you get if you exercise
- PV(asset delivered): present value today of what you pay if you exercise
- N(x): risk-adjusted probability of exercising (from received side)
- N(y): risk-adjusted probability of exercising (from delivered side)

**Why this structure?**

It directly reflects the option payoff structure: you receive something and you deliver something. The option only has value when what you receive > what you deliver.

### Mapping to Black-Scholes

| Formula | PV received | PV delivered |
|---------|-------------|--------------|
| Black-Scholes Call | e^(-qT) S(0) | e^(-rT) K |
| Black-Scholes Put | e^(-rT) K | e^(-qT) S(0) |
| Margrabe | e^(-q₁T) S₁(0) | e^(-q₂T) S₂(0) |
| Black Call | P(0,T') F(0) | P(0,T') K |
| Merton Call | e^(-qT) S(0) | P(0,T) K |

---

## 3. Black-Scholes Formula Review

**Call:**
```
C = e^(-qT) S(0) N(d₁) - e^(-rT) K N(d₂)
```

**Put:**
```
P = e^(-rT) K N(-d₂) - e^(-qT) S(0) N(-d₁)
```

Where:
```
d₁ = [log(S(0)/K) + (r - q + ½σ²)T] / (σ√T)
d₂ = d₁ - σ√T
```

**Interpretation:**
- e^(-qT) S(0): PV of stock received (dividend reduces effective value)
- e^(-rT) K: PV of strike paid
- N(d₁), N(d₂): risk-adjusted probabilities

**Note on currency independence:** The Black-Scholes formula doesn't depend on which currency you use. This is the key insight that enables Margrabe's derivation.

---

## 4. Margrabe's Formula — Exchange Options

### Setup

A European exchange option gives the right to:
- **Receive:** Asset 1 worth S₁(T)
- **Give up:** Asset 2 worth S₂(T)
- **At time T**

Payoff: max(0, S₁(T) - S₂(T))

Both assets follow GBM with dividend yields q₁, q₂ and volatilities σ₁, σ₂ with correlation ρ.

### The Formula

```
Value = e^(-q₁T) S₁(0) N(d₁) - e^(-q₂T) S₂(0) N(d₂)
```

Where:
```
d₁ = [log(S₁(0)/S₂(0)) + (q₂ - q₁ + ½σ²)T] / (σ√T)
d₂ = d₁ - σ√T
σ = √(σ₁² + σ₂² - 2ρσ₁σ₂)
```

### Derivation

**Step 1: Change numeraire to Asset 2**

The Black-Scholes formula works in any currency. Use Asset 2 as the "currency":

Divide payoff by S₂(T):
```
max(0, S₁(T)/S₂(T) - 1)
```

This is now a standard call option! The underlying is S₁/S₂, the strike is 1.

**Step 2: Identify the inputs**

Under Asset 2 as numeraire:
- Risk-free rate = dividend yield on Asset 2 = q₂ (Asset 2 pays dividends, which is like earning q₂)
- Underlying = S₁/S₂, with dividend yield q₁
- Volatility of S₁/S₂ = σ = √(σ₁² + σ₂² - 2ρσ₁σ₂)

**Step 3: Apply Black-Scholes and convert back**

Apply B-S in units of Asset 2, then multiply by S₂(0) to get back to dollars.

### Why σ = √(σ₁² + σ₂² - 2ρσ₁σ₂)?

From Itô's Lemma:
```
d(log S₁/S₂) = d(log S₁) - d(log S₂)
              = [(μ₁ - ½σ₁²) - (μ₂ - ½σ₂²)] dt + σ₁ dB₁ - σ₂ dB₂
```

The diffusion term is σ₁ dB₁ - σ₂ dB₂, and its variance is:
```
Var = σ₁² + σ₂² - 2ρσ₁σ₂
```

**Important:** σ₁, σ₂, ρ can individually be random. Only the combined σ must be constant!

### When is an Exchange Option Exercised?

Exercise when S₁(T) > S₂(T) — you receive something worth more than what you give up. No risk-free rate enters because no cash changes hands at exercise!

---

## 5. Black's Formula — Options on Forwards

### Setup and Key Distinction

**Two dates:**
- T: option maturity (when you decide)
- T': forward maturity (when payment occurs, T ≤ T')

**Critical:** When you exercise a call on a forward, you receive a **forward contract** — you do NOT pay cash immediately! Payment K happens at T', not T.

### Why Exercise When F(T) > K?

When you exercise:
1. You receive a long forward locked at strike K
2. You immediately enter a short forward at market price F(T) (costs nothing — new forward)
3. At T': buy at K, sell at F(T), net profit = F(T) - K

So F(T) > K → exercise → riskless profit at T' → exercise!

If F(T) < K → you'd be paying more than market → don't exercise.

### Deriving the Payoff

Exercise of call at T gives payoff at T of:
```
max(0, P(T,T')[F(T) - K])
```

The P(T,T') appears because the gain F(T)-K is received at T', so discount back to T.

### Casting as Exchange Option

Define:
```
S₁(t) = P(t,T') F(t)   ← "Asset 1"
S₂(t) = P(t,T') K      ← "Asset 2"
```

Then payoff = max(0, S₁(T) - S₂(T)) — exactly Margrabe's setup!

**What are these assets physically?**

Asset 1 = S₁(t) = P(t,T')F(t):
- Go long one forward contract (value = P(t,T')(F(t) - F(0)))
- Buy F(0) units of discount bond (value = F(0)·P(t,T'))
- Total = P(t,T')F(t) ✓

Asset 2 = S₂(t) = P(t,T')K:
- Buy K units of discount bond maturing at T'
- Total = K·P(t,T') ✓

Both assets are **non-dividend paying** (q₁ = q₂ = 0).

### Applying Margrabe

Substitute into Margrabe with q₁ = q₂ = 0:
```
S₁(0) = P(0,T') F(0),  S₂(0) = P(0,T') K
```

**Call Price:**
```
= P(0,T') F(0) N(d₁) - P(0,T') K N(d₂)
```

**Put Price:**
```
= P(0,T') K N(-d₂) - P(0,T') F(0) N(-d₁)
```

Where:
```
d₁ = [log(F(0)/K) + ½σ²T] / (σ√T)
d₂ = d₁ - σ√T
```

### T vs T' — Which Goes Where?

This is a common source of confusion:

| Term | Uses | Reason |
|------|------|--------|
| σ√T | T (option maturity) | Uncertainty accumulates until you DECIDE at T |
| ½σ²T | T (option maturity) | Same — variance over option life |
| P(0,T') | T' (forward maturity) | Cash flow happens at T', discount from T' |

**Key insight:** After T you've already decided to exercise or not — no more uncertainty. But the payment is still T' away, so that discount uses T'.

### Put-Call Parity for Forward Options

```
Call + P(0,T')K = Put + P(0,T')F(0)
```

Both sides worth max(F(T), K)·P(T,T') at time T.

### Why No Constant Risk-Free Rate Needed?

Because P(0,T') replaces e^(-rT'). Whatever shape interest rates take, P(0,T') is observable from the bond market today. No need to assume r is constant!

---

## 6. Merton's Formula — Stochastic Interest Rates

### The Problem

Black-Scholes requires constant r. What if r is random?

### The Key Insight

Recall the forward price formula (no-arbitrage):
```
F(t) = e^(-q(T-t)) S(t) / P(t,T)
```

At maturity T:
```
F(T) = S(T)    (since P(T,T) = 1 and e^0 = 1)
```

So a standard option on S(T) has the same payoff as a forward option on F(T)! We can use Black's formula, setting T' = T and:
```
F(0) = e^(-qT) S(0) / P(0,T)
```

### Merton's Formula

```
Call = e^(-qT) S(0) N(d₁) - P(0,T) K N(d₂)
Put  = P(0,T) K N(-d₂) - e^(-qT) S(0) N(-d₁)
```

Where:
```
d₁ = [log(S(0)/(K·P(0,T))) - qT + ½σ²T] / (σ√T)
d₂ = d₁ - σ√T
```

### Alternative Form Using Bond Yield

Let y = -log(P(0,T))/T, so P(0,T) = e^(-yT). Then:
```
d₁ = [log(S(0)/K) + (y - q + ½σ²)T] / (σ√T)
```

This looks exactly like Black-Scholes with r replaced by y (the bond yield)!

### What Volatility σ to Use?

The forward price F(t) = e^(-q(T-t))S(t)/P(t,T) has volatility:
```
σ = √(σₛ² + σₚ² - 2ρσₛσₚ)
```

Where σₛ = stock volatility, σₚ = bond price volatility, ρ = their correlation.

**For short-maturity options:** σₚ ≈ 0, so σ ≈ σₛ. Merton ≈ Black-Scholes for short maturities!

### Why P(0,T) Instead of e^(-rT)?

P(0,T) is observed directly from the bond market — no need to assume what r is. It already incorporates the market's expectation of all future interest rates!

---

## 7. Deferred Exchange Options

### Setup

An option that:
- **Matures at T** (when you decide)
- **Exchanges assets at T' ≥ T** (when settlement occurs)

Present value of one share of Asset i today = e^(-qᵢT') Sᵢ(0) (dividend adjusted to T')

### Formula

```
Value = e^(-q₁T') S₁(0) N(d₁) - e^(-q₂T') S₂(0) N(d₂)
```

Where:
```
d₁ = [log(S₁(0)/S₂(0)) + (q₂-q₁)T' + ½σ²T] / (σ√T)
d₂ = d₁ - σ√T
```

### T vs T' Again

Notice:
- σ²T uses T → uncertainty over option life
- (q₂-q₁)T' uses T' → dividend adjustment to settlement date
- e^(-qᵢT') uses T' → discounting to settlement date

The decision (T) and the settlement (T') are doing different jobs!

---

## 8. Greeks and Hedging

### Greeks for Margrabe

| Greek | Formula | Meaning |
|-------|---------|---------|
| ∂/∂S₁ | e^(-q₁T) N(d₁) | Sensitivity to Asset 1 price |
| ∂/∂S₂ | -e^(-q₂T) N(d₂) | Sensitivity to Asset 2 price |
| ∂²/∂S₁² | e^(-q₁T) n(d₁)/(σ√T S₁(0)) | Gamma with respect to Asset 1 |
| ∂²/∂S₂² | e^(-q₂T) n(d₂)/(σ√T S₂(0)) | Gamma with respect to Asset 2 |
| ∂²/∂S₁∂S₂ | -e^(-q₁T) n(d₁)/(σ√T S₂(0)) | Cross gamma |

**Key identity that simplifies calculations:**
```
e^(-q₁T) S₁(0) n(d₁) = e^(-q₂T) S₂(0) n(d₂)
```

This is the stochastic calculus analog of the Black-Scholes identity S n(d₁) = K e^(-rT) n(d₂).

### Greeks for Black Call

| Greek | Formula |
|-------|---------|
| ∂/∂F | P(0,T') N(d₁) |
| ∂/∂P | F(0) N(d₁) - K N(d₂) |
| ∂²/∂F² | P(0,T') n(d₁)/(σ√T F(0)) |
| ∂²/∂P² | 0 |
| ∂²/∂F∂P | N(d₁) |

**Why ∂²/∂P² = 0?**

P enters linearly in Black's formula — P(0,T')F(0)N(d₁) - P(0,T')KN(d₂). No P² term anywhere, so second derivative is zero.

**Interpretation of ∂/∂P:**

F(0)N(d₁) - KN(d₂) is the sensitivity to interest rates (through bond price P). If rates fall (P rises), call value rises — call is long bond-like. This is the "rho" equivalent for forward options.

### Delta Hedging for Exchange Options

To delta-hedge a written exchange option, hold:
- δ₁ = e^(-q₁T) N(d₁) shares of Asset 1 (long)
- δ₂ = -e^(-q₂T) N(d₂) shares of Asset 2 (short)

### Delta Hedging for Forward Options

For a written call on forward, the hedge is:
- Buy N(d₁) forward contracts
- Buy F(0)N(d₁) - KN(d₂) units of discount bond

The forward contract takes the place of the stock in standard Black-Scholes hedging!

---

## 9. Futures vs Forward Prices

### Three Key Facts

1. **Futures prices are martingales under risk-neutral measure Q**
2. **Forward prices are martingales under T'-forward measure**
3. **When interest rates are non-random, futures prices = forward prices**

### Fact 1: Futures are Martingales Under Q

**Proof:**

Build portfolio V(t):
- Start with zero dollars
- Hold one long futures at F*(0)
- Invest all futures gains/losses in risk-free asset R

Portfolio dynamics: dV = rV dt + dF*

Apply Itô's Lemma to V/R:
```
d(V/R)/(V/R) = dV/V - dR/R = (rV dt + dF*)/V - r dt = dF*/V
```

The r dt terms cancel perfectly! So:
- V/R is a martingale (no-arbitrage)
- d(V/R) ∝ dF*
- Therefore F* is a martingale under Q ✓

**Intuition:** Futures cost zero to enter → no expected profit under Q → martingale.

### Fact 2: Forward Prices are Martingales Under T'-Forward Measure

Portfolio: Long forward + F(0) discount bonds has price P(t,T')F(t).

This is a non-dividend-paying asset. Using P(t,T') as numeraire:
```
P(t,T')F(t) / P(t,T') = F(t) → martingale under T'-forward measure
```

### Fact 3: When Rates Deterministic, F* = F

**Proof sketch:**

When r(t) is deterministic:
- R(T') = exp(∫r dt) is a known constant
- P(0,T') = 1/R(T') is also a known constant

Compute probabilities of event A under both measures:
```
prob^R[A] = R(T') · E[1_A φ(T')]
prob^P[A] = R(T') · E[1_A φ(T')]
```

They're identical! Same measure → same expectations → F*(t) = F(t).

**Why deterministic rates is the key condition:**

When rates are random, R(T') is uncertain. The Q measure weights states by R(T'), while the forward measure weights by 1/P(0,T') (a constant). These weightings differ when R(T') is random.

---

## 10. Futures Options Formula

### Setup

When interest rates are deterministic:
- A call on a futures contract F* maturing at T' has value at T: max(0, F*(T) - K)
- Since rates are deterministic, F* = F (fact 3)
- So the payoff = max(0, F(T) - K) = same as forward option!

### The Formula

```
Call = P(0,T) F*(0) N(d₁) - P(0,T) K N(d₂)
Put  = P(0,T) K N(-d₂) - P(0,T) F*(0) N(-d₁)
```

Where:
```
d₁ = [log(F*(0)/K) + ½σ²T] / (σ√T)
d₂ = d₁ - σ√T
```

**Key difference from forward option:** Uses P(0,T) — the bond maturing at T (option maturity) — NOT P(0,T') (bond maturing at forward maturity). Because futures are settled at T, not T'!

### Practical Implementation in VBA

To value futures options using Black_Call function:
```
Input F = futures price F*(0)
Input P = price of bond maturing when option matures = P(0,T)
```

Note: This differs from forward options where P = P(0,T') — bond matures when the FORWARD matures.

---

## 11. Time-Varying Volatility

### The Result

When σ(t) is a deterministic (non-random) function of time, define:
```
σ²_avg = (1/T) ∫₀^T σ²(t) dt
```

All formulas hold with σ_avg replacing σ.

### Why This Works

All formulas depend on total variance σ²T. With time-varying σ(t):
```
Total variance = ∫₀^T σ²(t) dt = σ²_avg · T
```

Same total variance → same option price. The path of σ(t) doesn't matter, only the total accumulated variance!

### Why Deterministic is Key

If σ(t) is random (stochastic volatility), the distribution of S(T) is no longer lognormal — it's a mixture of lognormals. The simple substitution σ → σ_avg breaks down. Need more complex models (Heston, SABR, etc.).

---

## 12. Market Completeness

### What is Completeness?

A **complete market** is one where every contingent claim can be replicated by a trading strategy. Completeness requires:
- One risk-free asset
- As many risky assets as there are Brownian motions

### This Chapter's Setting is Incomplete

Without a risk-free asset (or with random rates), the market is generally incomplete. Not every claim can be hedged!

### But Some Claims Can Still Be Priced

In Margrabe's model, contingent claims of the form:
```
S₂(T) · X(T)
```
where X(T) depends only on the relative price path S₁(t)/S₂(t) for 0 ≤ t ≤ T can be replicated.

**Why?** Because using Asset 2 as numeraire transforms the problem into a complete market setting — you have one risky asset (S₁/S₂) and one "risk-free" asset (Asset 2 itself, which earns dividend yield q₂).

The exchange option max(0, S₁(T) - S₂(T)) = S₂(T) · max(0, S₁(T)/S₂(T) - 1) fits this form exactly!

---

## 13. VBA Implementation

### Generic Option Pricing Function

All formulas reduce to the same structure:
```vba
Function Generic_Option(P1, P2, sigma, T)
    ' P1 = present value of asset to be received
    ' P2 = present value of asset to be delivered
    x = (Log(P1/P2) + 0.5*sigma*sigma*T) / (sigma*Sqr(T))
    y = x - sigma*Sqr(T)
    N1 = Application.NormSDist(x)
    N2 = Application.NormSDist(y)
    Generic_Option = P1*N1 - P2*N2
End Function
```

### Margrabe's Formula

```vba
Function Margrabe(S1, S2, sigma, q1, q2, T)
    Margrabe = Generic_Option(Exp(-q1*T)*S1, Exp(-q2*T)*S2, sigma, T)
End Function
```

### Black's Formula for Forward/Futures Options

```vba
Function Black_Call(F, K, P, sigma, T)
    ' For forward option: P = bond maturing when forward matures (T')
    ' For futures option: P = bond maturing when option matures (T)
    Black_Call = Generic_Option(P*F, P*K, sigma, T)
End Function
```

The elegance: one generic function handles ALL cases by choosing the right inputs for P1 and P2!

---

## 14. Formula Cheat Sheet

### Margrabe's Formula (Exchange Option)
```
Value = e^(-q₁T) S₁(0) N(d₁) - e^(-q₂T) S₂(0) N(d₂)
d₁ = [log(S₁(0)/S₂(0)) + (q₂-q₁+½σ²)T] / (σ√T)
d₂ = d₁ - σ√T
σ = √(σ₁² + σ₂² - 2ρσ₁σ₂)
```

### Black's Formula (Options on Forwards)
```
Call = P(0,T') F(0) N(d₁) - P(0,T') K N(d₂)
Put  = P(0,T') K N(-d₂) - P(0,T') F(0) N(-d₁)
d₁ = [log(F(0)/K) + ½σ²T] / (σ√T)
d₂ = d₁ - σ√T
```

### Merton's Formula (Stochastic Rates)
```
Call = e^(-qT) S(0) N(d₁) - P(0,T) K N(d₂)
Put  = P(0,T) K N(-d₂) - e^(-qT) S(0) N(-d₁)
d₁ = [log(S(0)/(K·P(0,T))) - qT + ½σ²T] / (σ√T)
d₂ = d₁ - σ√T
```

### Futures Options Formula
```
Call = P(0,T) F*(0) N(d₁) - P(0,T) K N(d₂)
Put  = P(0,T) K N(-d₂) - P(0,T) F*(0) N(-d₁)
d₁ = [log(F*(0)/K) + ½σ²T] / (σ√T)
d₂ = d₁ - σ√T
```

### Deferred Exchange Option
```
Value = e^(-q₁T') S₁(0) N(d₁) - e^(-q₂T') S₂(0) N(d₂)
d₁ = [log(S₁(0)/S₂(0)) + (q₂-q₁)T' + ½σ²T] / (σ√T)
d₂ = d₁ - σ√T
```

### Put-Call Parity for Forward Options
```
Call + P(0,T')K = Put + P(0,T')F(0)
```

### Forward Price Formula (No Arbitrage)
```
F(0) = e^(-qT) S(0) / P(0,T)
```

### Key Relationships
```
F*(T') = F(T') = S(T')    (all converge to spot at maturity)
F*(t) = F(t)              (when interest rates are deterministic)
```

### Martingale Properties
```
F*(t): martingale under risk-neutral measure Q
F(t): martingale under T'-forward measure
Same measure when rates are deterministic
```

---

*Based on FIN4370/5370 — Forward, Futures, and Exchange Options*
*Washington University in St. Louis — Prof. Hong Liu*
