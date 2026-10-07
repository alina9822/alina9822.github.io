---
layout: page
title: Federated Learning with Adversarial Attacks
description: Reproduced Bhagoji et al.'s model-poisoning attacks on a 10-client federated learning system built from scratch, and evaluated FedAvg, Krum, and coordinate-wise median as defenses.
importance: 1.5
category: work
tools:
  - Python
  - Numpy
  - Scikit-learn
github: https://github.com/alina9822/Machine-Learning-Codes/tree/main/Federated%20Learning%20from%20scratch
---

**Technology & Tools:** Python

A reproduction and extension of a key federated learning security paper, built around a hand-written MLP and federated system from scratch.

## Key Work

- **Literature Review & Presentation** — prepared a presentation on attacks and defenses in federated learning.
- **Federated System** — built a 10-client federated learning system on the Adult Census dataset using a hand-written MLP.
- **Model-Poisoning Attacks** — implemented Bhagoji et al.'s three attacks (explicit boosting, stealthy poisoning, alternating minimization) under FedAvg, Krum, and coordinate-wise median.
- **Evaluation** — reproduced the paper's FedAvg results, and across 60 runs (5 seeds x 2 target directions) found that Krum and coordinate-wise median fully blocked the attacks on Adult, unlike the paper's Fashion-MNIST results.

> Reproduced "Analyzing Federated Learning through an Adversarial Lens" (Bhagoji et al., ICML 2019) end-to-end — attacks, defenses, and a from-scratch federated system.
