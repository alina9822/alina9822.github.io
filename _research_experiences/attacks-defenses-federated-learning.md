---
layout: course
title: Attacks and Defenses in Federated Learning
description: Reproduced Bhagoji et al.'s model-poisoning attacks on federated learning and evaluated Krum and coordinate-wise median as defenses.
instructor: Md Shahedul Haque (then MS student, Virginia Tech)
instructor_label: Mentor
year: 2025
term: Research Collaboration
time: April 2025 - June 2025
course_id: attacks-defenses-federated-learning
---

## Key Contributions

- Following a literature review and paper presentation on attacks and defenses in federated learning, reproduced Bhagoji et al., "Analyzing Federated Learning through an Adversarial Lens" (ICML 2019), on a 10-client federated system built on Adult Census with an MLP implemented from scratch, covering its three model-poisoning attacks (explicit boosting, stealthy poisoning, alternating minimization).
- Across 60 runs (6 attack/defense setups x 5 seeds x 2 target directions), matched the paper's FedAvg results and found that Krum and coordinate-wise median blocked the attacks tested against them in every valid run, unlike the paper's Fashion-MNIST results.
