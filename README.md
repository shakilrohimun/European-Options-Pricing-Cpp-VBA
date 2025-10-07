# Option Pricing & Hedging — Black–Scholes and Local Volatility (Theory Only)

> Theoretical foundations of option valuation and hedging under **Black–Scholes** and **Local Volatility** models. No implementation details are included.  
> Implemented in [![C++](https://img.shields.io/badge/Language-C%2B%2B-00599C.svg?logo=cplusplus&logoColor=white)](https://isocpp.org/) [![VBA](https://img.shields.io/badge/Language-VBA-1F2A44.svg?logo=microsoft-office&logoColor=white)](https://learn.microsoft.com/office/vba/api/overview/)

---

## Executive Summary

This project formalizes the pricing of European options via **risk-neutral valuation**, derives the **Black–Scholes PDE**, presents **closed-form prices** (calls/puts), and develops **Greek sensitivities** for risk management. It then generalizes to **Local Volatility** models with a state- and time-dependent volatility surface and the associated PDE. Hedging follows from **delta-neutral self-financing portfolios**. These elements provide a rigorous backbone for pricing/hedging engines and calibration workflows.

---

## Probabilistic Setup & Risk-Neutral Measure

We work on a filtered probability space $(\Omega,\mathcal{F},(\mathcal{F}_t)_{t\ge0},\mathbb{P})$ supporting a standard Brownian motion $B_t$.  
Under the risk-neutral measure, the (continuous-dividend-free) underlying $S_t$ follows a **geometric Brownian motion (GBM)**:
```math
\mathrm{d}S_t = r\,S_t\,\mathrm{d}t + \sigma\,S_t\,\mathrm{d}B_t,
\qquad
S_t = S_0 \exp\!\big[(r-\tfrac12\sigma^2)t + \sigma B_t\big],
```
with constant short rate $r$ and constant volatility $\sigma$. Pricing uses the discounted conditional expectation under the risk-neutral measure:
```math
P_t = \mathbb{E}\!\left[e^{-r(T-t)}\,f(S_T)\,\middle|\,\mathcal{F}_t\right].
```

---

## Black–Scholes PDE and Closed-Form Solutions

Let $V=V(t,S)$ denote the value of a European option. By Itô’s lemma and no-arbitrage hedging, $V$ solves the **Black–Scholes PDE**:
```math
\frac{\partial V}{\partial t}
+ r S \frac{\partial V}{\partial S}
+ \frac{1}{2}\sigma^2 S^2 \frac{\partial^2 V}{\partial S^2}
= r V,
\qquad
V(T,S)=f(S).
```

For a European call/put with strike $K$ and maturity $T$, the **closed-form** prices are:
```math
C(t,S) = S\,\Phi(d_1) - K e^{-r\tau}\,\Phi(d_2), \qquad
P(t,S) = K e^{-r\tau}\,\Phi(-d_2) - S\,\Phi(-d_1),
\quad \tau = T-t,
```
with
```math
d_1 = \frac{\ln(S/K) + (r + \tfrac12\sigma^2)\tau}{\sigma\sqrt{\tau}},
\qquad
d_2 = d_1 - \sigma\sqrt{\tau},
```
and $\Phi$ the standard normal CDF. The **put–call parity** $C-P = S - K e^{-r\tau}$ follows from a forward replication argument.

---

## Greeks (Sensitivities) for Risk Management

**Delta** (sensitivity to $S$):
```math
\Delta_{\text{call}}=\Phi(d_1), \qquad
\Delta_{\text{put}}=\Phi(d_1)-1.
```

**Gamma** (sensitivity of Delta to $S$):
```math
\Gamma = \frac{\varphi(d_1)}{S\,\sigma\sqrt{\tau}},
```
with $\varphi$ the standard normal PDF.

**Vega** (sensitivity to $\sigma$):
```math
\mathrm{Vega} = S \sqrt{\tau}\,\varphi(d_1).
```

**Theta** (time decay):
```math
\Theta_{\text{call}} =
- \frac{S\sigma\,\varphi(d_1)}{2\sqrt{\tau}} - rK e^{-r\tau}\Phi(d_2),
\qquad
\Theta_{\text{put}} =
- \frac{S\sigma\,\varphi(d_1)}{2\sqrt{\tau}} + rK e^{-r\tau}\Phi(-d_2).
```

**Rho** (sensitivity to $r$):
```math
\rho_{\text{call}} = K\tau e^{-r\tau}\,\Phi(d_2),
\qquad
\rho_{\text{put}}  = -K\tau e^{-r\tau}\,\Phi(-d_2).
```

---

## Hedging Rationale (Delta-Neutral Self-Financing)

Consider a portfolio long $\Delta_t$ units of $S_t$ and short one option $V(t,S_t)$: $\Pi_t = \Delta_t S_t - V(t,S_t)$.  
Choosing $\Delta_t = \partial V/\partial S$ eliminates the diffusion term in $\mathrm{d}\Pi_t$, making the portfolio locally riskless and thus earning the risk-free rate $r$. This **delta hedging** argument both yields the PDE and underpins practical risk control.

---

## Local Volatility Model (Deterministic Surface $\sigma(t,S)$)

To capture empirical features such as volatility smiles/smirks, the **Local Volatility** model assumes a time- and state-dependent volatility:
```math
\mathrm{d}S_t = r\,S_t\,\mathrm{d}t + \sigma(t,S_t)\,S_t\,\mathrm{d}B_t.
```
The corresponding valuation PDE for $v(t,x)\equiv V(t,S=x)$ is
```math
\frac{\partial v}{\partial t}
+ \frac{1}{2}\,\sigma^2(t,x)\,x^2 \frac{\partial^2 v}{\partial x^2}
+ r x \frac{\partial v}{\partial x}
- r v = 0,
\qquad
v(T,x)=f(x).
```
Here $\sigma(t,S)$ is a (deterministic) function calibrated to market option prices, enabling a state-dependent instantaneous variance consistent with observed implied-volatility structures.

---

## Theoretical Takeaways

- **Risk-neutral pricing + no-arbitrage hedging** $\Rightarrow$ Black–Scholes PDE and closed-form solutions for European options.  
- **Greeks** quantify first- and second-order sensitivities, crucial for hedging and risk management.  
- **Local Volatility** generalizes constant-$\sigma$ models by allowing $\sigma(t,S)$, improving consistency with market smiles while preserving a one-factor diffusion and a parabolic pricing PDE.

---

**Author:** Shakil Rohimun  
**License:** Private Academic Use – Université Paris-Saclay
