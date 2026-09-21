---
layout: course
title: Adaptive Feature Selection for Content-Based Image Retrieval (CBIR)
description: Co-developed an adaptive multi-channel aggregation method for CNN-based image retrieval, improving mAP by 2.3 points over the AdCoW+I baseline on Oxford5K and Paris6K.
instructor: Prof. Md. Monirul Islam, Dept. of CSE, BUET
year: 2025
term: Undergraduate Thesis
location: BUET, Dhaka, Bangladesh
time: Oct 2023 - Mar 2025
course_id: adaptive-feature-selection-cbir
---

## Key Contributions

- Co-developed an adaptive multi-channel aggregation method for CNN-based image retrieval, replacing the single-dominant-channel selection used by prior work with a dataset-driven threshold that fuses multiple high-ranking convolutional channels.
- Implemented the full retrieval pipeline over frozen VGG16 conv5_3 features: dominance-ranked channel fusion, spatial masking, Gaussian spatial weighting, and PCA-whitening to 100-d descriptors.
- Improved mAP by 2.3 points over the AdCoW+I baseline on both Oxford5K and Paris6K, with gains consistent across all tested threshold values.
- Analyzed the effect of monochromatic input on retrieval, identifying a dataset-dependent effect that motivates follow-up investigation.

Contributed equally with the first author to method design, implementation, and evaluation. Published at [IEEE ICMI 2026](https://ieeexplore.ieee.org/xpl/conhome/11539750/proceeding) (see [Publications](/publications/)).
