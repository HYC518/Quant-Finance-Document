# Chapter 3: Black-Scholes — Full Study Notes (Chapter 2 Level)

**FIN 4370 & 5370 — Advanced Derivative Securities**  
**Professor Jian Cai / Spring 2026**

---

## Table of Contents

1. Black-Scholes Assumptions  
2. Pricing via Digitals  
3. Derivation of Black-Scholes Formula  
4. Greeks  
5. Delta & Gamma Hedging  
6. Implied Volatility  
7. Term Structure of Volatility  
8. Smiles and Smirks  
9. Key Intuition & Connections  

---

# 1. Black-Scholes Assumptions

## 1.1 Stock Dynamics

\[
\frac{dS}{S} = \mu dt + \sigma dB
\]

Under risk-neutral measure:

\[
\frac{dS}{S} = (r - q) dt + \sigma dB^R
\]

---

## 1.2 Core Assumptions

- Constant volatility \(\sigma\)
- Constant interest rate \(r\)
- Constant dividend yield \(q\)
- Continuous trading
- No arbitrage

---

## ⚠️ Critical Point

\[
\sigma = \text{constant}
\]

👉 This is the **ONLY assumption that really breaks in reality**

---

# 2. Pricing via Digitals

## 2.1 Digital Call

\[
\text{Payoff} = 1_{\{S(T) \ge K\}}
\]

\[
\text{Price} = e^{-rT} \mathbb{P}^R(S(T) \ge K)
\]

---

## 2.2 Share Digital

\[
\text{Payoff} = S(T) 1_{\{S(T) \ge K\}}
\]

\[
\text{Price} = e^{-qT} S_0 \mathbb{P}^V(S(T) \ge K)
\]

---

## 2.3 Tail Probabilities

\[
\mathbb{P}(S(T) \ge K) = N(d)
\]

\[
d = \frac{\ln(S_0/K) + \alpha T}{\sigma \sqrt{T}}
\]

---

# 3. Derivation of Black-Scholes

## 3.1 Define d1, d2

\[
d_1 = \frac{\ln(S_0/K) + (r - q + \frac{1}{2}\sigma^2)T}{\sigma \sqrt{T}}
\]

\[
d_2 = d_1 - \sigma \sqrt{T}
\]

---

## 3.2 Final Formula

\[
C = e^{-qT} S_0 N(d_1) - e^{-rT} K N(d_2)
\]

\[
P = e^{-rT} K N(-d_2) - e^{-qT} S_0 N(-d_1)
\]

---

## 3.3 Interpretation

- First term = expected stock payoff
- Second term = expected strike payment

👉 Pricing = expectation under different numeraires

---

# 4. Greeks

## 4.1 Definitions

| Variable | Greek | Meaning |
|---------|------|--------|
| S | Delta | sensitivity |
| S | Gamma | convexity |
| T | Theta | time decay |
| σ | Vega | volatility sensitivity |
| r | Rho | interest rate sensitivity |

---

## 4.2 Key Formulas

### Delta

\[
\Delta = e^{-qT} N(d_1)
\]

### Gamma

\[
\Gamma = \frac{e^{-qT} n(d_1)}{S \sigma \sqrt{T}}
\]

### Vega

\[
\nu = e^{-qT} S n(d_1) \sqrt{T}
\]

---

# 5. Delta & Gamma Hedging

## 5.1 Delta Hedging

Replicate option:

- Long Δ shares
- Borrow cash

---

## 5.2 Key Insight

Continuous hedge ⇒ perfect  

Discrete hedge ⇒ error  

---

## 5.3 Gamma Hedging

Add second option:

\[
-\delta + a + b\delta' = 0
\]

\[
-\Gamma + b\Gamma' = 0
\]

---

# 6. Implied Volatility

## 6.1 Definition

Solve:

\[
C_{market} = C_{BS}(\sigma)
\]

---

## 6.2 Interpretation

👉 σ is NOT observable  
👉 Implied vol = market pricing of risk  

---

## 6.3 Important Insight

Price is hard to compare  

Volatility standardizes everything  

---

# 7. Term Structure of Volatility

## 7.1 Observation

Different maturities ⇒ different σ  

---

## 7.2 Formula

\[
\hat{\sigma}^2 = \frac{1}{T} \int_0^T \sigma^2(t) dt
\]

---

## 7.3 Interpretation

- Implied vol = average variance  
- Leads to forward vol  

---

# 8. Smiles and Smirks

## 8.1 Empirical Fact

\[
\sigma = \sigma(K)
\]

---

## 8.2 Shape (Equity)

- Low strike → high vol  
- ATM → lowest  
- High strike → slight increase  

👉 Smirk  

---

## 8.3 Why This Happens

### (1) Fat Tails

\[
P(\text{extreme moves}) \uparrow
\]

---

### (2) Skew

\[
P(\text{downside}) > P(\text{upside})
\]

---

## 8.4 Conclusion

❌ Not lognormal  
❌ Not Black-Scholes  

---

# 9. Key Intuition

## 9.1 What is Implied Vol?

👉 Price of insurance  

---

## 9.2 Why left side high?

Crash risk  

---

## 9.3 Big Picture

| Model | Reality |
|------|--------|
| constant σ | σ(K,T) |
| lognormal | fat tail |
| symmetric | skew |

---

## Final Insight

\[
\text{Vol Surface} = \text{Market's Risk View}
\]

