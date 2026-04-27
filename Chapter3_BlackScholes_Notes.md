# Chapter 3: Black-Scholes — Study Notes (Enhanced)

**FIN 4370 & 5370 — Advanced Derivative Securities**  
**Professor Jian Cai / Spring 2026**

---

## Table of Contents

1. Black-Scholes Model Setup  
2. Pricing via Digitals  
3. Black-Scholes Formula  
4. Greeks  
5. Implied Volatility  
6. Term Structure of Volatility  
7. Smiles and Smirks  
8. Key Q&A and Intuition  
9. Summary & Big Picture  

---

# 1. Black-Scholes Model Setup

## Assumptions

\[
\frac{dS}{S} = \mu dt + \sigma dB
\]

- Constant risk-free rate \( r \)
- Constant dividend yield \( q \)
- Constant volatility \( \sigma \)
- No arbitrage

## ⚠️ Critical Assumption

\[
\sigma = \text{constant}
\]

---

# 2. Pricing via Digitals

## Digital Call

\[
\text{Payoff} = 1_{\{S(T) \ge K\}}
\]

Price:

\[
e^{-rT} \cdot P^R(S(T) \ge K)
\]

## Share Digital

\[
\text{Payoff} = S(T) \cdot 1_{\{S(T) \ge K\}}
\]

Price:

\[
e^{-qT} S_0 N(d_1)
\]

---

# 3. Black-Scholes Formula

\[
C = e^{-qT} S_0 N(d_1) - e^{-rT} K N(d_2)
\]

\[
d_1 = \frac{\ln(S_0/K) + (r - q + \frac{1}{2}\sigma^2)T}{\sigma \sqrt{T}}
\]

\[
d_2 = d_1 - \sigma \sqrt{T}
\]

---

# 4. Greeks

| Parameter | Greek | Meaning |
|----------|------|--------|
| S | Delta | sensitivity to price |
| S | Gamma | curvature |
| T | Theta | time decay |
| σ | Vega | vol sensitivity |
| r | Rho | rate sensitivity |

---

# 5. Implied Volatility

## Definition

Solve σ such that:

\[
C_{market} = C_{BS}(\sigma)
\]

## Interpretation

👉 Implied vol = market’s pricing of risk

---

# 6. Term Structure of Volatility

\[
\hat{\sigma}^2 = \frac{1}{T} \int_0^T \sigma^2(t) dt
\]

👉 Implied vol = time-average variance

---

# 7. Smiles and Smirks

## Observation

For same maturity, different strikes:

\[
\sigma = \sigma(K)
\]

## Shape

- Low strike → high vol  
- ATM → lower vol  
- High strike → slight increase  

👉 Smirk (equity)

---

## Interpretation

### 1. Fat Tails

\[
P(\text{extreme move}) \uparrow
\]

→ OTM options expensive

---

### 2. Skew

\[
P(\text{large negative}) > P(\text{large positive})
\]

→ crash risk priced in

---

## Conclusion

❌ Not consistent with lognormal assumption  
❌ Not consistent with Black-Scholes  

---

# 8. Key Q&A and Intuition

## Q1: Why does implied vol depend on strike?

Because market distribution ≠ lognormal.

---

## Q2: Why is left side higher?

Crash protection demand → puts expensive

---

## Q3: Why far OTM also high?

Fat tail → extreme events have probability

---

## Q4: What does implied vol represent?

👉 Price of risk, not actual volatility

---

# 9. Summary & Big Picture

## Black-Scholes works if:

- σ constant
- returns lognormal

---

## Reality:

- σ depends on strike → smile/smirk  
- fat tails  
- skew  

---

## Final Insight

\[
\text{Implied Volatility Surface} = \text{Market View of Risk}
\]

