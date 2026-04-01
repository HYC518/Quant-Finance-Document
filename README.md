# Quant Finance Reference Documents

A collection of comprehensive reference guides for quantitative finance topics, built from FIN4370/5370 coursework at Washington University in St. Louis (Prof. Hong Liu).

These documents are designed to be **memorization-friendly** — every formula comes with intuition, derivation, and common pitfalls.

---

## Documents

### 1. [`ito_lemma_reference.md`](./ito_lemma_reference.md)
A comprehensive reference for Itô calculus and stochastic processes. Covers:
- Why stochastic calculus differs from ordinary calculus
- Itô's Lemma — full statement and derivation
- Key application: d(log S) and the -½σ² correction
- Volatility of ratio of two assets
- Money market account R(t)
- Martingale properties under different measures
- State price density and measure changes
- When futures prices = forward prices
- Time-varying volatility
- Common mistakes and pitfalls
- Quick reference summary table

### 2. [`forward_futures_exchange_options_guide.md`](./forward_futures_exchange_options_guide.md)
Extensive explanation of forward, futures, and exchange options pricing. Covers:
- The general pattern behind ALL option formulas
- Margrabe's Formula (exchange options) — full derivation
- Black's Formula (options on forwards) — full derivation
- Merton's Formula (stochastic interest rates)
- Deferred exchange options
- Greeks and hedging tables
- Futures vs forward price relationship
- Futures options formula
- Time-varying volatility
- Market completeness
- VBA implementation
- Formula cheat sheet

---

## Key Concepts at a Glance

### The Universal Option Pricing Pattern
Every formula in this repo follows:
```
Option Value = PV(asset received) × N(x) - PV(asset delivered) × N(y)
x = [log(PV_received/PV_delivered) + ½σ²T] / (σ√T)
y = x - σ√T
```

### Itô's Multiplication Table
| × | dt | dB |
|---|----|----|
| dt | 0 | 0 |
| dB | 0 | dt |

### Martingale Properties
| Process | Martingale Under |
|---------|-----------------|
| Futures price F* | Risk-neutral measure Q |
| Forward price F | T'-forward measure |
| F* = F | Both (when r deterministic) |

---

## How to Use These Documents

These are meant as **active study references**, not passive reading:

1. Read the derivation once carefully
2. Close the document and try to reproduce the formula
3. Check against the quick reference at the end
4. Focus especially on the **intuition sections** — formulas are easier to remember when you understand why they look the way they do

---

## Course Information

- **Course:** FIN4370/5370 — Derivative Securities
- **Institution:** Washington University in St. Louis
- **Professor:** Hong Liu
- **Topics:** Black-Scholes, Margrabe, Black's, Merton's formulas; Foreign Exchange; Quantos; Return Swaps

---

## Contributing

Feel free to open issues or PRs if you spot errors or want to add content!

---

*Built with help from Claude (Anthropic)*
