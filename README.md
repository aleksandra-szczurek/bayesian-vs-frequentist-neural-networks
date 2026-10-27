# Deep Learning for Temperature Forecasting: Bayesian vs Frequentist Neural Networks

**MSc thesis** – University of Perugia, Department of Economics (2026)  
Supervisor: Prof. Francesco Bartolucci

> **Research question:** are Bayesian neural networks worth their higher computational cost?

## Overview

A classical neural network and a Bayesian neural network with **identical architecture** are trained and evaluated under the same conditions on two monthly temperature series:

| Dataset | Description |
|---|---|
| **Nottingham** (`nottem`, R) | Clean benchmark, 1920–1939, 240 observations |
| **Rome** (`nasapower`, R) | Real-world NASA POWER reanalysis data, noisier and longer |

## Method

- **Features:** 12 monthly lags + cyclical month encoding (sine/cosine) → 14 inputs
- **Split:** chronological 80/20, standardisation fitted on the training set only (no data leakage)
- **Architecture:** feed-forward network, 1 hidden layer with 8 neurons
- **Frequentist model:** `nnet` (point estimates, weight decay)
- **Bayesian model:** `bnns` (Stan, Hamiltonian Monte Carlo / NUTS), 4 chains, 6,000 iterations, all R̂ < 1.01
- **Evaluation:** RMSE and MAE for both models; for the BNN also 90% credible-interval coverage, interval width and mean predictive log-likelihood

## Results

| Metric | Nottingham – NN | Nottingham – BNN | Rome – NN | Rome – BNN |
|---|---|---|---|---|
| RMSE | 3.75 | **2.14** | 1.98 | **1.46** |
| MAE | 2.95 | **1.74** | 1.58 | **1.09** |
| 90% coverage | – | 91.3% | – | 84.7% |
| Mean interval width | – | 7.51 °F | – | 4.27 °C |

- The Bayesian network was **more accurate on both datasets** and also returned **well-calibrated uncertainty**.
- Predictive uncertainty was **decomposed into aleatoric and epistemic components**: in both series the irreducible data noise dominates.
- A calibration problem (coverage of only 48%) was diagnosed and fixed: the package's `predict` function omitted the observation noise, which was re-estimated from the training residuals.

**Conclusion:** the extra computational cost is justified wherever reliable uncertainty quantification matters; when speed is the only priority, classical networks remain a solid alternative.

## Tools

R 4.4.1 · nnet · bnns · Stan · nasapower
