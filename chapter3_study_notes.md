# Chapter 3: Black-Scholes — Study Notes

**FIN 4370 & 5370 — Advanced Derivative Securities**
**Professor Jian Cai / Spring 2026**

---

## Table of Contents

1. [Black-Scholes Assumptions](#1-black-scholes-assumptions)
2. [Pricing Share Digitals and Digitals](#2-pricing-share-digitals-and-digitals)
3. [Tail Probabilities of GBM (Review)](#3-tail-probabilities-of-gbm-review)
4. [Pricing Standard Calls and Puts](#4-pricing-standard-calls-and-puts)
5. [Greeks](#5-greeks)
6. [Delta and Gamma Hedging](#6-delta-and-gamma-hedging)
7. [Implied Volatilities](#7-implied-volatilities)
8. [Time-Varying Volatility](#8-time-varying-volatility)
9. [Term Structure of Volatility](#9-term-structure-of-volatility)
10. [Smiles and Smirks](#10-smiles-and-smirks)
11. [Calculations in VBA](#11-calculations-in-vba)

---

## 1. Black-Scholes Assumptions

### 1.1 The Setup

Black-Scholes assumes:
- **Constant, continuously compounded risk-free rate** $r$
- **Constant dividend yield** $q$
- **Stock price** $S$ follows a geometric Brownian motion (GBM):

$$\boxed{\frac{dS}{S} = \mu \, dt + \sigma \, dB \quad (1)}$$

where $B$ is a standard Brownian motion.

### 1.2 Key Assumptions

- $\sigma$ is **constant** (or, later, allowed to vary in a non-random way)
- $\mu$ can be a **quite general random process** — it doesn't matter for pricing!
- No arbitrage, continuous trading

> **Key insight from Chapter 2:** Under the risk-neutral measure $R$, $\mu$ is replaced by $r - q$. The drift never appears in option prices — only $\sigma$ matters.

### 1.3 Why These Assumptions Matter

| Assumption | Implication |
|-----------|-------------|
| Constant $\sigma$ | Unique implied volatility for all options |
| Log-normal prices | Tractable formulas via normal CDF |
| No jumps | Replication is possible with continuous trading |
| Constant $r$ | Clean discounting with $e^{-rT}$ |

---

## 2. Pricing Share Digitals and Digitals

### 2.1 Share Digital Options

A **share digital call** pays $S(T)$ at $T$ if $S(T) \geq K$, else $0$.

Using the stock (growth-adjusted) $V(t) = e^{qt}S(t)$ as numeraire:

$$\boxed{SDC(0) = e^{-qT} S(0) \cdot N(d_1)}$$

A **share digital put** pays $S(T)$ if $S(T) \leq K$, else $0$:

$$\boxed{SDP(0) = e^{-qT} S(0) \cdot N(-d_1)}$$

### 2.2 Cash Digital Options

A **digital call** pays $\$1$ at $T$ if $S(T) \geq K$, else $0$.

Using the risk-free asset $R(t) = e^{rt}$ as numeraire:

$$\boxed{DC(0) = e^{-rT} N(d_2)}$$

A **digital put** pays $\$1$ if $S(T) \leq K$, else $0$:

$$\boxed{DP(0) = e^{-rT} N(-d_2)}$$

### 2.3 The $d_1$ and $d_2$ Formulas

$$\boxed{d_1 = \frac{\log(S(0)/K) + (r - q + \frac{1}{2}\sigma^2) T}{\sigma \sqrt{T}} \quad (4)}$$

$$\boxed{d_2 = \frac{\log(S(0)/K) + (r - q - \frac{1}{2}\sigma^2) T}{\sigma \sqrt{T}} = d_1 - \sigma\sqrt{T} \quad (5)}$$

**Memory trick:**
- $d_1$ → share digital → uses the **stock** as numeraire → drift includes $+\frac{1}{2}\sigma^2$
- $d_2$ → cash digital → uses the **bond** as numeraire → drift includes $-\frac{1}{2}\sigma^2$
- They always differ by exactly $\sigma\sqrt{T}$

---

## 3. Tail Probabilities of GBM (Review)

Under any numeraire, we have:

$$\text{prob}^{\text{num}}(S(T) \geq K) = N(d)$$

where

$$d = \frac{\log(S(0)/K) + \alpha^{\text{num}} T}{\sigma \sqrt{T}}$$

and the numeraire-specific drifts are:
- $\alpha^R = r - q - \sigma^2/2$ → gives $d_2$
- $\alpha^V = r - q + \sigma^2/2$ → gives $d_1$

**Connection to Chapter 2:** This is exactly the tail probability result from Chapter 2, Section 9. Black-Scholes is just the application of those probabilities to pricing!

---

## 4. Pricing Standard Calls and Puts

### 4.1 Call Price: Building from Digitals

A standard call has payoff $(S(T) - K)^+ = (S(T) - K) \cdot \mathbf{1}_{S(T) \geq K}$.

Decompose: **long 1 share digital call, short $K$ cash digital calls**

$$\boxed{c(0) = e^{-qT} S(0) N(d_1) - e^{-rT} K N(d_2) \quad (6)}$$

| Component | Meaning |
|-----------|---------|
| $e^{-qT} S(0) N(d_1)$ | Expected discounted value of receiving the stock |
| $e^{-rT} K N(d_2)$ | Expected discounted cost of paying the strike |
| $N(d_2)$ | Risk-neutral probability the call expires in-the-money |
| $N(d_1)$ | Share-measure probability (slightly higher due to the $\sigma^2/2$ adjustment) |

### 4.2 Put Price

A standard put has payoff $(K - S(T))^+$. Decompose: **long $K$ cash digital puts, short 1 share digital put**

$$\boxed{p(0) = e^{-rT} K N(-d_2) - e^{-qT} S(0) N(-d_1) \quad (7)}$$

### 4.3 Put-Call Parity

From the formulas, you can verify:

$$\boxed{c - p = e^{-qT} S(0) - e^{-rT} K}$$

> **Exam tip:** Always double-check your call/put prices against put-call parity.

---

## 5. Greeks

### 5.1 What Are Greeks?

**Greeks** are partial derivatives of the option price with respect to each input parameter.

| Input | Symbol | Greek | Symbol | Definition |
|-------|--------|-------|--------|------------|
| Stock price | $S$ | Delta | $\delta$ | $\partial \Pi / \partial S$ |
| Delta | $\delta$ | Gamma | $\Gamma$ | $\partial^2 \Pi / \partial S^2$ |
| -(Time to maturity) | $-T$ | Theta | $\Theta$ | $-\partial \Pi / \partial T$ |
| Volatility | $\sigma$ | Vega | $\upsilon$ | $\partial \Pi / \partial \sigma$ |
| Interest rate | $r$ | Rho | $\rho$ | $\partial \Pi / \partial r$ |

### 5.2 Calculation Preliminaries

**Normal density function:**
$$n(d) = \frac{1}{\sqrt{2\pi}} e^{-d^2/2} = N'(d)$$

**Crucial identity** (saves a lot of algebra!):
$$\boxed{e^{-qT} S \cdot n(d_1) = e^{-rT} K \cdot n(d_2) \quad (8)}$$

*Why?* Because $d_1 - d_2 = \sigma\sqrt{T}$, so the exponential terms adjust to make both sides equal.

**Put Greeks from Call Greeks** (via put-call parity):

$$p = c + e^{-rT} K - e^{-qT} S$$

- $\delta_p = \delta_c - e^{-qT}$
- $\Gamma_p = \Gamma_c$ (same!)
- $\upsilon_p = \upsilon_c$ (same!)

### 5.3 Greeks for a Call

$$\boxed{\delta_c = e^{-qT} N(d_1)}$$

$$\boxed{\Gamma_c = \frac{e^{-qT} n(d_1)}{S \sigma \sqrt{T}}}$$

$$\boxed{\Theta_c = -e^{-qT} S \cdot n(d_1) \frac{\sigma}{2\sqrt{T}} + q e^{-qT} S N(d_1) - r e^{-rT} K N(d_2)}$$

$$\boxed{\upsilon_c = e^{-qT} S \cdot n(d_1) \sqrt{T}}$$

$$\boxed{\rho_c = T e^{-rT} K N(d_2)}$$

### 5.4 Intuition for the Greeks

**Delta** ($0 \leq \delta_c \leq e^{-qT}$):
- Deep in-the-money call: $\delta \to e^{-qT}$ (moves like the stock)
- At-the-money: $\delta \approx 0.5$
- Deep out-of-the-money: $\delta \to 0$

**Gamma** (always positive for both calls and puts):
- Largest when at-the-money and near expiry
- Gamma is the "curvature" of the delta hedge — large gamma means the hedge needs frequent rebalancing

**Vega** (always positive):
- Both calls and puts gain value when volatility rises
- Largest when at-the-money

**Theta** (usually negative for long options):
- Time decay — options lose value as expiry approaches
- Exception: deep in-the-money puts can have positive theta

---

## 6. Delta and Gamma Hedging

### 6.1 Delta Hedging

Since $c = e^{-qT} S N(d_1) - e^{-rT} K N(d_2)$, one can **replicate** the call by:
- Buying $\delta_c = e^{-qT} N(d_1)$ shares
- Borrowing $e^{-rT} K N(d_2)$ in cash

**To hedge a long call** (eliminate stock price risk): short sell $\delta_c$ shares.

This is a **perfect hedge only if rebalanced continuously**. In practice, rebalancing is discrete, which introduces hedging error.

**Put hedge:** Buying $e^{-qT} N(-d_1)$ shares perfectly hedges a long put.

### 6.2 Discretely-Rebalanced Delta Hedge

With rebalancing at dates $0 = t_0 < t_1 < \cdots < t_N = T$, with $\Delta t = T/N$:

**Stock price simulation:**
$$\Delta \log S = \left(\mu - q - \frac{1}{2}\sigma^2\right) \Delta t + \sigma \sqrt{\Delta t} \cdot z$$

where $z \sim N(0,1)$.

**Hedge portfolio setup** (short call, long $\delta$ shares, cash position):
- Cash position at each step is updated for: interest earned ($e^{r\Delta t}$), dividends received ($\delta S (e^{q \Delta t} - 1)$), cost of rebalancing ($({\delta'} - \delta) S'$)
- At maturity: $\text{Profit} = \text{Cash}_T - (S_T - K)^+$

> **Key observation:** As $N \to \infty$ (more frequent rebalancing), the distribution of profits becomes tighter around zero — the hedge approaches perfection.

### 6.3 Gamma Hedging

**Problem with delta hedging:** Delta changes as $S$ moves (measured by Gamma). Discrete rebalancing leaves residual risk proportional to Gamma.

**Solution:** Add a second option to make the portfolio **both delta and gamma neutral**.

Let $\delta, \Gamma$ = delta, gamma of the written option; $\delta', \Gamma'$ = delta, gamma of the hedging option (Option 2).

Hold $a$ shares of stock and $b$ units of Option 2:

$$\boxed{b = \frac{\Gamma}{\Gamma'}, \quad a = \delta - \frac{\Gamma}{\Gamma'} \delta'}$$

**Why can't stock hedge gamma?** The stock has $\Gamma_S = 0$ (delta of stock is always 1, never changes with $S$). You need another option to introduce curvature.

---

## 7. Implied Volatilities

### 7.1 Definition

All Black-Scholes inputs are observable ($S$, $K$, $r$, $q$, $T$) **except** $\sigma$.

**Implied volatility**: the value of $\sigma$ that makes the Black-Scholes formula equal the observed market price.

$$\hat{\sigma} = \sigma : \text{BS}(S, K, r, \hat{\sigma}, q, T) = \text{Market Price}$$

### 7.2 Why It's Useful

Even if B-S is not exactly right:
- Makes options with different strikes and maturities **comparable** on a common scale
- A high implied vol → option is "expensive"; low → "cheap"
- **Paradox:** It's harder to judge from the price alone, because you must account for moneyness and time to maturity

### 7.3 Arbitrage Bounds

For an implied vol to exist, the call price must satisfy:

$$e^{-qT} S \geq C \geq e^{-qT} S - e^{-rT} K$$

(i.e., the price must be between the stock value and its intrinsic value). If this fails, there's an arbitrage.

### 7.4 Bisection Method (Algorithm)

To solve for $\hat{\sigma}$ numerically, use **bisection**:

1. Set `lower = 0`, `upper = 1` (or keep doubling `upper` until BS(upper) > market price)
2. Repeatedly bisect the interval: `guess = (lower + upper) / 2`
3. Stop when `upper - lower < tolerance` (e.g., $10^{-6}$)

**Key property:** Because the call price is strictly increasing in $\sigma$, bisection is guaranteed to converge.

> **For puts:** Convert to a call price using put-call parity, then apply the same algorithm.

---

## 8. Time-Varying Volatility

### 8.1 Key Result

The Black-Scholes formula remains valid even when $\sigma$ is **a non-random function of time** $\sigma(t)$, i.e.:

$$\frac{dS(t)}{S(t)} = \mu(t) \, dt + \sigma(t) \, dB(t)$$

The variance of $\log S(T)$ is:

$$\int_0^T \sigma^2(t) \, dt \quad (7)$$

### 8.2 Average Volatility

Simply replace constant $\sigma$ with the **average volatility** $\sigma_{\text{avg}}$ in the $d_1, d_2$ formulas:

$$\boxed{\sigma_{\text{avg}}^2 = \frac{1}{T} \int_0^T \sigma^2(t) \, dt \quad (8)}$$

**Intuition:** Volatility compounds — what matters is total accumulated variance $\sigma^2 T$, not the path of $\sigma(t)$.

---

## 9. Term Structure of Volatility

### 9.1 The Problem

Options with **different maturities** typically have **different implied volatilities**:
- $T_1$: implied vol = $\hat{\sigma}_1$
- $T_2 > T_1$: implied vol = $\hat{\sigma}_2$

We want to find a function $\sigma(t)$ consistent with both observations.

### 9.2 Interpretation

Each implied vol is the **average** of $\sigma(t)$ over its horizon:

$$\hat{\sigma}_1^2 = \frac{1}{T_1} \int_0^{T_1} \sigma^2(t) \, dt \qquad \hat{\sigma}_2^2 = \frac{1}{T_2} \int_0^{T_2} \sigma^2(t) \, dt$$

### 9.3 Constructing $\sigma(t)$

$$\boxed{\sigma(t) = \begin{cases} \hat{\sigma}_1 & \text{for } t \leq T_1 \\ \sqrt{\dfrac{\hat{\sigma}_2^2 T_2 - \hat{\sigma}_1^2 T_1}{T_2 - T_1}} & \text{for } T_1 < t \leq T_2 \end{cases}}$$

**How to derive the second piece:** Total variance over $[0, T_2]$ must equal total variance over $[0, T_1]$ plus total variance over $[T_1, T_2]$:

$$\hat{\sigma}_2^2 T_2 = \hat{\sigma}_1^2 T_1 + \sigma^2(t) \cdot (T_2 - T_1)$$

Solving: $\sigma(t) = \sqrt{\dfrac{\hat{\sigma}_2^2 T_2 - \hat{\sigma}_1^2 T_1}{T_2 - T_1}}$ for $T_1 < t \leq T_2$.

### 9.4 General Case (Multiple Maturities)

Given maturities $T_1 < T_2 < \cdots < T_N$ with implied vols $\hat{\sigma}_1, \ldots, \hat{\sigma}_N$:

$$\boxed{\sigma(t) = \sqrt{\frac{\hat{\sigma}_{i+1}^2 T_{i+1} - \hat{\sigma}_i^2 T_i}{T_{i+1} - T_i}}} \quad \text{for } T_i < t \leq T_{i+1}$$

**Validity condition:** The expression inside the square root must be **positive**. If not, there is an arbitrage opportunity between the two options.

> **The GPA analogy:** If your 2-year cumulative GPA is 3.5 and your 1-year GPA was 3.2, you can back out exactly what your 2nd year GPA must have been. The same logic applies here with variance instead of grade points.

---

## 10. Smiles and Smirks

### 10.1 The Observation

For options with the **same maturity but different strikes**, implied volatilities are not equal — they form a **smile** or **smirk** pattern.

**For equities and equity indices:**
- Implied vol **decreases** as strike increases (for low strikes)
- Then **flattens out or rises slightly** at high strikes
- The resulting shape looks like a **twisted smile** → called a **smirk**

### 10.2 What It Implies

The smile/smirk pattern is **inconsistent with Black-Scholes**. It reveals:

| Observation | What it means |
|-------------|--------------|
| Implied vol decreases with strike | More probability mass in left tail (large negative returns) |
| Higher vol for low strikes | Market fears crashes ("crash risk premium") |
| Implied vol ≠ constant across strikes | True return distribution is NOT lognormal |

**Fat tails:** The market-implied distribution has more extreme outcomes than Black-Scholes predicts (both positive and negative).

**Skew:** The left tail is fatter than the right — extreme drops are more likely than extreme rallies.

### 10.3 Intuition: Why Does the Smirk Exist?

1. **Crash fear:** Out-of-the-money puts provide insurance against market crashes → high demand → high prices → high implied vol
2. **Leverage effect:** As stock prices fall, equity becomes riskier (firm becomes more levered) → volatility rises with bad news
3. **Risk premium:** Investors pay extra to hedge downside risk

> **Bottom line:** The volatility smile is the market telling us that lognormal returns are a simplification. Real return distributions have fat, skewed tails.

---

## 11. Calculations in VBA

### 11.1 Key VBA Functions

| Mathematical Function | VBA Equivalent |
|----------------------|----------------|
| $N(d)$ — normal CDF | `Application.NormSDist(d)` |
| $\ln(x)$ | `Log(x)` |
| $\sqrt{x}$ | `Sqr(x)` |
| $e^x$ | `Exp(x)` |
| $\pi$ | `Application.Pi` |
| $\max(a, b)$ | `Application.Max(a, b)` |

### 11.2 Black-Scholes Call Formula (VBA)

```vba
Function Black_Scholes_Call(S, K, r, sigma, q, T)
    Dim d1, d2, N1, N2
    If sigma = 0 Then
        Black_Scholes_Call = Application.Max(0, Exp(-q*T)*S - Exp(-r*T)*K)
    Else
        d1 = (Log(S/K) + (r - q + 0.5*sigma*sigma)*T) / (sigma*Sqr(T))
        d2 = d1 - sigma*Sqr(T)
        N1 = Application.NormSDist(d1)
        N2 = Application.NormSDist(d2)
        Black_Scholes_Call = Exp(-q*T)*S*N1 - Exp(-r*T)*K*N2
    End If
End Function
```

### 11.3 Black-Scholes Put Formula (VBA)

```vba
Function Black_Scholes_Put(S, K, r, sigma, q, T)
    Dim d1, d2, N1, N2
    If sigma = 0 Then
        Black_Scholes_Put = Application.Max(0, Exp(-r*T)*K - Exp(-q*T)*S)
    Else
        d1 = (Log(S/K) + (r - q + 0.5*sigma*sigma)*T) / (sigma*Sqr(T))
        d2 = d1 - sigma*Sqr(T)
        N1 = Application.NormSDist(-d1)
        N2 = Application.NormSDist(-d2)
        Black_Scholes_Put = Exp(-r*T)*K*N2 - Exp(-q*T)*S*N1
    End If
End Function
```

### 11.4 Implied Volatility via Bisection (VBA)

```vba
Function Black_Scholes_Call_Implied_Vol(S, K, r, q, T, CallPrice)
    Dim tol, lower, upper, guess, fguess, fupper
    If CallPrice < Exp(-q*T)*S - Exp(-r*T)*K Then
        MsgBox("Option price violates arbitrage bound.")
        Exit Function
    End If
    tol = 10^-6
    lower = 0
    upper = 1
    fupper = Black_Scholes_Call(S,K,r,upper,q,T) - CallPrice
    Do While fupper < 0
        upper = 2*upper
        fupper = Black_Scholes_Call(S,K,r,upper,q,T) - CallPrice
    Loop
    guess = 0.5*lower + 0.5*upper
    fguess = Black_Scholes_Call(S,K,r,guess,q,T) - CallPrice
    Do While upper - lower > tol
        If fupper*fguess < 0 Then
            lower = guess
        Else
            upper = guess
            fupper = fguess
        End If
        guess = 0.5*lower + 0.5*upper
        fguess = Black_Scholes_Call(S,K,r,guess,q,T) - CallPrice
    Loop
    Black_Scholes_Call_Implied_Vol = guess
End Function
```

---

## Key Formulas Quick Reference

| Formula | Expression | Description |
|---------|-----------|-------------|
| GBM | $dS/S = \mu \, dt + \sigma \, dB$ | Stock price process |
| $d_1$ | $\dfrac{\log(S_0/K) + (r-q+\frac{1}{2}\sigma^2)T}{\sigma\sqrt{T}}$ | Share-digital parameter |
| $d_2$ | $d_1 - \sigma\sqrt{T}$ | Cash-digital parameter |
| Call | $e^{-qT} S N(d_1) - e^{-rT} K N(d_2)$ | Black-Scholes call price |
| Put | $e^{-rT} K N(-d_2) - e^{-qT} S N(-d_1)$ | Black-Scholes put price |
| Call Delta | $e^{-qT} N(d_1)$ | $\partial c / \partial S$ |
| Put Delta | $e^{-qT} (N(d_1) - 1) = -e^{-qT} N(-d_1)$ | $\partial p / \partial S$ |
| Gamma | $e^{-qT} n(d_1) / (S \sigma \sqrt{T})$ | Same for call and put |
| Vega | $e^{-qT} S \cdot n(d_1) \sqrt{T}$ | Same for call and put |
| Identity | $e^{-qT} S \cdot n(d_1) = e^{-rT} K \cdot n(d_2)$ | Useful for Greeks |
| Gamma hedge | $b = \Gamma / \Gamma'$, $a = \delta - b\delta'$ | Units of option 2 and stock |
| Avg. vol | $\sigma_{\text{avg}}^2 = \frac{1}{T}\int_0^T \sigma^2(t) \, dt$ | Time-varying vol replacement |
| Fwd. vol | $\sigma(t) = \sqrt{(\hat{\sigma}_2^2 T_2 - \hat{\sigma}_1^2 T_1)/(T_2-T_1)}$ | Between $T_1$ and $T_2$ |

---

## Key Concepts Review

### What is $N(d_1)$ vs $N(d_2)$?

Both are probabilities, but under **different measures**:
- $N(d_2)$ = risk-neutral probability that the call expires in the money $\Rightarrow$ prob of paying $K$
- $N(d_1)$ = stock-measure probability that the call expires in the money $\Rightarrow$ prob-weighted share receipt

Since the stock drift under the stock measure is higher by $\sigma^2$, we always have $d_1 > d_2$ and $N(d_1) > N(d_2)$ (for a call in the money).

### Delta Hedging: Perfect in Theory, Imperfect in Practice

Delta hedging is **only perfect with continuous rebalancing**. In practice:
- More rebalancing → smaller hedging error → higher transaction costs
- Gamma measures how quickly the delta changes → high gamma = expensive to hedge
- Gamma hedging (with a second option) reduces this error

### The Volatility Smile: The Market's Verdict on Black-Scholes

The smile pattern is the most direct evidence that the Black-Scholes model is wrong:
- B-S predicts a **flat** implied vol across strikes (constant $\sigma$)
- The market shows a **skewed smile (smirk)** for equities
- This means: **actual return distributions have fat, left-skewed tails** — crashes are more likely than B-S assumes

---

## FAQ

### Q1: Why does $\mu$ not appear in the Black-Scholes formula?

Because we price by replication, not expectation. The replicating portfolio eliminates all dependence on $\mu$ through delta hedging. Alternatively, under the risk-neutral measure, $\mu$ is replaced by $r - q$ for everyone — regardless of their subjective beliefs.

### Q2: What's the difference between historical vol and implied vol?

**Historical (realized) vol:** Computed from past price data — backward-looking.
**Implied vol:** Backed out from current option prices — forward-looking market expectation.
They often differ because: (1) risk premiums, (2) fear of crashes, (3) supply/demand in options market.

### Q3: Why do calls and puts have the same gamma and vega?

From put-call parity: $p = c + e^{-rT}K - e^{-qT}S$. The second and third terms don't depend on $\sigma$ or $S^2$, so their derivatives with respect to $\sigma$ and $\partial^2/\partial S^2$ are zero. Thus $\Gamma_p = \Gamma_c$ and $\upsilon_p = \upsilon_c$.

### Q4: When does the term structure of volatility arbitrage condition fail?

If $\hat{\sigma}_2^2 T_2 < \hat{\sigma}_1^2 T_1$ for $T_2 > T_1$, then the forward variance would be negative — impossible. This means you can construct a static arbitrage: the longer-dated option is too cheap relative to the shorter-dated one.

### Q5: Why does the smirk appear for equities but not necessarily for other assets?

Equities have **asymmetric risk**: companies can go bankrupt (large left tail) but have unlimited upside. Additionally, the **leverage effect** means volatility tends to spike when prices fall. For currencies (FX), smiles are often more symmetric because both currencies can crash relative to the other.

---

## Chapter Summary

Chapter 3 applies the machinery of Chapter 2 to derive and use the Black-Scholes option pricing framework:

1. **Black-Scholes pricing** builds directly on the numeraire/probability framework of Chapter 2: call = share digital − $K$ × cash digital.

2. **$d_1$ and $d_2$** represent the probabilities of finishing in the money under two different measures (share and bond numeraires), differing by exactly $\sigma\sqrt{T}$.

3. **Greeks** quantify how option prices respond to each input. Delta and gamma are the most important for hedging; vega matters for implied vol trading.

4. **Delta hedging** replicates the option but requires continuous rebalancing. Gamma hedging using a second option improves performance under discrete rebalancing.

5. **Implied volatility** inverts the Black-Scholes formula to extract the market's volatility expectation — the standard language for comparing option prices.

6. **Time-varying volatility** is accommodated by replacing $\sigma$ with the average volatility $\sigma_{\text{avg}}$, as long as $\sigma(t)$ is non-random.

7. **Term structure of volatility** extracts a forward volatility function $\sigma(t)$ consistent with implied vols across different maturities — valid only when forward variances are positive.

8. **Smiles and smirks** reveal that real return distributions are fat-tailed and left-skewed — the market prices in crash risk that Black-Scholes ignores.

**The bottom line: Black-Scholes is a benchmark, not the truth. Its power lies in giving us a common language (implied vol) to describe, compare, and hedge options — even when its assumptions are violated.**
