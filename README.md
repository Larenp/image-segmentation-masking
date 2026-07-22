# Reference-Guided Image Segmentation using REBASE and SAM

An implementation of a reference-guided image segmentation pipeline inspired by the **REBASE** approach. The system uses a reference image and its segmentation mask to identify and segment a semantically similar target object in a different query image.

The pipeline combines **DINOv2** for semantic feature extraction, **REBASE-style background subspace elimination** for improving cross-image correspondence, **Similarity-Weighted Farthest-Point Sampling (SW-FPS)** for point prompt generation, and the **Segment Anything Model (SAM)** for final segmentation.

> **Status:** Work in progress. The core end-to-end prototype has been implemented, but several components are still being validated and refined for closer reproduction of the original method.

---

## Overview

The goal of this project is to perform **reference-guided segmentation**.

The system receives:

1. A **reference image**
2. A **binary segmentation mask** for the reference image
3. A **query image**

The reference image and mask define the target object. The system then attempts to locate and segment the corresponding semantic object in the query image.

```text
Reference Image + Reference Mask
              │
              ↓
            DINOv2
              │
              ↓
    Reference Feature Extraction
              │
              ↓
   Background Feature Selection
              │
              ↓
             SVD
              │
              ↓
    Background Subspace Basis
              │
              ↓
 Background Subspace Elimination
              │
              ├───────────────┐
              ↓               ↓
   Reference Features     Query Features
              │               │
              └───────┬───────┘
                      ↓
              Cosine Similarity
                      ↓
             37 × 37 Similarity Map
                      │
               ┌──────┴──────┐
               ↓             ↓
             SW-FPS      Dense Prior
               │             │
               ↓             ↓
          Point Prompts   Mask Prior
               │             │
               └──────┬──────┘
                      ↓
                     SAM
                      ↓
          Final Segmentation Mask
