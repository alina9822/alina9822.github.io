---
layout: post
title: Our paper "Extending Feature Selection Strategies in VGG16 Convolutional Feature Aggregation for Content-Based Image Retrieval" published in IEEE ICMI 2026!
date: 2026-06-04 16:11:00-0400
inline: false
related_posts: false
---

Our paper, co-authored with Kuljit Shantanu Saha and supervised by Prof. Md. Monirul Islam, has been published at the 2026 IEEE 5th International Conference on Computing and Machine Intelligence (ICMI). Read it on [IEEE Xplore](https://ieeexplore.ieee.org/abstract/document/11539861), or check out the [publication entry](/publications/) and the [research experience write-up](/research-experience/) on this site.

#### Abstract

> Content-Based Image Retrieval (CBIR) has achieved significant advances through deep learning, with VGG16 serving as a widely adopted architecture for robust feature extraction. Conventional feature aggregation strategies, such as AdCoW+I, rely on a single dominant feature channel, potentially overlooking complementary semantic information from other channels. This paper proposes an adaptive multi-channel fusion framework that selects multiple high-ranking channels based on a dataset-driven threshold (γ) through an iterative process for optimization. The selected channels are fused using weighted averaging. Spatial and channel-wise weighting schemes are applied to enhance discriminative capability while reducing visual burstiness. Experiments on the Oxford5K and Paris6K datasets demonstrate consistent improvements over AdCoW+I, with Mean Average Precision (MAP) increasing from 39.7% to 42.04% on Oxford5K and from 43.78% to 46.12% on Paris6K. Grayscale features were also evaluated, yielding dataset-dependent effects—beneficial for Oxford5K but detrimental for Paris6K. The results highlight the potential of adaptive multi-channel fusion to improve CBIR accuracy.

![Dominance-ranked channel fusion, spatial masking, and weighting pipeline](/assets/img/publication_preview/thesisPaper.png)
