# Itô's Lemma — Comprehensive Reference Guide

A complete reference for Itô calculus, stochastic processes, and related results.
Based on FIN4370/5370 Chapter 7 material.

---

## Table of Contents

1. [Why Stochastic Calculus is Different](#1-why-stochastic-calculus-is-different)
2. [Brownian Motion Basics](#2-brownian-motion-basics)
3. [Itô's Lemma — Core Statement](#3-itôs-lemma--core-statement)
4. [Key Application: d(log S)](#4-key-application-dlog-s)
5. [Volatility of a Ratio of Two Assets](#5-volatility-of-a-ratio-of-two-assets)
6. [The Money Market Account R(t)](#6-the-money-market-account-rt)
7. [Martingales and No-Arbitrage](#7-martingales-and-no-arbitrage)
8. [Futures Prices are Martingales Under Q](#8-futures-prices-are-martingales-under-q)
9. [Forward Prices are Martingales Under T-Forward Measure](#9-forward-prices-are-martingales-under-t-forward-measure)
10. [State Price Density and Measure Changes](#10-state-price-density-and-measure-changes)
11. [When Futures = Forward Prices](#11-when-futures--forward-prices)
12. [Time-Varying Volatility](#12-time-varying-volatility)
13. [Common Mistakes and Pitfalls](#13-common-mistakes-and-pitfalls)
14. [Quick Reference Summary](#14-quick-reference-summary)

---

## 1. Why Stochastic Calculus is Different

In ordinary calculus, if f is a smooth function and x(t) is a smooth path:

```
df = f'(x) dx        (ordinary calculus)
```

In stochastic calculus, this is **WRONG** when x follows a Brownian motion. The reason is:

- In ordinary calculus: (dx)² → 0 as dt → 0
- In stochastic calculus: (dB)² = dt  ← this does NOT vanish!

This is the fundamental difference. The quadratic variation of Brownian motion is non-zero, which forces an extra correction term in every derivative calculation.

**Key multiplication rules (Itô's table):**

| × | dt | dB |
|---|----|----|
| dt | 0 | 0 |
| dB | 0 | dt |

So (dB)² = dt, (dt)² = 0, and dB·dt = 0.

---

## 2. Brownian Motion Basics

A standard Brownian motion B(t) satisfies:
- B(0) = 0
- Increments B(t) - B(s) ~ N(0, t-s) for t > s
- Increments are independent across non-overlapping intervals
- Paths are continuous but nowhere differentiable

**Geometric Brownian Motion (GBM):** The standard model for stock prices

```
dS/S = μ dt + σ dB
```

Or equivalently:
```
dS = μS dt + σS dB
```

Where:
- μ = drift (expected return)
- σ = volatility
- B = standard Brownian motion

---

## 3. Itô's Lemma — Core Statement

For a function f(S, t) where S follows dS = a dt + b dB:

```
df = (∂f/∂t) dt + (∂f/∂S) dS + (1/2)(∂²f/∂S²)(dS)²
```

Expanding (dS)² = b² dt (using Itô's table):

```
df = [∂f/∂t + a·∂f/∂S + (1/2)b²·∂²f/∂S²] dt + b·∂f/∂S dB
```

**The critical extra term is: (1/2)(∂²f/∂S²)(dS)²**

This has NO counterpart in ordinary calculus. It arises purely because (dB)² = dt ≠ 0.

### Intuition for the Correction Term

If f is **concave** (like log), then f''(S) < 0, so the correction term is negative — the drift of f is less than you'd expect from ordinary calculus. This is Jensen's inequality in action:

```
E[log S] < log(E[S])
```

The -½σ² term is exactly this Jensen's inequality correction.

---

## 4. Key Application: d(log S)

**Setup:** S follows GBM: dS = μS dt + σS dB

**Goal:** Find d(log S)

**Step 1:** Let f(S) = log(S). Then:
- f'(S) = 1/S
- f''(S) = -1/S²

**Step 2:** Apply Itô's Lemma:

```
d(log S) = f'(S) dS + (1/2) f''(S) (dS)²
```

**Step 3:** Compute each term:
- First term: (1/S)(μS dt + σS dB) = μ dt + σ dB
- Second term: (1/2)(-1/S²)(σS)² dt = -½σ² dt

**Result:**
```
d(log S) = (μ - ½σ²) dt + σ dB
```

**Why the -½σ² matters:**
- Without it: you'd think log S drifts at rate μ
- With it: log S actually drifts at rate μ - ½σ²
- The difference is the Itô correction — it appears because log is concave

**Integrated form:**
```
log S(T) = log S(0) + (μ - ½σ²)T + σ B(T)
```

So:
```
S(T) = S(0) · exp[(μ - ½σ²)T + σ B(T)]
```

This tells us S(T) is **lognormally distributed**.

---

## 5. Volatility of a Ratio of Two Assets

**Setup:**
- dS₁/S₁ = μ₁ dt + σ₁ dB₁
- dS₂/S₂ = μ₂ dt + σ₂ dB₂
- Corr(dB₁, dB₂) = ρ (so dB₁·dB₂ = ρ dt)

**Goal:** Find the volatility of the ratio R = S₁/S₂

**Step 1:** Take logs: log R = log S₁ - log S₂

**Step 2:** Apply d(log S) formula to each:
```
d(log S₁) = (μ₁ - ½σ₁²) dt + σ₁ dB₁
d(log S₂) = (μ₂ - ½σ₂²) dt + σ₂ dB₂
```

**Step 3:** Subtract:
```
d(log R) = (μ₁ - μ₂ - ½σ₁² + ½σ₂²) dt + σ₁ dB₁ - σ₂ dB₂
```

**Step 4:** The diffusion part is σ₁ dB₁ - σ₂ dB₂. Its variance per unit time is:

```
Var(σ₁ dB₁ - σ₂ dB₂) = σ₁² + σ₂² - 2ρσ₁σ₂
```

(This is just variance of a difference: Var(X - Y) = Var(X) + Var(Y) - 2Cov(X,Y))

**Result — Volatility of ratio:**
```
σ_ratio = √(σ₁² + σ₂² - 2ρσ₁σ₂)
```

**Geometric intuition (law of cosines):**

This is literally the law of cosines! Think of σ₁ and σ₂ as vectors with angle θ between them where cos(θ) = ρ. Then σ_ratio is the length of their difference vector.

**Special cases:**
- ρ = 1 (perfect correlation): σ_ratio = |σ₁ - σ₂| → ratio barely moves
- ρ = -1 (perfect negative correlation): σ_ratio = σ₁ + σ₂ → maximum uncertainty
- ρ = 0 (independent): σ_ratio = √(σ₁² + σ₂²) → Pythagorean theorem

**Important note from slides:** σ₁, σ₂, and ρ individually can be random processes. Only the combined σ_ratio needs to be constant for the option pricing formulas to work!

---

## 6. The Money Market Account R(t)

**Definition:** The money market account with time-varying rate r(t):

```
dR = r(t) R dt
```

This says: at every instant, your account earns rate r(t) proportionally.

**Solving the ODE:**

Rearrange:
```
dR/R = r(t) dt
```

Integrate both sides:
```
∫₀ᵗ dR/R = ∫₀ᵗ r(s) ds
log R(t) - log R(0) = ∫₀ᵗ r(s) ds
```

Since R(0) = 1 (start with $1):
```
R(t) = exp(∫₀ᵗ r(s) ds)
```

**Why this form?**
- The integral ∫₀ᵗ r(s) ds accumulates all instantaneous rates over time
- Exponentiating because interest compounds continuously
- If r is constant: R(t) = e^(rt) ✓ (the familiar result)

**Key property:** R(t) is used as the numeraire for the risk-neutral measure Q.

---

## 7. Martingales and No-Arbitrage

**Definition:** A process X(t) is a martingale under measure P if:
```
E^P_t[X(T)] = X(t)  for all T > t
```

No expected drift — best forecast of future value is current value.

**Fundamental Pricing Principle:**

For ANY self-financing portfolio V and numeraire N:
```
V(t)/N(t) is a martingale under the measure associated with N
```

If this fails → **arbitrage exists**.

This is the foundation of all option pricing!

**Self-financing:** A portfolio is self-financing if all value changes come from price changes — no external cash injected or withdrawn.

---

## 8. Futures Prices are Martingales Under Q

**Setup:** Portfolio V(t) starts at zero and holds:
- One long futures contract at price F*(0)
- Gains/losses continuously invested in R(t)

**Portfolio dynamics:**
```
dV = rV dt + dF*
```
- rV dt: interest earned on R position
- dF*: instantaneous futures gain/loss

**Apply Itô's Lemma to V/R:**

```
d(V/R)/(V/R) = dV/V - dR/R
             = (rV dt + dF*)/V - r dt
             = dF*/V
```

The r dt terms cancel exactly! This cancellation is the whole point of the construction.

**Conclusion:** Since V(0) = 0 (zero initial investment), and V/R must be a martingale under Q (no arbitrage), and d(V/R) ∝ dF*, we get:

```
F*(t) is a martingale under Q
```

**Intuition:** Futures require zero initial investment → can't earn positive expected return under Q → must be a martingale.

---

## 9. Forward Prices are Martingales Under T-Forward Measure

**Setup:** Consider the portfolio:
- Long one forward contract (delivery F(0) at T')
- Buy F(0) units of discount bond P(t, T')

**Portfolio value:**
```
Value of forward: P(t,T')[F(t) - F(0)]
Value of bonds: F(0)·P(t,T')
Total: P(t,T')F(t)  ← this is a non-dividend-paying asset!
```

**Using discount bond as numeraire:**

When we use P(t,T') as numeraire, the ratio:
```
P(t,T')F(t) / P(t,T') = F(t)
```
must be a martingale. So:
```
F(t) is a martingale under the T'-forward measure
```

**Why different from futures?**
- Futures → settled daily → naturally linked to R(t) → Q measure
- Forwards → settled at T' → naturally linked to P(t,T') → T'-forward measure

Each contract is "at home" in its natural measure.

---

## 10. State Price Density and Measure Changes

**State price density φ(t):** Prices today of $1 in a specific state at time t.

The price of any asset paying X(T) at T is:
```
Price = E[φ(T) · X(T)]
```

**Measure change formula:** For numeraire N, the probability of event A under N-measure is:
```
prob^N[A] = E[1_A · φ(T') · N(T')/N(0)]
```

Where:
- 1_A = 1 if event A occurs, 0 otherwise
- φ(T') = state price density
- N(T')/N(0) = growth of numeraire

**Intuition:** The measure reweights probabilities by how valuable the numeraire is in each state. States where N is worth more get higher probability weight.

**For risk-neutral measure Q (numeraire = R):**
```
prob^R[A] = E[1_A · φ(T') · R(T')]
```

**For T'-forward measure P (numeraire = P(t,T')):**
```
prob^P[A] = E[1_A · φ(T') · 1/P(0,T')]
```

---

## 11. When Futures = Forward Prices

**Claim:** When interest rates are deterministic, F*(t) = F(t).

**Proof:**

When r(t) is deterministic:
```
R(T') = exp(∫₀^T' r(t)dt)  ← known constant, not random!
P(0,T') = exp(-∫₀^T' r(t)dt) = 1/R(T')  ← also a known constant!
```

Now compute prob^R[A]:
```
prob^R[A] = E[1_A · φ(T') · R(T')]
          = R(T') · E[1_A · φ(T')]   ← R(T') is constant, pull out
```

And prob^P[A]:
```
prob^P[A] = E[1_A · φ(T') / P(0,T')]
          = R(T') · E[1_A · φ(T')]   ← since 1/P(0,T') = R(T')
```

**They are identical!** So Q and T'-forward measure are the same when rates are deterministic.

**Therefore:**
```
F*(t) = E^Q_t[F*(T')]    (futures martingale under Q)
       = E^Q_t[F(T')]     (F*(T') = F(T') at maturity — both = S(T'))
       = E^P_t[F(T')]     (measures are the same)
       = F(t)             (forward martingale under P)
```

**When rates are random:** The two measures diverge because R(T') is no longer a constant — it becomes uncertain, and each measure weights that uncertainty differently.

---

## 12. Time-Varying Volatility

When volatility σ(t) is a deterministic (non-random) function of time:

**Definition of average volatility:**
```
σ²_avg = (1/T) ∫₀^T σ²(t) dt
```

**Key result:** All option pricing formulas still hold — just replace σ with σ_avg.

**Why this works:** The key quantity in all formulas is σ²T (total variance). With time-varying volatility:
```
∫₀^T σ²(t) dt = σ²_avg · T
```
So total variance is the same whether we use σ_avg constant or σ(t) time-varying.

**Important:** This ONLY works when σ(t) is non-random (deterministic). If σ itself is a random process, the formulas break down (stochastic volatility models require more complex treatment).

---

## 13. Common Mistakes and Pitfalls

### Mistake 1: Forgetting the Itô Correction

**Wrong:**
```
d(log S) = (1/S) dS = μ dt + σ dB
```

**Right:**
```
d(log S) = (μ - ½σ²) dt + σ dB
```

The -½σ² is not optional — it fundamentally changes the drift!

### Mistake 2: Confusing σ_ratio Requirement

The individual σ₁, σ₂, ρ CAN be random. Only the combined:
```
σ = √(σ₁² + σ₂² - 2ρσ₁σ₂)
```
needs to be constant for Margrabe's formula to apply.

### Mistake 3: Using Wrong Measure for Each Price

- Futures prices: martingale under Q (risk-neutral)
- Forward prices: martingale under T'-forward measure
- They are NOT both martingales under the same measure (unless rates are deterministic)

### Mistake 4: Ordinary Calculus Rules in Stochastic Setting

Cannot use:
- Chain rule without Itô correction
- Product rule without cross-variation terms
- Integration by parts without stochastic correction

### Mistake 5: (dB)² ≠ 0

In ordinary calculus (dx)² → 0. In stochastic calculus:
```
(dB)² = dt    (NOT zero!)
```
This is the source of all the extra terms.

---

## 14. Quick Reference Summary

### Itô's Lemma
```
df(S,t) = ∂f/∂t dt + ∂f/∂S dS + ½ ∂²f/∂S² (dS)²
```

### d(log S) for GBM
```
dS/S = μ dt + σ dB  →  d(log S) = (μ - ½σ²) dt + σ dB
```

### Volatility of Ratio S₁/S₂
```
σ_ratio = √(σ₁² + σ₂² - 2ρσ₁σ₂)
```

### Money Market Account
```
dR = rR dt  →  R(t) = exp(∫₀ᵗ r(s) ds)
```

### Martingale Properties
| Process | Martingale Under |
|---------|-----------------|
| V/R (any self-financing V) | Risk-neutral Q |
| Futures price F* | Risk-neutral Q |
| Forward price F | T'-forward measure |
| F* = F | Both (when r deterministic) |

### Itô Multiplication Table
| × | dt | dB |
|---|----|----|
| dt | 0 | 0 |
| dB | 0 | dt |

### Time-Varying Volatility
```
σ²_avg = (1/T) ∫₀^T σ²(t) dt  →  use σ_avg in all formulas
```

---

*Reference compiled from FIN4370/5370 — Forward, Futures, and Exchange Options (Chapter 7)*
*Washington University in St. Louis — Prof. Hong Liu*
