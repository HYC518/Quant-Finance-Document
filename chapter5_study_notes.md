# Chapter 5: Introduction to Monte Carlo and Binomial Models — Study Notes

**FIN 4370 & 5370 — Advanced Derivative Securities**
**Professor Jian Cai / Spring 2026**

---

## Table of Contents

1. [Overview and Big Picture](#1-overview-and-big-picture)
2. [Introduction to Monte Carlo](#2-introduction-to-monte-carlo)
3. [Two Main Drawbacks of Monte Carlo](#3-two-main-drawbacks-of-monte-carlo)
4. [Introduction to Binomial Models](#4-introduction-to-binomial-models)
5. [Valuation with a Binomial Tree](#5-valuation-with-a-binomial-tree)
6. [Monte Carlo vs Binomial Tree](#6-monte-carlo-vs-binomial-tree)
7. [Backward Induction](#7-backward-induction)
8. [Binomial Models for American Options](#8-binomial-models-for-american-options)
9. [Binomial Tree Parameters](#9-binomial-tree-parameters)
10. [Numerical Greeks](#10-numerical-greeks)
11. [VBA Implementations](#11-vba-implementations)
12. [FAQ](#12-faq)
13. [Chapter Summary](#13-chapter-summary)

---

## 1. Overview and Big Picture

This chapter introduces **two principal numerical methods** for valuing derivative securities when closed-form solutions don't exist:

1. **Monte Carlo (MC)** — simulate many sample paths and average the payoffs
2. **Binomial models** — discretize the stock price into a recombining tree and work backward

### Two Featured Applications

| Application | Method | Why |
|------------|--------|-----|
| Valuing European options under stochastic volatility | Monte Carlo | Path-dependent (volatility evolves) but no early exercise |
| Valuing American options | Binomial tree | Backward induction handles early-exercise check naturally |

### The Underlying Pricing Formula

Both methods compute the same risk-neutral expectation from Chapter 2:

$$\boxed{V_0 = e^{-rT}\, E^R[X] \quad (1)}$$

where $X$ is the payoff at $T$. They just **approximate the expectation differently**:

- **MC:** $E^R[X] \approx \frac{1}{M}\sum_{i=1}^{M} x_i$ (sample mean)
- **Binomial:** $E^R[X] \approx \sum_{i=0}^{N} \binom{N}{i} p^i(1-p)^{N-i} \cdot x_i$ (discrete probability-weighted sum)

---

## 2. Introduction to Monte Carlo

### 2.1 Core Idea

The risk-neutral pricing formula (1) says the value of a derivative paying $x$ at $T$ is

$$V_0 = e^{-rT}\, E^R[x]$$

Monte Carlo estimates this expectation by:
1. **Simulating** $M$ sample values of the random variable $x$
2. **Averaging** the sample to estimate $E^R[x]$
3. **Discounting** by $e^{-rT}$

### 2.2 Application: European Call

For a European call, $x = \max(0, S(T) - K)$. Under Black-Scholes assumptions, $\log S(T)$ is normally distributed under the risk-neutral measure:

$$\log S(T) \sim \mathcal{N}\big(\log S(0) + \nu T,\ \sigma^2 T\big)$$

where

$$\boxed{\nu = r - q - \tfrac{1}{2}\sigma^2}$$

> **Why $\nu$ has a $-\sigma^2/2$ term:** This is Itô's lemma at work. From $dS/S = (r-q)dt + \sigma dB^R$, applying Itô's lemma to $\log S$ gives $d\log S = (r-q-\sigma^2/2)dt + \sigma dB^R$. The variance correction comes from $(dB)^2 = dt$.

### 2.3 The Simulation Formula

Generate standard normals $z_1, z_2, \ldots, z_M$. Then:

$$\log S^{(i)}(T) = \log S(0) + \nu T + \sigma\sqrt{T}\, z_i$$

Equivalently:

$$\boxed{S^{(i)}(T) = S(0) \exp\big(\nu T + \sigma\sqrt{T}\, z_i\big)}$$

The sample payoff is:

$$x_i = \max\big(0,\ S^{(i)}(T) - K\big)$$

Final price estimate:

$$\hat{V}_0 = e^{-rT} \cdot \frac{1}{M}\sum_{i=1}^{M} x_i$$

### 2.4 One-Step vs Path Simulation

| Option type | Time discretization needed |
|-------------|---------------------------|
| European (path-independent) | **No** — jump directly from $t=0$ to $t=T$ in one step |
| Asian, barrier, lookback (path-dependent) | **Yes** — simulate full path with $N$ small time steps |
| Stochastic volatility (e.g. GARCH) | **Yes** — must update vol each step |

---

## 3. Two Main Drawbacks of Monte Carlo

### 3.1 Drawback 1: Difficult to Value Early-Exercise Features

The early exercise value at any date depends on the value of exercising early at **later** dates, which depends on still **later** dates, etc.

**Why this is a problem for MC:** MC simulates *forward* in time. But early-exercise decisions need to be made *backward* — you need to know future option values at each state to compare against immediate exercise.

> **Important nuance:** You cannot simply do "naive backward induction" on MC paths. Each path has only **one** future from any given state, not a distribution of futures. The continuation value $E[V_{t+1} \mid S_t = s]$ is a *conditional expectation* — and forward simulation doesn't give this naturally. (Trees do, because they recombine.)

This is why early exercise needs special MC techniques like the **Longstaff-Schwartz regression** method (covered later in the course).

### 3.2 Drawback 2: Slow Convergence

The standard error of the sample mean $\bar{x}$ is:

$$\boxed{\text{SE}(\bar{x}) = \sqrt{\frac{1}{M(M-1)}\left[\sum_{i=1}^M x_i^2 - M\bar{x}^2\right]} \quad (2)}$$

This is small only when $M$ is **very large**. Specifically:

$$\text{SE} \propto \frac{1}{\sqrt{M}}$$

| To reduce error by | Need to increase $M$ by |
|--------------------|-------------------------|
| Factor of 2 | Factor of 4 |
| Factor of 10 | Factor of 100 |
| Factor of 100 | Factor of 10,000 |

This is why **variance reduction techniques** (antithetic variates, control variates, importance sampling) are heavily used in practice.

---

## 4. Introduction to Binomial Models

### 4.1 The Setup

Starting from price $S$, after one period the stock takes one of two values:

- $uS$ with probability $p$ (up)
- $dS$ with probability $1-p$ (down)

where $u > 1 > d > 0$.

### 4.2 Three Parameters

| Parameter | Meaning |
|-----------|---------|
| $u$ | Up-factor (multiplicative) |
| $d$ | Down-factor (multiplicative) |
| $p$ | Risk-neutral probability of up move |

### 4.3 Recombining Trees

A tree is **recombining** if an up-down sequence reaches the same node as a down-up sequence:

$$ud \cdot S = du \cdot S$$

This drastically reduces computation:

| Tree type | Number of nodes at date $N$ |
|-----------|----------------------------|
| Recombining | $N+1$ |
| Bushy (non-recombining) | $2^N$ |

For $N = 30$: recombining gives 31 nodes; bushy gives over **a billion**.

### 4.4 Three-Period Tree Structure

```
                        u³S
                       /
                  u²S
                 /    \
             uS         u²dS
            /  \       /
           S    udS
            \  /     \
             dS       ud²S
                 \    /
                  d²S
                       \
                        d³S
```

After $N$ periods, the price at the node with $i$ ups and $N-i$ downs is $u^i d^{N-i} S$.

---

## 5. Valuation with a Binomial Tree

### 5.1 European Call Formula

In an $N$-period model, the probability of $i$ ups and $N-i$ downs is:

$$\binom{N}{i} p^i (1-p)^{N-i} = \frac{N!}{i!(N-i)!}\, p^i (1-p)^{N-i}$$

The European call price using an $N$-period binomial tree is:

$$\boxed{C_0 = e^{-rT} \sum_{i=0}^{N} \binom{N}{i} p^i (1-p)^{N-i} \max\big(u^i d^{N-i} S - K,\ 0\big) \quad (3)}$$

This is just discounted expectation under the binomial probability distribution.

### 5.2 Why This Works for European Only

Equation (3) is a **direct sum over terminal nodes** — it ignores all intermediate values. This is fine for European because the payoff depends only on $S(T)$.

For **American** options, you need values at every interior node to check the early-exercise condition — so you can't use (3) directly. You must use backward induction.

---

## 6. Monte Carlo vs Binomial Tree

Both methods converge to the continuous-time distribution of $S(T)$ as the number of points increases. But each has different strengths.

### 6.1 Comparison Table

| Criterion | Monte Carlo | Binomial Tree |
|-----------|-------------|---------------|
| Path-independent European options | Slow ($O(M)$ for given accuracy) | **Fast** (exact via formula 3) |
| American options | **Hard** (needs Longstaff-Schwartz) | **Easy** (backward induction) |
| Path-dependent options | **Faster** (simulate $M$ paths) | Slow ($2^N$ paths needed) |
| Multiple underlying assets | **Better** (no curse of dimensionality) | Worse (exponential in dimensions) |
| Convergence rate | $1/\sqrt{M}$ | $1/N$ (typically) |

### 6.2 Decision Rule

| Option type | Method of choice |
|-------------|------------------|
| European, single asset | Black-Scholes if available; else either |
| American, single asset | **Binomial tree** |
| Path-dependent, single asset | **Monte Carlo** |
| Multi-asset (rainbow, basket) | **Monte Carlo** |
| American on multiple assets | Both struggle — use Longstaff-Schwartz MC |

---

## 7. Backward Induction

### 7.1 The Procedure

Backward induction is the systematic way to compute option values throughout the tree.

**Step 1:** At the **last date** ($t = T$), compute the option payoff at each of the $N+1$ terminal nodes.

Store the value at the bottom node as $C(0)$, the next node up as $C(1)$, ..., the top node as $C(N)$.

**Step 2:** At the **second-to-last date**, compute:

$$\boxed{C(i) = e^{-r\Delta t} p\, C(i+1) + e^{-r\Delta t}(1-p)\, C(i) \quad (4)}$$

Notice $C(i)$ on the LHS is the *new* value at date $N-1$, while $C(i)$ on the RHS is the *old* (just-computed) value at date $N$ — same array index, different time. This **in-place overwriting** keeps memory at $O(N)$ instead of $O(N^2)$.

**Step 3:** Continue iterating backward until you reach date 0. The answer is $C(0)$ at the final iteration.

### 7.2 Why This Approach is Essential

Backward induction calculates the option value **at every node** in the tree, not just at $t=0$. This is what makes assessing the value of early exercise possible.

For European options, backward induction gives the same answer as formula (3) — just slower. The real power shows up for American options.

---

## 8. Binomial Models for American Options

### 8.1 The Modification

For American options, at each node check whether **immediate exercise** is more valuable than **continuing**:

$$\text{Option value} = \max\big(\underbrace{\text{intrinsic value}}_{\text{exercise now}},\ \underbrace{\text{discounted expected value}}_{\text{continue}}\big)$$

When intrinsic value exceeds the continuation value, **early exercise is optimal** — replace the discounted expected value with the intrinsic value.

### 8.2 American Put: Equations (5) and (6)

For a **European put**, we back up with:

$$P(i) = e^{-r\Delta t} p\, P(i+1) + e^{-r\Delta t}(1-p)\, P(i) \quad (5)$$

For an **American put**, we replace (5) with:

$$\boxed{P(i) = \max\big(K - u^i d^{N-i} S,\ e^{-r\Delta t} p\, P(i+1) + e^{-r\Delta t}(1-p)\, P(i)\big) \quad (6)}$$

### 8.3 Why Early Exercise Matters More for Puts than Calls

> **Question on slide 14:** Why is early exercise more important for puts than for calls?

**Answer:**
- For a **call** on a non-dividend-paying stock, early exercise is **never optimal** (the time value of money makes waiting always better). Even with dividends, early exercise of calls is rare.
- For a **put**, the maximum payoff is bounded ($K$ at $S=0$). Once the stock falls deeply, you'd rather **collect $K$ now** and earn interest on it than wait.

The asymmetry comes from the fact that interest rates favor delaying call exercise (you delay paying $K$) but punish delaying put exercise (you delay receiving $K$).

---

## 9. Binomial Tree Parameters

The $u$, $d$, and $p$ parameters must be chosen so the tree converges to GBM as $N \to \infty$.

### 9.1 The Convergence Conditions

Recall under GBM (in log form):

$$\Delta \log S = \nu \Delta t + \sigma \Delta B^R$$

where $\nu = r - q - \sigma^2/2$. Therefore:

$$\frac{E^R[\Delta \log S]}{\Delta t} = \nu, \qquad \frac{\text{Var}^R[\Delta \log S]}{\Delta t} = \sigma^2$$

In the binomial model, $\Delta \log S$ takes value $\log u$ w.p. $p$ and $\log d$ w.p. $1-p$. So:

$$\frac{E^R[\Delta \log S]}{\Delta t} = \frac{p \log u + (1-p) \log d}{\Delta t}$$

$$\frac{\text{Var}^R[\Delta \log S]}{\Delta t} = \frac{p(1-p)(\log u - \log d)^2}{\Delta t}$$

> **Derivation of the variance:** For a two-point distribution taking value $a$ w.p. $p$ and $b$ w.p. $1-p$, the variance is $p(1-p)(a-b)^2$. This is a standard result and can be verified by computing $E[X^2] - (E[X])^2$ directly.

### 9.2 Convergence Conditions

For the tree to converge to GBM as $N \to \infty$ with $T$ fixed:

$$\boxed{\frac{p \log u + (1-p) \log d}{\Delta t} \to \nu, \qquad \frac{p(1-p)(\log u - \log d)^2}{\Delta t} \to \sigma^2}$$

These two equations have **three unknowns** ($u$, $d$, $p$), so we need one extra condition. Different choices give different popular schemes.

### 9.3 The Cox-Ross-Rubinstein (CRR) Model — Most Popular

**Extra condition:** $d = 1/u$ (symmetric in log-space)

$$\boxed{u = e^{\sigma\sqrt{\Delta t}}, \qquad d = \frac{1}{u}, \qquad p = \frac{e^{(r-q)\Delta t} - d}{u - d} \quad (7\text{a, b})}$$

**Why CRR is popular:**
- $d = 1/u$ makes the tree symmetric — easy to visualize
- Up and down moves are balanced: equal log-distances from current price
- Simple parameter formulas

### 9.4 The Jarrow-Rudd Model

**Extra condition:** $p = 1/2$ (equal probability)

$$u = \exp\big[(r - q - \tfrac{1}{2}\sigma^2)\Delta t + \sigma\sqrt{\Delta t}\big]$$

$$d = \exp\big[(r - q - \tfrac{1}{2}\sigma^2)\Delta t - \sigma\sqrt{\Delta t}\big]$$

**Pros:** Equal probabilities make the tree look like a "natural" walk. **Cons:** Tree is asymmetric in price space.

### 9.5 Mean-Variance Matching Method

**Exact moment matching** with $d = 1/u$:

$$\log u = \sqrt{\sigma^2 \Delta t + \nu^2 (\Delta t)^2}, \qquad p = \frac{1}{2} + \frac{\nu \Delta t}{2 \log u} \quad (9\text{a, b})$$

This matches the mean and variance **exactly** for finite $\Delta t$ — not just in the limit. Slightly more accurate than CRR for small $N$.

### 9.6 Comparison

| Method | Extra condition | Strengths |
|--------|----------------|-----------|
| CRR | $d = 1/u$ | Most popular, symmetric tree |
| Jarrow-Rudd | $p = 1/2$ | Equal probabilities |
| Mean-variance matching | $d = 1/u$, exact moments | Most accurate at small $N$ |

In practice all three converge to Black-Scholes for large $N$. The differences matter mainly for small $N$ or specific computational reasons.

---

## 10. Numerical Greeks

### 10.1 General Approach: Finite Differences

To estimate any Greek (vega, delta, gamma, ...), run the valuation **twice** with slightly different parameter values, then compute:

$$\text{Greek} \approx \frac{V(\text{param}_H) - V(\text{param}_L)}{\text{param}_H - \text{param}_L}$$

### 10.2 Estimating Vega

Compute the option value at $0.99\sigma$ and $1.01\sigma$:

$$\boxed{\text{Vega} \approx \frac{C_H - C_L}{1.01\sigma - 0.99\sigma} = \frac{C_H - C_L}{0.02\sigma}}$$

Smaller bumps (e.g., $0.999\sigma$ and $1.001\sigma$) give more precise estimates **in principle**, but in MC the noise can dominate the signal at very small bumps.

### 10.3 Estimating Delta and Gamma

To estimate gamma at price $S$, you need values at three prices: $S_d < S < S_u$.

Let $C_u, C, C_d$ be the option values at $S_u, S, S_d$. Two natural delta estimates:

$$\delta_u = \frac{C_u - C}{S_u - S}, \qquad \delta_d = \frac{C - C_d}{S - S_d}$$

- $\delta_u$ approximates delta at the **midpoint** of $[S, S_u]$
- $\delta_d$ approximates delta at the **midpoint** of $[S_d, S]$

Then gamma (the derivative of delta) is:

$$\boxed{\Gamma \approx \frac{\delta_u - \delta_d}{(S_u - S_d)/2}}$$

### 10.4 Common Random Numbers (Critical for MC Greeks!)

When estimating Greeks via MC, **always reuse the same random numbers** for both bumped valuations. Otherwise the noise in $C_H - C_L$ overwhelms the signal.

This trick is used explicitly in `European_Call_MC` (slide 21): the same `LogS` is used for both the up-bumped and down-bumped versions.

---

## 11. VBA Implementations

### 11.1 European Call via Monte Carlo (slides 20-21)

The function `European_Call_MC(S, K, r, sigma, q, T, M)` does **three things at once**:

1. Computes the call price (sum of payoffs / $M$, discounted)
2. Computes **Delta Method 1** — finite difference (bump and revalue)
3. Computes **Delta Method 2** — pathwise method

**Key precomputed quantities:**

```vba
LogS0 = Log(S)
drift = (r - q - 0.5 * sigma * sigma) * T   ' = ν·T
SigSqrT = sigma * Sqr(T)                     ' = σ·√T
UpChange = Log(1.01)                         ' for delta method 1
DownChange = Log(0.99)                       ' for delta method 1
```

Pulling these out of the loop saves $M$ redundant calculations.

**Main loop logic:**

```vba
For i = 1 To M
    LogS = LogS0 + drift + SigSqrT * RandN()  ' simulate log S(T)
    CallV = Max(0, Exp(LogS) - K)             ' payoff
    SumCall = SumCall + CallV
    ' Delta Method 1: bump up and down
    LogSu = LogS + UpChange
    CallVu = Max(0, Exp(LogSu) - K)
    LogSd = LogS + DownChange
    CallVd = Max(0, Exp(LogSd) - K)
    SumCallChange = SumCallChange + CallVu - CallVd
    ' Delta Method 2: pathwise
    If Exp(LogS) > K Then
        SumPathwise = SumPathwise + Exp(LogS) / S
    End If
Next i
```

**Final aggregation:**

```vba
CallV  = Exp(-r * T) * SumCall / M
Delta1 = Exp(-r * T) * SumCallChange / (M * 0.02 * S)
Delta2 = Exp(-r * T) * SumPathwise / M
```

### 11.2 Comparison: Two Delta Methods

| Method | Formula | Pros | Cons |
|--------|---------|------|------|
| **Method 1** (finite diff) | $\frac{C(1.01S) - C(0.99S)}{0.02S}$ | Works for any payoff | Bias from finite bump; noisier |
| **Method 2** (pathwise) | $E\big[\mathbb{1}\{S_T > K\} \cdot \frac{S_T}{S_0}\big]$ | Exact (no bias); lower variance | Requires differentiable payoff |

**The pathwise method derivation:** Differentiate the payoff w.r.t. $S_0$ analytically *before* taking the MC average.

$$\Delta = \frac{\partial}{\partial S_0} E^R[e^{-rT}\max(S_T - K, 0)] = e^{-rT} E^R\Big[\mathbb{1}\{S_T > K\} \cdot \frac{S_T}{S_0}\Big]$$

Since $S_T = S_0 e^{\nu T + \sigma\sqrt{T} z}$, we have $\partial S_T / \partial S_0 = S_T / S_0$. The indicator $\mathbb{1}\{S_T > K\}$ is what the `If` statement enforces.

### 11.3 European Call via Binomial Tree (slide 24)

The function `European_Call_Binomial(S, K, r, sigma, q, T, N)` uses a clever trick to **avoid storing the full tree**.

**Setup (CRR parameters):**

```vba
dt = T / N
u  = Exp(sigma * Sqr(dt))
d  = 1 / u
pu = (Exp((r - q) * dt) - d) / (u - d)
pd = 1 - pu
u2 = u * u
```

**Initialize at the bottom terminal node:**

```vba
S    = S * d ^ N            ' bottom-node price = S₀·d^N
prob = pd ^ N               ' probability of all downs
CallV = prob * Max(S - K, 0)
```

**Walk up the terminal nodes** (the clever loop):

```vba
For i = 1 To N
    S    = S * u2                                   ' walk up: price × u²
    prob = prob * (pu / pd) * (N - i + 1) / i       ' update probability
    CallV = CallV + prob * Max(S - K, 0)
Next i
```

**Two key tricks:**

1. **Price update:** Going from node $i-1$ to node $i$ swaps one down for one up, so the price ratio is $u/d = u^2$ (since $d = 1/u$). One multiplication per step.

2. **Probability update:** Compare consecutive binomial terms:

   $$\frac{\binom{N}{i} p^i (1-p)^{N-i}}{\binom{N}{i-1} p^{i-1} (1-p)^{N-i+1}} = \frac{N-i+1}{i} \cdot \frac{p}{1-p}$$

   This recursive update avoids computing factorials directly (which would overflow for large $N$).

**Result:** The whole computation is $O(N)$ time, $O(1)$ memory. Beautiful piece of code.

### 11.4 American Put via Binomial Tree (slides 25-26)

For American options, the European trick **does not work** — you need full backward induction.

**Why:** The early-exercise check requires knowing option values at every node, not just terminal nodes.

**Setup (same as European):**

```vba
ReDim PutV(N)
dt = T / N
u  = Exp(sigma * Sqr(dt))
d  = 1 / u
pu = (Exp((r - q) * dt) - d) / (u - d)
dpu = Exp(-r * dt) * pu      ' precomputed discount × prob
dpd = Exp(-r * dt) * (1 - pu)
u2  = u * u
```

**Initialize all $N+1$ terminal nodes:**

```vba
S = S0 * d ^ N
PutV(0) = Max(K - S, 0)
For j = 1 To N
    S = S * u2
    PutV(j) = Max(K - S, 0)
Next j
```

**Backward induction with early-exercise check:**

```vba
For i = N - 1 To 0 Step -1
    S = S0 * d ^ i                                                ' bottom node
    PutV(0) = Max(K - S, dpd * PutV(0) + dpu * PutV(1))
    For j = 1 To i
        S = S * u2
        PutV(j) = Max(K - S, dpd * PutV(j) + dpu * PutV(j + 1))
    Next j
Next i
```

**Memory trick:** A single 1D array `PutV()` of size $N+1$ is reused across all time slices. The values are overwritten in place — once you compute the new $\text{PutV}(j)$ you no longer need the old one.

This keeps memory at $O(N)$ instead of $O(N^2)$ (which storing the full tree would require).

### 11.5 Why You Can't Use the Walk-Up Trick for American

| Aspect | European | American |
|--------|----------|----------|
| Depends on intermediate nodes? | No | **Yes** (early-exercise check) |
| Can use closed-form sum over terminals? | **Yes** (formula 3) | No |
| Time complexity | $O(N)$ | $O(N^2)$ |
| Memory complexity | $O(1)$ | $O(N)$ |

---

## 12. FAQ

### Q1: Why does $\nu = r - q - \sigma^2/2$ keep showing up?

This is **the same $\nu$** in both Monte Carlo and binomial models because both are discretizations of the same GBM:

$$d \log S = \nu\, dt + \sigma\, dB^R$$

The $-\sigma^2/2$ correction comes from Itô's lemma applied to $\log S$ — it's the Itô variance correction that ensures $E[S(T)] = S(0) e^{(r-q)T}$ (i.e. the stock grows at the risk-neutral rate, not at $r-q$ in log).

### Q2: When should I use MC vs binomial?

| Situation | Method |
|-----------|--------|
| European, single asset, closed form available | Black-Scholes |
| European, single asset, no closed form | Either; binomial often faster |
| American, single asset | **Binomial** |
| Path-dependent (Asian, barrier, lookback) | **Monte Carlo** |
| High dimensional (basket, rainbow) | **Monte Carlo** |
| American + path-dependent + high-dimensional | Longstaff-Schwartz MC |

### Q3: Why can't I just do "naive backward induction" on Monte Carlo paths?

Because you'd need the **conditional expectation** $E[V_{t+1} \mid S_t]$ at each node, but each MC path only has *one* future from any given state (itself). Naive approaches give either:
- **Perfect foresight bias** (using the path's own future overstates the option's value)
- **Wrong conditioning** (averaging over all paths ignores $S_t$)

The **Longstaff-Schwartz** method solves this by using regression to estimate the conditional expectation as a function of $S_t$. The binomial tree avoids this issue entirely because every node has exactly two known children with known probabilities.

### Q4: Why is early exercise more important for puts than calls?

For a non-dividend-paying call, early exercise is never optimal — you're better off delaying $K$ payment to earn interest. For puts, deeply ITM means you'd rather receive $K$ now and earn interest than wait. The asymmetry is driven by the **direction of cash flow**.

### Q5: How fast does Monte Carlo converge?

Standard error decreases as $1/\sqrt{M}$. To halve the error, quadruple the simulations. For 3 decimal places of accuracy, expect $M \sim 10^6$ or more — variance reduction techniques can reduce this dramatically.

### Q6: How fast does the binomial tree converge?

Roughly $O(1/N)$ but with **oscillation** — adding more periods doesn't always reduce error monotonically because of where strikes fall relative to terminal nodes. CRR tree errors can oscillate around the true price.

### Q7: Why is the binomial coefficient updated multiplicatively in the European code?

To avoid factorial overflow and unnecessary computation. The recursive ratio $\frac{N-i+1}{i} \cdot \frac{p}{1-p}$ is numerically stable and lets you compute all $N+1$ binomial probabilities in $O(N)$ total time.

### Q8: What's the role of common random numbers in MC Greeks?

When estimating $\partial V / \partial \sigma$ (vega), if you use **different** random numbers for $V(\sigma_H)$ and $V(\sigma_L)$, the difference $V_H - V_L$ will be dominated by Monte Carlo noise rather than the actual sensitivity. Using the **same** random numbers makes the difference smooth, isolating the parameter effect.

---

## 13. Chapter Summary

This chapter introduces the **two workhorse numerical methods** in derivative pricing:

### Monte Carlo
- Simulate $M$ sample paths under the risk-neutral measure
- Average the discounted payoffs to estimate the price
- **Strengths:** Path-dependent options, high-dimensional problems
- **Weaknesses:** Slow convergence ($1/\sqrt{M}$), early exercise is hard

### Binomial Trees
- Discretize price into a recombining tree of $N$ periods
- Use **backward induction** to handle early exercise naturally
- **Strengths:** American options, intuitive structure, fast for low dimensions
- **Weaknesses:** Path-dependence requires $2^N$ paths; bad for high dimensions

### Tree Parameter Choices
- **CRR** (most popular): $d = 1/u$, $u = e^{\sigma\sqrt{\Delta t}}$
- **Jarrow-Rudd:** $p = 1/2$
- **Mean-variance matching:** Exact moments at finite $\Delta t$

All converge to GBM as $N \to \infty$ — the differences matter only for small $N$.

### Numerical Greeks
Use finite differences for any Greek; always use **common random numbers** in Monte Carlo to control noise. The pathwise method is cleaner when the payoff is differentiable.

### Key Takeaway

> **The binomial method is much better for American options** because trees give you **conditional expectations at every node for free** — the backward induction step is trivial. Monte Carlo struggles because forward-simulation paths don't recombine, so you can't get conditional expectations natively. This is the fundamental reason behind the choice of method.

---

*Reference compiled from FIN4370/5370 — Introduction to Monte Carlo and Binomial Models (Chapter 5)*
*Washington University in St. Louis — Prof. Jian Cai*
