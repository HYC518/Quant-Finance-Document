# Chapter 2: Continuous-Time Models — Study Notes

**FIN 4370 & 5370 — Advanced Derivative Securities**
**Professor Jian Cai / Spring 2026**

---

## Table of Contents

1. [Brownian Motion](#1-brownian-motion)
2. [Quadratic Variation & Why Brownian Motion?](#2-quadratic-variation--why-brownian-motion)
3. [Itô Processes](#3-itô-processes)
4. [Itô's Formula](#4-itôs-formula)
5. [Multiple Itô Processes](#5-multiple-itô-processes)
6. [Reinvesting Dividends](#6-reinvesting-dividends)
7. [Geometric Brownian Motion](#7-geometric-brownian-motion)
8. [Numeraires and Probabilities](#8-numeraires-and-probabilities)
9. [Tail Probabilities of GBM](#9-tail-probabilities-of-gbm)
10. [Volatilities](#10-volatilities)

---

## 1. Brownian Motion

### 1.1 Definition

A **Brownian motion** $B(t)$ is a random process that:
- **Evolves continuously** in time
- **Increments are independent**: $B(u) - B(t)$ is independent of past values
- **Increments are normally distributed**: $B(u) - B(t) \sim N(0, u-t)$
- Convention: $B(0) = 0$

### 1.2 Key Properties

- **Mean zero increments** → Brownian motion is a **martingale**
- For any $u > t$: $E_t[B(u) - B(t)] = 0$, so $E_t[B(u)] = B(t)$ ✓
- **Variance grows linearly**: $\text{Var}(B(u) - B(t)) = u - t$

### 1.3 Simulation

For partition $0 = t_0 < t_1 < \cdots < t_N = T$ with step $\Delta t = T/N$:

$$B(t_i) = B(t_{i-1}) + \sqrt{\Delta t} \cdot z$$

where $z \sim N(0, 1)$ is standard normal.

**Key insight:** $\Delta B \sim \sqrt{\Delta t}$ (not $\Delta t$!) — this is what makes Brownian motion special.

---

## 2. Quadratic Variation & Why Brownian Motion?

### 2.1 Quadratic Variation

For a partition $0 = t_0 < t_1 < \cdots < t_N = T$, the **quadratic variation** is:

$$\lim_{N \to \infty} \sum_{i=1}^N (\Delta B(t_i))^2$$

**For Brownian motion: this limit equals $T$ with probability 1!**

### 2.2 Smooth Functions Have Zero Quadratic Variation

If $X$ is continuously differentiable, then $\Delta X \approx X'(t) \Delta t$, so:
$$\sum (\Delta X)^2 \approx \sum (X')^2 (\Delta t)^2 = T \cdot (X')^2 \cdot \Delta t \to 0$$

**Intuition:** Smooth functions have $(\Delta X)^2 \sim (\Delta t)^2$, which vanishes. Brownian motion has $(\Delta B)^2 \sim \Delta t$, which doesn't!

### 2.3 Why Brownian Motion?

**Logical chain:**
1. **Asset pricing → martingales** (from Chapter 1)
2. **Continuous models are easier than jumpy ones** → study **continuous martingales**
3. **Any non-constant continuous martingale has infinite total variation** (very wiggly)
4. **Lévy's theorem:** A continuous martingale is a Brownian motion **iff** its quadratic variation over $[0, T]$ equals $T$

> **So every continuous martingale is essentially a transformed Brownian motion!**

This is why Brownian motion is the **fundamental building block** of continuous-time finance.

---

## 3. Itô Processes

### 3.1 Definition

An **Itô process** is a variable $X$ satisfying:

$$\boxed{dX(t) = \mu(t) \, dt + \sigma(t) \, dB(t) \quad (1)}$$

where:
- $B$ is a Brownian motion
- $\mu(t)$ is the **drift** (deterministic trend)
- $\sigma(t)$ is the **diffusion coefficient** (random scale)

### 3.2 Integral Form

$$X(T) = X(0) + \int_0^T \mu(t) \, dt + \int_0^T \sigma(t) \, dB(t)$$

### 3.3 Martingale Property

**$X$ is a martingale ⟺ $\mu = 0$** (no drift)

If $\mu = 0$ and $E\left[\int_0^T \sigma^2(t) \, dt\right] < \infty$:
$$\text{Var}(X(T)) = E\left[\int_0^T \sigma^2(t) \, dt\right] \quad (2)$$

### 3.4 Quadratic Variation of Itô Process

$$\lim_{N \to \infty} \sum_{i=1}^N (\Delta X(t_i))^2 = \int_0^T \sigma^2(t) \, dt \quad (3)$$

### 3.5 Itô Calculus Rules (Critical!)

$$\boxed{(dt)^2 = 0, \quad dt \cdot dB = 0, \quad (dB)^2 = dt}$$

These rules let us "compute" with differentials algebraically.

**Example:** $(dX)^2 = (\mu \, dt + \sigma \, dB)^2 = \sigma^2 \, dt$

---

## 4. Itô's Formula

### 4.1 The Big Idea

**Itô's Formula = Second-order Taylor expansion + Itô rules.**

In ordinary calculus, second-order terms vanish (because $(\Delta x)^2 \to 0$). But for Brownian motion, $(\Delta B)^2 \to dt$, so the second-order term **survives**!

### 4.2 Version 1: $Y = g(B)$

If $g$ is twice continuously differentiable:

$$\boxed{dY = g'(B) \, dB + \frac{1}{2} g''(B) \, dt}$$

**The "extra term"** $\frac{1}{2} g''(B) dt$ is the **Itô correction** — it doesn't appear in ordinary calculus!

### 4.3 Version 2: $Z = g(t, X)$ where $X$ is an Itô process

$$\boxed{dZ = \frac{\partial g}{\partial t} dt + \frac{\partial g}{\partial x} dX + \frac{1}{2} \frac{\partial^2 g}{\partial x^2} (dX)^2}$$

Recall $(dX)^2 = \sigma^2 \, dt$.

### 4.4 Version 3: $Z = g(t, X, Y)$ where $X, Y$ are Itô processes

$$\boxed{dZ = \frac{\partial g}{\partial t} dt + \frac{\partial g}{\partial x} dX + \frac{\partial g}{\partial y} dY + \frac{1}{2} \frac{\partial^2 g}{\partial x^2} (dX)^2 + \frac{1}{2} \frac{\partial^2 g}{\partial y^2} (dY)^2 + \frac{\partial^2 g}{\partial x \partial y} dX \, dY \quad (8)}$$

### 4.5 Why the Extra Term?

Second-order Taylor expansion:
$$\Delta Y \approx g'(B) \Delta B + \frac{1}{2} g''(B) (\Delta B)^2$$

- **Ordinary case:** $(\Delta x)^2 \to 0$ → second term vanishes
- **Brownian case:** $(\Delta B)^2 \to dt$ → second term becomes $\frac{1}{2} g''(B) dt$

### 4.6 When Does Itô Formula Reduce to Ordinary Calculus?

**When the function is linear in the Itô process** (or only involves smooth functions/time).

Example: $V = e^{qt} S$ is linear in $S$, so $\frac{\partial^2 g}{\partial S^2} = 0$ → no Itô correction needed.

---

## 5. Multiple Itô Processes

### 5.1 Two Correlated Itô Processes

$$dX = \mu_x \, dt + \sigma_x \, dB_x$$
$$dY = \mu_y \, dt + \sigma_y \, dB_y$$

where $\text{corr}(B_x, B_y) = \rho$.

### 5.2 New Itô Rule

$$\boxed{dB_x \cdot dB_y = \rho \, dt \quad (4d)}$$

So:
$$dX \cdot dY = \sigma_x \sigma_y \rho \, dt$$

### 5.3 Important Examples (from Itô's Formula)

#### Products: $Z = XY$

$$\boxed{\frac{dZ}{Z} = \frac{dX}{X} + \frac{dY}{Y} + \frac{dX}{X} \cdot \frac{dY}{Y} \quad (9)}$$

#### Ratios: $Z = Y/X$

$$\boxed{\frac{dZ}{Z} = \frac{dY}{Y} - \frac{dX}{X} - \frac{dY}{Y} \cdot \frac{dX}{X} + \left(\frac{dX}{X}\right)^2 \quad (10)}$$

#### Exponentials: $Z = e^X$

$$\boxed{\frac{dZ}{Z} = dX + \frac{1}{2}(dX)^2 \quad (11)}$$

#### Logarithms: $Z = \log X$

$$\boxed{dZ = \frac{dX}{X} - \frac{1}{2}\left(\frac{dX}{X}\right)^2 \quad (12)}$$

#### Compounding/Discounting: $Z = YX$ where $Y(t) = \exp\left(\int_0^t q(s) \, ds\right)$

Then $dY = qY \, dt$ (deterministic), and:
$$\boxed{\frac{dZ}{Z} = q \, dt + \frac{dX}{X} \quad (13)}$$

---

## 6. Reinvesting Dividends

### 6.1 Setup

For a stock $S$ with constant dividend yield $q$:
- Dividend in time $dt$ for one share = $q \, dt$ (in dollars per share)
- Reinvest dividends → number of shares grows

### 6.2 Equivalent Non-Dividend-Paying Stock

If we start with 1 share and reinvest all dividends:
- Number of shares at time $t$: $e^{qt}$
- Portfolio value: $V(t) = e^{qt} S(t)$

### 6.3 Dynamics of V

Since $e^{qt}$ is smooth (no $dB$), Itô's formula reduces to:

$$\boxed{\frac{dV}{V} = q \, dt + \frac{dS}{S} \quad (14)}$$

**Interpretation:** Total return ($dV/V$) = price return ($dS/S$) + dividend return ($q \, dt$).

> **Key trick:** $V = e^{qt} S$ converts a dividend-paying stock into an equivalent non-dividend-paying asset. All Chapter 1 results apply directly to $V$!

---

## 7. Geometric Brownian Motion (GBM)

### 7.1 Definition

$$\boxed{S(t) = S(0) \exp\left(\left(\mu - \frac{\sigma^2}{2}\right) t + \sigma B(t)\right) \quad (15)}$$

**Equivalent SDE form:**
$$\boxed{\frac{dS}{S} = \mu \, dt + \sigma \, dB \quad (16)}$$

- $\mu$ = **drift** (expected return)
- $\sigma$ = **volatility**

### 7.2 Log Form

$$d(\log S) = \left(\mu - \frac{\sigma^2}{2}\right) dt + \sigma \, dB \quad (18)$$

**Critical insight:** The drift of $\log S$ is $\mu - \sigma^2/2$, NOT $\mu$!

### 7.3 ⚠️ Common Trap: $S$ vs $\log S$ Martingale

**$S$ and $\log S$ cannot both be martingales** (unless $\sigma = 0$).

| Martingale condition | $S$ | $\log S$ |
|---------------------|-----|----------|
| $\mu = 0$ | ✓ | ✗ (drift $= -\sigma^2/2$) |
| $\mu = \sigma^2/2$ | ✗ | ✓ |

**This is because Itô's formula changes the drift when we transform variables!**

### 7.4 Volatility Drag

The $-\sigma^2/2$ term explains why:
- Geometric mean returns < arithmetic mean returns
- Long-term holding of volatile assets has "drag"

---

## 8. Numeraires and Probabilities

### 8.1 Effect of Changing Probability Measures

When we change probability measure:
- ✅ **Diffusion coefficient stays the same** (volatility is unchanged)
- ✅ **Instantaneous covariance ($dX \, dY$) stays the same**
- ❌ **Drift changes** (Brownian motion under one measure may not be under another)

### 8.2 Setup

- $S$: dividend-paying asset (yield $q$)
- $Y$: another non-dividend-paying asset
- $R$: risk-free asset
- $V = e^{qt} S$: equivalent non-dividend stock

Assumed dynamics:
$$\frac{dR}{R} = r \, dt$$
$$\frac{dS}{S} = \mu_s \, dt + \sigma_s \, dB_s$$
$$\frac{dY}{Y} = \mu_y \, dt + \sigma_y \, dB_y$$

with correlation $\rho$ between $B_s, B_y$.

### 8.3 Risk-Free Asset as Numeraire (Risk-Neutral Measure)

**Goal:** Make $Z = V/R$ a martingale.

**Step 1:** Use ratio formula. Since $R$ is deterministic, $(dR/R)^2 = 0$ and $dV \cdot dR = 0$:
$$\frac{dZ}{Z} = \frac{dV}{V} - \frac{dR}{R} = \frac{dV}{V} - r \, dt = (q - r) \, dt + \frac{dS}{S}$$

**Step 2:** For $Z$ to be martingale, drift of $dS/S$ must be $r - q$:

$$\boxed{\frac{dS}{S} = (r - q) \, dt + \sigma_s \, dB_s^R \quad (21)}$$

where $B_s^R$ is a Brownian motion under the risk-neutral measure.

### 8.4 Underlying Asset as Numeraire

**Goal:** Make $Z = R/V$ a martingale.

By Itô's formula:
$$\frac{dZ}{Z} = r \, dt - \frac{dV}{V} + \left(\frac{dV}{V}\right)^2 = (r - q + \sigma_s^2) \, dt - \frac{dS}{S}$$

For $Z$ to be martingale:

$$\boxed{\frac{dS}{S} = (r - q + \sigma_s^2) \, dt + \sigma_s \, dB_s^V \quad (22)}$$

### 8.5 Another Risky Asset Y as Numeraire

**Goal:** Make $Z = V/Y$ a martingale.

After applying Itô's formula and matching drifts:

$$\boxed{\frac{dS}{S} = (r - q + \rho \sigma_s \sigma_y) \, dt + \sigma_s \, dB_s^Y \quad (23)}$$

**Note:** Formula (23) is the most general — it reduces to (21) when $\rho \sigma_s \sigma_y = 0$ and to (22) when $Y = V$ (so $\sigma_y = \sigma_s$ and $\rho = 1$).

### 8.6 Log Form Summary

For numeraire **num**:
$$d(\log S) = \alpha^{\text{num}} \, dt + \sigma_s \, dB^{\text{num}}$$

| Numeraire | $\alpha^{\text{num}}$ |
|-----------|----------------------|
| Risk-free $R$ | $r - q - \sigma_s^2/2$ |
| Underlying $V$ | $r - q + \sigma_s^2/2$ |
| Other asset $Y$ | $r - q + \rho \sigma_s \sigma_y - \sigma_s^2/2$ |

---

## 9. Tail Probabilities of GBM

### 9.1 Setup

Assume $\alpha^{\text{num}}$ and $\sigma_s = \sigma$ are constants. Then:
$$\log S(T) = \log S(0) + \alpha^{\text{num}} T + \sigma B^{\text{num}}(T)$$

### 9.2 Computing $\text{prob}^{\text{num}}(S(T) \geq K)$

$$S(T) \geq K \Leftrightarrow \log S(T) \geq \log K$$
$$\Leftrightarrow \sigma B^{\text{num}}(T) \geq \log K - \log S(0) - \alpha^{\text{num}} T$$
$$\Leftrightarrow \frac{B^{\text{num}}(T)}{\sqrt{T}} \geq \frac{\log(K/S(0)) - \alpha^{\text{num}} T}{\sigma \sqrt{T}}$$

Since $B^{\text{num}}(T)/\sqrt{T} \sim N(0,1)$, define $z = -B^{\text{num}}(T)/\sqrt{T} \sim N(0,1)$:

$$\Leftrightarrow z \leq \frac{\log(S(0)/K) + \alpha^{\text{num}} T}{\sigma \sqrt{T}}$$

### 9.3 Tail Probability Formulas

$$\boxed{\text{prob}^{\text{num}}(S(T) \geq K) = N(d) \quad (26)}$$

where
$$\boxed{d = \frac{\log(S(0)/K) + \alpha^{\text{num}} T}{\sigma \sqrt{T}} \quad (27)}$$

and $N(\cdot)$ is the standard normal CDF.

$$\text{prob}^{\text{num}}(S(T) \leq K) = N(-d)$$

**This is the foundation of Black-Scholes!** Different choices of numeraire give different $d$ values:
- $d_1$ (using $V$ as numeraire) = $\frac{\log(S(0)/K) + (r - q + \sigma^2/2) T}{\sigma \sqrt{T}}$
- $d_2$ (using $R$ as numeraire) = $\frac{\log(S(0)/K) + (r - q - \sigma^2/2) T}{\sigma \sqrt{T}} = d_1 - \sigma \sqrt{T}$

---

## 10. Volatilities

### 10.1 Setup

Two Itô processes with possibly correlated Brownian motions ($\rho$ correlation):
$$\frac{dX}{X} = \mu_x \, dt + \sigma_x \, dB_x$$
$$\frac{dY}{Y} = \mu_y \, dt + \sigma_y \, dB_y$$

### 10.2 Volatility of Products

For $Z = XY$:

**Apply product rule (formula 9):**
$$\frac{dZ}{Z} = (\mu_x + \mu_y + \rho \sigma_x \sigma_y) \, dt + \sigma_x \, dB_x + \sigma_y \, dB_y \quad (28)$$

**Compute instantaneous variance:**
$$\left(\frac{dZ}{Z}\right)^2 = (\sigma_x dB_x + \sigma_y dB_y)^2 = (\sigma_x^2 + \sigma_y^2 + 2\rho \sigma_x \sigma_y) \, dt$$

**Volatility of $XY$:**
$$\boxed{\sigma_{XY} = \sqrt{\sigma_x^2 + \sigma_y^2 + 2\rho \sigma_x \sigma_y} \quad (29)}$$

### 10.3 Volatility of Ratios

For $Z = Y/X$:

**Apply ratio rule (formula 10):**
$$\frac{dZ}{Z} = (\mu_y - \mu_x - \rho \sigma_x \sigma_y + \sigma_x^2) \, dt + \sigma_y \, dB_y - \sigma_x \, dB_x \quad (30)$$

**Compute instantaneous variance:**
$$\left(\frac{dZ}{Z}\right)^2 = (\sigma_y dB_y - \sigma_x dB_x)^2 = (\sigma_x^2 + \sigma_y^2 - 2\rho \sigma_x \sigma_y) \, dt$$

**Volatility of $Y/X$:**
$$\boxed{\sigma_{Y/X} = \sqrt{\sigma_x^2 + \sigma_y^2 - 2\rho \sigma_x \sigma_y} \quad (31)}$$

### 10.4 Key Insight: Just Like Variance!

These formulas are exactly the variance of sum/difference:
$$\text{Var}(A + B) = \text{Var}(A) + \text{Var}(B) + 2\text{Cov}(A, B)$$
$$\text{Var}(A - B) = \text{Var}(A) + \text{Var}(B) - 2\text{Cov}(A, B)$$

**Why?** Because:
- $\log(XY) = \log X + \log Y$ (product → sum)
- $\log(Y/X) = \log Y - \log X$ (ratio → difference)

**Geometric interpretation (Law of Cosines):**
- $\sigma_{XY}$ = length of vector sum $\vec{\sigma_x} + \vec{\sigma_y}$
- $\sigma_{Y/X}$ = length of vector difference $\vec{\sigma_y} - \vec{\sigma_x}$

### 10.5 Special Cases

| Correlation $\rho$ | $\sigma_{XY}$ | $\sigma_{Y/X}$ |
|-------------------|---------------|----------------|
| $+1$ (perfect positive) | $\sigma_x + \sigma_y$ | $|\sigma_x - \sigma_y|$ |
| $0$ (uncorrelated) | $\sqrt{\sigma_x^2 + \sigma_y^2}$ | $\sqrt{\sigma_x^2 + \sigma_y^2}$ |
| $-1$ (perfect negative) | $|\sigma_x - \sigma_y|$ | $\sigma_x + \sigma_y$ |

**Practical applications:** Cross-currency rates, spread options, quanto products, relative-value trades.

---

## Key Formulas Quick Reference

| # | Formula | Description |
|---|---------|-------------|
| (1) | $dX = \mu \, dt + \sigma \, dB$ | Itô process definition |
| (2) | $\text{Var}(X(T)) = E[\int_0^T \sigma^2 \, dt]$ | Variance of martingale Itô process |
| (3) | Quadratic variation $= \int_0^T \sigma^2 \, dt$ | QV of Itô process |
| (4a-d) | $(dt)^2 = 0$, $dt \cdot dB = 0$, $(dB)^2 = dt$, $dB_x dB_y = \rho \, dt$ | **Itô calculus rules** |
| (8) | Itô's formula for $g(t, X, Y)$ | General Itô's formula |
| (9) | $\frac{dZ}{Z} = \frac{dX}{X} + \frac{dY}{Y} + \frac{dX dY}{XY}$ | Product rule ($Z=XY$) |
| (10) | $\frac{dZ}{Z} = \frac{dY}{Y} - \frac{dX}{X} - \frac{dX dY}{XY} + (\frac{dX}{X})^2$ | Ratio rule ($Z=Y/X$) |
| (11) | $\frac{dZ}{Z} = dX + \frac{1}{2}(dX)^2$ | Exponential ($Z=e^X$) |
| (12) | $dZ = \frac{dX}{X} - \frac{1}{2}(\frac{dX}{X})^2$ | Logarithm ($Z=\log X$) |
| (14) | $\frac{dV}{V} = q \, dt + \frac{dS}{S}$ | Equivalent non-dividend stock |
| (15) | $S = S_0 \exp((\mu - \sigma^2/2)t + \sigma B)$ | GBM solution |
| (16) | $\frac{dS}{S} = \mu \, dt + \sigma \, dB$ | GBM SDE |
| (18) | $d(\log S) = (\mu - \sigma^2/2) dt + \sigma \, dB$ | GBM log form |
| (21) | $\frac{dS}{S} = (r-q) \, dt + \sigma_s \, dB^R$ | $S$ under risk-neutral measure |
| (22) | $\frac{dS}{S} = (r-q+\sigma_s^2) \, dt + \sigma_s \, dB^V$ | $S$ under V-measure |
| (23) | $\frac{dS}{S} = (r-q+\rho\sigma_s\sigma_y) \, dt + \sigma_s \, dB^Y$ | $S$ under Y-measure |
| (26-27) | $\text{prob}^{\text{num}}(S(T) \geq K) = N(d)$ | GBM tail probability |
| (29) | $\sigma_{XY} = \sqrt{\sigma_x^2 + \sigma_y^2 + 2\rho\sigma_x\sigma_y}$ | Volatility of product |
| (31) | $\sigma_{Y/X} = \sqrt{\sigma_x^2 + \sigma_y^2 - 2\rho\sigma_x\sigma_y}$ | Volatility of ratio |

---

## Key Concepts Review

### What is $dX$?

$dX$ is an **infinitesimal change** in $X$ — a "differential form." It has meaning on its own, NOT only when divided by $dt$.

**Why we use $dX$ instead of $dX/dt$ in Itô calculus:** Because $dB/dt$ doesn't exist (Brownian motion is nowhere differentiable). So we must use differential form.

**Practical rules:** Treat $dt$, $dB$ as algebraic symbols you can multiply, square, and rearrange — just remember the Itô rules:
- $(dt)^2 = 0$
- $dt \cdot dB = 0$
- $(dB)^2 = dt$
- $dB_x \cdot dB_y = \rho \, dt$

### Itô's Formula = Taylor Expansion

Itô's formula is fundamentally a **2nd-order Taylor expansion**, not the chain rule. The "extra term" $\frac{1}{2} g'' \, dt$ comes from the second-order Taylor term that survives because $(\Delta B)^2 \to dt$ instead of vanishing.

**When does Itô formula reduce to ordinary calculus?**
- When the function is **linear** in the Itô process (e.g., $V = e^{qt}S$)
- Or when one variable is a **smooth/deterministic function** (no $dB$)

### Martingale Condition

For ANY form (whether $dX = \cdots$ or $\frac{dX}{X} = \cdots$):
> **$X$ is a martingale ⟺ the total $dt$ coefficient in $dX$ equals 0**

⚠️ **Warning:** If $X$ is a martingale, **$f(X)$ is generally NOT a martingale** (unless $f$ is affine). For GBM, $S$ being a martingale and $\log S$ being a martingale are mutually exclusive!

### Change of Measure Effects

| What changes | What doesn't change |
|--------------|---------------------|
| Drift | Diffusion (volatility) |
| Brownian motion (becomes new BM under new measure) | Instantaneous covariance $dX \, dY$ |

**For Black-Scholes:** Different numeraires give different probability measures, but the volatility $\sigma_s$ is the same in all of them!

---

## FAQ

### Q1: Why do we need Itô's formula instead of the chain rule?

Because Brownian motion has nonzero quadratic variation. The second-order Taylor term doesn't vanish, so it must be included. Ordinary chain rule misses this critical term.

### Q2: How do I know whether to include the second-order term?

Check if the variable is "rough" (has nonzero quadratic variation). If yes (Brownian motion, Itô process) → include 2nd-order. If no (smooth/deterministic) → skip it.

**Shortcut:** Compute partial derivatives. If $\partial^2 g/\partial x^2 = 0$ for the Itô process variable, no 2nd-order term needed.

### Q3: Why does the drift change under different numeraires but not the volatility?

The Radon-Nikodym derivative (which converts measures) only "tilts" probabilities — it shifts where the mass concentrates but doesn't change the magnitude of randomness. Mathematically, this corresponds to **Girsanov's theorem**: changing measures only adds a deterministic drift to a Brownian motion.

### Q4: What's the practical use of these volatility formulas?

- **Cross-currency derivatives:** EUR/JPY = USD-JPY / USD-EUR
- **Spread options:** Volatility of $S_1 - S_2$
- **Quanto options:** FX-equity correlations
- **Implied volatility surfaces:** Connecting different products

### Q5: Why is $V = e^{qt} S$ called the "equivalent non-dividend stock"?

Because $V(0) = E[\phi(T) V(T)]$ holds (it satisfies equation (7) from Chapter 1), just like a non-dividend stock would. The dividend reinvestment trick converts the problem into a familiar non-dividend setting where all of Chapter 1's results apply.

---

## Chapter Summary

This chapter develops the **mathematical machinery** of continuous-time finance:

1. **Brownian motion** is the foundation — the unique continuous martingale (up to time-rescaling) by Lévy's theorem.

2. **Itô processes** generalize Brownian motion: $dX = \mu \, dt + \sigma \, dB$.

3. **Itô's formula** tells us how functions of Itô processes evolve, with a crucial **second-order correction** $\frac{1}{2} g'' \sigma^2 \, dt$.

4. **Geometric Brownian motion** is the standard model for stock prices: $dS/S = \mu \, dt + \sigma \, dB$.

5. **Different numeraires** correspond to different probability measures and different drifts for $S$, but the volatility is invariant.

6. **Tail probabilities** of GBM lead to formulas of the form $N(d)$ — the building blocks of Black-Scholes.

7. **Volatility formulas for products and ratios** mirror the variance formulas for sums and differences (with $\pm 2\rho \sigma_x \sigma_y$).

**These tools are the foundation for Black-Scholes (Chapter 3) and all subsequent derivative pricing.**
