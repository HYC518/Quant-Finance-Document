# Chapter 4: Estimating and Modeling Volatility — Study Notes

**FIN 4370 & 5370 — Advanced Derivative Securities**
**Professor Jian Cai / Spring 2026**

---

## Table of Contents

1. [Motivation](#1-motivation)
2. [Statistics Review](#2-statistics-review)
3. [Estimating Volatility and Mean from GBM](#3-estimating-volatility-and-mean-from-gbm)
4. [Reliability: Mean vs. Variance](#4-reliability-mean-vs-variance)
5. [GARCH Models](#5-garch-models)
6. [Stochastic Volatility Models (Heston)](#6-stochastic-volatility-models-heston)
7. [Simulating Correlated Brownian Motions](#7-simulating-correlated-brownian-motions)
8. [Smiles and Smirks Revisited](#8-smiles-and-smirks-revisited)
9. [Calculations in VBA](#9-calculations-in-vba)

---

## 1. Motivation

### 1.1 The Problem with Constant Volatility

In Chapters 2 and 3, we assumed volatility $\sigma$ was either constant or varied in a **non-random** way. But real-world data tells a different story:

- Volatility **clusters**: calm periods are followed by calm periods, turbulent periods by turbulent periods
- Volatility **spikes** during market crashes and stays elevated for extended periods
- The **smirk** pattern in implied vols (Chapter 3) cannot be explained by a constant $\sigma$

### 1.2 What This Chapter Does

Two distinct problems are addressed:

| Problem | Approach |
|---------|----------|
| **Estimating** $\sigma$ from historical price data | Statistics + GBM properties |
| **Modeling** $\sigma$ as a random process | GARCH and Stochastic Volatility |

> **Key insight:** Estimating $\sigma$ well is the prerequisite for modeling it well — you need to understand what you're trying to capture before building a model.

---

## 2. Statistics Review

### 2.1 Sample Mean and Variance

Given a random sample $x_1, \ldots, x_N$ from a population with mean $\mu$ and variance $\sigma^2$:

**Sample mean** (best estimate of $\mu$):
$$\boxed{\bar{x} = \frac{1}{N} \sum_{i=1}^N x_i}$$

**Unbiased sample variance** (best estimate of $\sigma^2$):
$$\boxed{s^2 = \frac{1}{N-1} \sum_{i=1}^N (x_i - \bar{x})^2 = \frac{1}{N-1}\left(\sum_{i=1}^N x_i^2 - N\bar{x}^2\right)}$$

> **Why $N-1$ and not $N$?** Because $\bar{x}$ is estimated from the same data. Using $N-1$ corrects for this bias — it's called **Bessel's correction**.

### 2.2 Standard Error of the Mean

The **standard error** of $\bar{x}$ — how much $\bar{x}$ varies across different samples — is:

$$\text{SE}(\bar{x}) = \frac{\sigma}{\sqrt{N}} \approx \frac{s}{\sqrt{N}}$$

**Key takeaway:** More data → smaller standard error → more reliable estimate of $\mu$.

### 2.3 Central Limit Theorem

Regardless of the actual distribution of $x$, $\bar{x}$ is approximately normally distributed for large $N$:

$$\bar{x} \approx N\!\left(\mu, \frac{\sigma^2}{N}\right)$$

This justifies using normal-distribution-based confidence intervals even when the raw data isn't normal.

---

## 3. Estimating Volatility and Mean from GBM

### 3.1 Setup

Stock price follows GBM:
$$\frac{dS}{S} = \mu \, dt + \sigma \, dB$$

Observed at dates $0 = t_0 < t_1 < \cdots < t_N = T$ with equal spacing $\Delta t$.

Define the **log return** over each interval:

$$\boxed{y_i \equiv r_i \Delta t = \log\frac{S(t_i)}{S(t_{i-1})} = \left(\mu - \frac{1}{2}\sigma^2\right)\Delta t + \sigma\left(B(t_i) - B(t_{i-1})\right) \quad (1)}$$

### 3.2 Distribution of $y_i$

Each $y_i$ is **independent** and normally distributed:

$$y_i \sim N\!\left(\left(\mu - \frac{\sigma^2}{2}\right)\Delta t,\; \sigma^2 \Delta t\right)$$

Note: the **variance** of $y_i$ is $\sigma^2 \Delta t$ (not $\sigma^2$), because it's measured over $\Delta t$.

### 3.3 Estimators

| Quantity | Estimator |
|----------|-----------|
| Mean of $y$ | $\bar{y} = \frac{1}{N}\sum y_i$ |
| Variance $\sigma^2$ | $\hat{\sigma}^2 = \frac{1}{N-1}\sum(y_i - \bar{y})^2$ |
| Drift $\mu$ | $\hat{\mu} = \frac{\bar{y}}{\Delta t} + \frac{1}{2}\hat{\sigma}^2 = \bar{r} + \frac{1}{2}\hat{\sigma}^2$ |

The $+\frac{1}{2}\hat{\sigma}^2$ correction comes from the Itô adjustment: the log return has mean $(\mu - \frac{1}{2}\sigma^2)\Delta t$, so we add back $\frac{1}{2}\sigma^2$ to recover $\mu$.

---

## 4. Reliability: Mean vs. Variance

### 4.1 Why the Mean Estimate is Unreliable

Notice that the sample mean of $r_i$ telescopes:

$$\bar{r} = \frac{\sum_{i=1}^N \log S(t_i) - \log S(t_{i-1})}{N \Delta t} = \frac{\log S(T) - \log S(0)}{T} \quad (2)$$

This depends **only on the first and last price observations**, regardless of how many intermediate prices you collect.

**Implication:** No matter how frequently you sample, the estimate of $\mu$ only gets better as the total time span $T$ grows — not as $\Delta t$ shrinks.

**Standard deviation of $\bar{r}$** in repeated samples:
$$\text{SD}(\bar{r}) = \frac{\sigma}{\sqrt{T}}$$

**Example:** With $\sigma = 0.30$, $T = 10$ years:
$$\text{SD}(\bar{r}) = \frac{0.30}{\sqrt{10}} \approx 9.5\%$$

A 95% confidence interval spans roughly $\pm 2 \times 9.5\% = \pm 19\%$, giving a band of **~38%** around the estimate. That's enormous.

> **Punchline:** We essentially can never estimate the expected return $\mu$ reliably from historical data. This is a fundamental limitation — we can only know where the stock has been, not where it's going.

### 4.2 Why the Variance Estimate is Reliable

For small $\Delta t$, $\bar{y} = \bar{r}\Delta t \to 0$, so the sample variance simplifies to:

$$\hat{\sigma}^2 \approx \frac{\sum_{i=1}^N y_i^2}{N-1} = \frac{N}{N-1} \cdot \frac{1}{T} \cdot \sum_{i=1}^N \left(\log\frac{S(t_i)}{S(t_{i-1})}\right)^2 \quad (3), (4)$$

As $\Delta t \to 0$ and $N \to \infty$, this converges to $\sigma^2$ — the **quadratic variation** result from Chapter 2!

**Implication:** You can estimate $\sigma^2$ to **any desired precision** by sampling more frequently within the same time window.

| Quantity | Gets better with more data over time? | Gets better with higher-frequency sampling? |
|----------|---------------------------------------|---------------------------------------------|
| $\mu$ | ✅ Yes | ❌ No |
| $\sigma^2$ | ✅ Yes | ✅ Yes |

> **Connection to Chapter 2:** This is exactly why quadratic variation equals $\int_0^T \sigma^2(t) \, dt$ — sampling the squared log-returns more finely recovers the integrated variance.

---

## 5. GARCH Models

### 5.1 Motivation

Simple historical estimation assumes $\sigma$ is **constant** — but in reality:
- Volatility changes over time
- High-volatility periods cluster together ("volatility clustering")
- Returns show "fat tails" compared to a normal distribution

GARCH captures this by making $\sigma_i^2$ **depend on past returns and past variances**.

### 5.2 The GARCH(1,1) Model

Redefine $y_i$ as the **standardized** log return:

$$y_i \equiv \frac{\log S(t_i) - \log S(t_{i-1}) - (r - q - \frac{1}{2}\sigma_i^2)\Delta t}{\sqrt{\Delta t}}$$

So $y_i \sim N(0, \sigma_i^2)$ conditionally.

The variance evolves as:

$$\boxed{\sigma_i^2 = \kappa\theta + (1-\kappa)(1-\lambda)y_{i-1}^2 + \lambda\sigma_{i-1}^2 \quad (6)}$$

where:
- $\kappa \in (0,1)$: **mean-reversion speed** (how fast vol reverts to $\theta$)
- $\theta > 0$: **long-run (unconditional) variance** — where $\sigma^2$ reverts to
- $\lambda \in (0,1)$: **persistence** of past variance (how long shocks last)

### 5.3 Reparametrization

Let $a = \kappa\theta$, $b = (1-\kappa)(1-\lambda)$, $c = (1-\kappa)\lambda$. Then:

$$\sigma_i^2 = a + b \cdot y_{i-1}^2 + c \cdot \sigma_{i-1}^2$$

| Parameter | Role |
|-----------|------|
| $a$ | Baseline variance floor |
| $b$ | Weight on last period's shock ($y^2$) |
| $c$ | Weight on last period's variance |
| $b + c$ | Total persistence — closer to 1 means shocks die slowly |

### 5.4 Key Features of GARCH(1,1)

**Volatility clustering:**
$$\text{Large } |y_{i-1}| \Rightarrow \text{large } y_{i-1}^2 \Rightarrow \text{large } \sigma_i^2 \Rightarrow \text{likely large } |y_i|$$

**Symmetric response:** Because $y_{i-1}^2$ is squared, GARCH treats positive and negative returns **identically**. A +20% return raises future vol just as much as a -20% return.

**Mean reversion:** $E[\sigma_{i+n}^2] \to \theta$ as $n \to \infty$ — volatility eventually reverts to its long-run level.

**Fat tails:** Because a large return spikes volatility, which then makes the next draw wider, the **unconditional** distribution of returns has heavier tails than a normal.

> **Important limitation:** GARCH cannot explain the **smirk** (asymmetric smile) because it treats up and down shocks equally. It produces a roughly **symmetric** smile pattern at best.

### 5.5 GARCH vs. Constant Volatility

| Feature | Constant $\sigma$ | GARCH(1,1) |
|---------|-------------------|------------|
| Volatility clustering | ❌ | ✅ |
| Fat tails | ❌ | ✅ |
| Asymmetric response to shocks | ❌ | ❌ |
| Explains vol smirk | ❌ | Partially |
| Easy to estimate | ✅ | Moderate |

---

## 6. Stochastic Volatility Models (Heston)

### 6.1 What's Different from GARCH?

In GARCH, volatility is random but **determined by past stock returns** — there's only one source of randomness ($B_s$).

In stochastic volatility models, volatility has its **own independent source of randomness** ($B_v$) — a second Brownian motion.

### 6.2 The Heston (1993) Model

**Stock price dynamics** (under risk-neutral measure):
$$\boxed{d\log S = \left(r - q - \frac{1}{2}v(t)\right)dt + \sqrt{v(t)}\, dB_s \quad (7a)}$$

**Variance dynamics:**
$$\boxed{dv(t) = \kappa(\theta - v(t))\, dt + \gamma\sqrt{v(t)}\, dB_v \quad (7b)}$$

where $\text{corr}(B_s, B_v) = \rho$, and $\kappa, \theta, \gamma > 0$.

| Parameter | Meaning |
|-----------|---------|
| $v(t) = \sigma(t)^2$ | Instantaneous variance |
| $\kappa$ | Mean-reversion speed of variance |
| $\theta$ | Long-run (unconditional) variance |
| $\gamma$ | Volatility of volatility ("vol of vol") |
| $\rho$ | Correlation between stock and vol shocks |

### 6.3 Key Properties

**Mean reversion:** $v(t)$ reverts to $\theta$ at speed $\kappa$ — same intuition as GARCH.

**Always non-negative:** As $v(t) \to 0$, the diffusion term $\gamma\sqrt{v} \to 0$ but drift $\kappa\theta > 0$ pushes it back up. (In simulation, we additionally take $\max(0, \cdot)$ to enforce this numerically.)

**Volatility of volatility:** $\gamma$ controls how wildly the variance itself fluctuates. High $\gamma$ → vol can spike dramatically.

### 6.4 The Role of Correlation $\rho$

This is the most important and most subtle parameter. In practice, $\rho < 0$ for equities.

**What $\rho < 0$ means mechanically:**

When $B_s$ moves negatively (stock crashes), $B_v$ tends to move positively (vol rises), and vice versa.

| Event | $B_s$ shock | $B_v$ tendency ($\rho < 0$) | Effect on $v(t)$ |
|-------|------------|------------------------------|------------------|
| Stock **crashes** | Large negative | Positive | Variance **spikes** |
| Stock **rallies** | Large positive | Negative | Variance **falls** |

**What happens next period after a crash (vol spikes):**

The next return is drawn from:
$$\text{Next return} \sim N\!\left(r - q - \frac{v}{2}, \; v\right)$$

The **center** of this distribution is always the drift $(r-q)$ — a small number like 5%. The **width** is determined by $v$. So:

- After a crash → $v$ is large → next return has a **wide distribution** → another extreme return (in either direction) is likely
- After a rally → $v$ is small → next return has a **narrow distribution** → next return is moderate, close to the drift

> **Critical point:** "Closer to $r$" means closer to the **drift** (≈5%), not closer to the previous period's extreme return. GBM has no momentum — each period starts fresh from the drift.

**Why $\rho < 0$ creates fat LEFT tails (the smirk):**

$$\text{Crash} \xrightarrow{\rho < 0} \text{Vol spikes} \rightarrow \text{Next return can be extremely negative again}$$
$$\text{Rally} \xrightarrow{\rho < 0} \text{Vol drops} \rightarrow \text{Next return is calm, moderate}$$

This **asymmetry** — crashes are self-reinforcing, rallies are self-dampening — is what produces:
- A **fat left tail** in the return distribution
- A **smirk** in implied volatilities (not a symmetric smile)

### 6.5 GARCH vs. Heston

| Feature | GARCH(1,1) | Heston |
|---------|------------|--------|
| Sources of randomness | 1 ($B_s$ only) | 2 ($B_s$ and $B_v$) |
| Vol driven by | Past returns | Its own Brownian motion |
| Response to crashes vs. rallies | **Symmetric** ($y^2$ term) | **Asymmetric** ($\rho < 0$) |
| Fat tails | ✅ Yes | ✅ Yes |
| Explains smirk (asymmetric smile) | ❌ Partial | ✅ Better |
| Tractable pricing formula | ❌ (need simulation) | ✅ (semi-closed form) |
| Parameters | $\kappa, \theta, \lambda$ | $\kappa, \theta, \gamma, \rho, v(0)$ |

---

## 7. Simulating Correlated Brownian Motions

### 7.1 The Problem

We need to simulate $\Delta B_s$ and $\Delta B_v$ jointly where $\text{corr}(\Delta B_s, \Delta B_v) = \rho$.

### 7.2 The Cholesky Decomposition Trick

Generate two **independent** standard normals $z_1, z_2 \sim N(0,1)$, then construct:

$$\boxed{z = z_1, \qquad z^* = \rho z_1 + \sqrt{1-\rho^2}\, z_2}$$

Then set:
$$\Delta B_s = \sqrt{\Delta t}\, z, \qquad \Delta B_v = \sqrt{\Delta t}\, z^*$$

**Verification:**
- $z^*$ is normal with mean 0 and variance $\rho^2 + (1-\rho^2) = 1$ ✓
- $\text{corr}(z, z^*) = \text{corr}(z_1, \rho z_1 + \sqrt{1-\rho^2}z_2) = \rho$ ✓

### 7.3 Discretized Heston Equations

$$\log S(t_{i+1}) = \log S(t_i) + \left(r - q - \frac{1}{2}v(t_i)\right)\Delta t + \sqrt{v(t_i)}\sqrt{\Delta t}\, z_1 \quad (8a)$$

$$v(t_{i+1}) = \max\!\left(0,\; v(t_i) + \kappa(\theta - v(t_i))\Delta t + \gamma\sqrt{v(t_i)}\sqrt{\Delta t}\, z^* \right) \quad (8b)$$

The $\max(0, \cdot)$ in equation (8b) ensures variance never goes negative due to discretization error.

---

## 8. Smiles and Smirks Revisited

### 8.1 Why Standard Models (GARCH, Heston) Fit Better

Let $\sigma_{\text{am}}$ = implied vol from an at-the-money option. The smirk says implied vol is **higher** for deep OTM and deep ITM options than $\sigma_{\text{am}}$.

**What this means for the return distribution:**

The market prices OTM puts (crash insurance) and OTM calls at a premium above what Black-Scholes with $\sigma_{\text{am}}$ would predict. This can only happen if the market assigns higher probability to extreme returns ($S(T) \ll S(0)$ or $S(T) \gg S(0)$) than the lognormal distribution implies.

In other words: **the market uses a fat-tailed, left-skewed distribution**.

### 8.2 How Each Model Addresses This

| Model | Fat tails? | Left skew? | Smirk? |
|-------|-----------|-----------|--------|
| Black-Scholes (constant $\sigma$) | ❌ | ❌ | ❌ |
| GARCH(1,1) | ✅ | ❌ (symmetric) | Partial |
| Heston ($\rho < 0$) | ✅ | ✅ | ✅ |

### 8.3 Calibration

Both GARCH and Heston parameters can be **implied from option prices** the same way implied vol is backed out from Black-Scholes:

- GARCH: imply $\sigma(0), \kappa, \theta, \lambda$ from a cross-section of option prices
- Heston: imply $\sigma(0), \kappa, \theta, \gamma, \rho$ from a cross-section of option prices

This is more complex than single-number implied vol, but gives a richer description of the vol surface.

---

## 9. Calculations in VBA

### 9.1 GARCH Simulation

```vba
Sub Simulating_GARCH()
    ' Parameters: S, sigma, r, q, dt, N, theta, kappa, lambda
    Dim a, b, c, y, i
    a = kappa * theta
    b = (1 - kappa) * (1 - lambda)
    c = (1 - kappa) * lambda

    For i = 1 To N
        y = sigma * RandN()                              ' standardized return
        LogS = LogS + (r - q - 0.5*sigma^2)*dt + Sqr(dt)*y
        S = Exp(LogS)
        sigma = Sqr(a + b*y^2 + c*sigma^2)              ' GARCH update
    Next i
End Sub
```

**Key GARCH update line:** `sigma = Sqr(a + b*y^2 + c*sigma^2)`
- `b*y^2`: last period's squared shock (symmetric — doesn't care about sign)
- `c*sigma^2`: persistence of last period's variance

### 9.2 Heston Simulation

```vba
Sub Simulating_Stochastic_Volatility()
    ' Parameters: S, sigma, r, q, dt, N, theta, kappa, Gamma, rho
    Dim z1, z2, Zstar, var
    Sqrrho = Sqr(1 - rho*rho)
    var = sigma*sigma

    For i = 1 To N
        z1 = RandN()
        z2 = RandN()
        Zstar = rho*z1 + Sqrrho*z2                      ' correlated normal

        LogS = LogS + (r - q - 0.5*sigma^2)*dt + sigma*Sqr(dt)*z1
        S = Exp(LogS)

        var = Application.Max(0, var + kappa*(theta - var)*dt _
              + Gamma*sigma*Sqr(dt)*Zstar)               ' Heston update
        sigma = Sqr(var)
    Next i
End Sub
```

**Key difference from GARCH:** The variance update uses `Zstar` (correlated with `z1` via $\rho$) — not `z1^2`. This is what introduces the **asymmetry**.

---

## Key Formulas Quick Reference

| Formula | Expression | Description |
|---------|-----------|-------------|
| Log return | $y_i = (\mu - \frac{\sigma^2}{2})\Delta t + \sigma\Delta B$ | (1) Discrete GBM return |
| Mean estimator | $\bar{r} = \frac{\log S(T) - \log S(0)}{T}$ | (2) Only depends on endpoints |
| Variance estimator | $\hat{\sigma}^2 \approx \frac{\sum y_i^2}{N-1}$ | (3),(4) Improves with frequency |
| SE of mean | $\sigma/\sqrt{T}$ | Unreliable — only shrinks with longer $T$ |
| GARCH(1,1) | $\sigma_i^2 = \kappa\theta + (1-\kappa)(1-\lambda)y_{i-1}^2 + \lambda\sigma_{i-1}^2$ | (6) Variance update |
| Heston stock | $d\log S = (r-q-v/2)dt + \sqrt{v}\,dB_s$ | (7a) Log price dynamics |
| Heston variance | $dv = \kappa(\theta-v)dt + \gamma\sqrt{v}\,dB_v$ | (7b) Variance dynamics |
| Correlated normals | $z^* = \rho z_1 + \sqrt{1-\rho^2}z_2$ | Simulate $\text{corr}=\rho$ |
| Heston discrete | $v_{i+1} = \max(0, v_i + \kappa(\theta-v_i)\Delta t + \gamma\sqrt{v_i}\Delta B_v)$ | (8b) Non-negative variance |

---

## Key Concepts Review

### Why Can't We Just Sample More to Estimate $\mu$?

Because $\bar{r} = \frac{\log S(T) - \log S(0)}{T}$ — it collapses to the ratio of two endpoint values. Adding more intermediate observations literally adds no information about this ratio. The only way to shrink the standard error $\sigma/\sqrt{T}$ is to observe the stock over a **longer total period** $T$.

This is a deep and somewhat depressing result: **expected returns are fundamentally hard to know**.

### GARCH Is Symmetric; Heston Can Be Asymmetric

GARCH uses $y_{i-1}^2$ — the squared return — which erases the sign. A rally and a crash of the same magnitude increase future volatility by exactly the same amount. Heston's $\rho < 0$ breaks this symmetry: crashes raise vol, rallies lower it.

### "Closer to $r$" — A Common Misconception

When vol drops after a rally in the Heston model, the **next return** is drawn more tightly around the **drift** $(r-q)$ — a small, boring number — NOT around the previous large return. GBM has no momentum: each period's return is independent of the last. Lower vol simply means a narrower distribution around the same drift center.

### The Fat Left Tail Mechanism in Heston

$$\underbrace{\text{Crash}}_{\Delta B_s \ll 0} \xrightarrow{\rho < 0} \underbrace{\text{Vol spikes}}_{\Delta B_v \gg 0} \rightarrow \underbrace{\text{Wide distribution next period}}_{\text{another extreme return likely}}$$

$$\underbrace{\text{Rally}}_{\Delta B_s \gg 0} \xrightarrow{\rho < 0} \underbrace{\text{Vol drops}}_{\Delta B_v \ll 0} \rightarrow \underbrace{\text{Narrow distribution next period}}_{\text{calm, moderate return}}$$

This asymmetry creates a fat **left** tail specifically — the right tail is actually thinner than GARCH would predict.

---

## FAQ

### Q1: If we can estimate $\sigma^2$ precisely by sampling frequently, why don't we just do that always?

In theory, yes — and in practice, high-frequency data (tick data) is used for exactly this purpose, giving very accurate **realized variance** estimates. The catch is microstructure noise (bid-ask spreads, price discreteness) that contaminates ultra-high-frequency data. In practice, 5-minute returns are often used as a sweet spot.

### Q2: What does "unconditional variance $\theta$" mean in GARCH?

It's the long-run average variance that $\sigma_i^2$ reverts to as $i \to \infty$, regardless of starting conditions. Mathematically, $E[\sigma_{i+n}^2] \to \theta$ as $n \to \infty$. Think of $\theta$ as "normal times" volatility and $\sigma_i^2$ as the current, possibly elevated, volatility.

### Q3: What does the Feller condition mean for Heston?

The condition $2\kappa\theta > \gamma^2$ (called the Feller condition) guarantees that $v(t)$ never actually hits zero in continuous time. If it's violated, variance can touch zero, making the $\sqrt{v}$ terms problematic. In discrete simulation we enforce non-negativity with the $\max(0, \cdot)$ trick regardless.

### Q4: Why does GARCH only partially explain the smirk?

GARCH produces fat tails (higher vol for extreme returns), which raises implied vols for both deep OTM puts AND deep OTM calls symmetrically — creating a **smile**, not a **smirk**. The smirk requires the left tail to be fatter than the right, which needs asymmetry. GARCH lacks this because it uses $y^2$ (symmetric). Heston with $\rho < 0$ has the asymmetry built in.

### Q5: How do we choose between GARCH and Heston in practice?

- **GARCH** is simpler to estimate (standard MLE on historical returns) and better for **risk management** (measuring historical vol clustering)
- **Heston** is better for **option pricing** because it can match the volatility smirk and has a semi-closed-form pricing formula
- In practice, many desks use GARCH for risk and Heston (or variants) for pricing

---

## Chapter Summary

Chapter 4 addresses two questions: how to **measure** volatility from data, and how to **model** it as a dynamic random process.

1. **Estimation:** Log returns from GBM are i.i.d. normal with variance $\sigma^2\Delta t$. The variance $\sigma^2$ can be estimated to any precision by sampling frequently (it's related to quadratic variation). The mean $\mu$ cannot — it only improves with longer time spans.

2. **GARCH(1,1):** Lets variance depend on past squared returns and past variance. Captures volatility clustering and fat tails symmetrically. The $y^2$ term means it can't distinguish crashes from rallies.

3. **Heston stochastic volatility:** Adds a separate Brownian motion for variance, correlated with the stock via $\rho$. When $\rho < 0$, crashes raise volatility (self-reinforcing) while rallies lower it (self-dampening). This asymmetry generates a fat left tail and explains the volatility smirk.

4. **Simulation:** GARCH updates $\sigma$ using $y^2$ (one random draw). Heston needs two correlated normals, constructed via $z^* = \rho z_1 + \sqrt{1-\rho^2}z_2$.

5. **Smiles and smirks:** The market's implied vol surface reflects fat, left-skewed tails. GARCH partially explains this; Heston (with $\rho < 0$) explains it better. Both are calibrated by matching model prices to observed option prices across strikes and maturities.

**The bridge to Chapter 5:** Pricing options under GARCH or Heston requires computing $\text{prob}^V(S(T) \geq K)$ and $\text{prob}^R(S(T) \geq K)$ — there are no simple closed-form solutions, so Monte Carlo simulation (Chapter 5) is needed.
