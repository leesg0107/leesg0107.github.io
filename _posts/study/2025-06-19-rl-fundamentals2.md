---
layout: post
title: "Visual SLAM vs Occupancy Networks"
subtitle: "Vision-based mapping without LiDAR: trade-offs and when to use each"
tags: [robotics, localization,mapping,camera]
author: solgyu
category: study
mathjax: true
---

 What makes these two exciting is simple: both promise localization and mapping with plain cameras—no LiDAR. LiDAR is fantastic, but it’s heavy, power‑hungry, and not always practical. Cameras are cheap, ubiquitous, and versatile; with the right algorithms, they can do a lot. So let’s have a friendly duel: Visual SLAM vs Occupancy Networks. We won’t crown a universal champion—trade‑offs and use cases decide—but the comparison is fun and useful.

## Warm-up: Who are they?

- Visual SLAM
  - Tracks image features (e.g., ORB‑SLAM family) or uses direct photometric alignment to jointly estimate camera pose and a map (points/keyframes).
  - Inputs: Monocular / Stereo / RGB‑D / VIO (with IMU). Pure monocular has scale ambiguity; stereo/depth/IMU resolves it.
  - Outputs: Accurate trajectory, sparse/semi‑dense map, loop‑closure to rein in drift.
  - Canon: ORB‑SLAM2/3, DSO, VINS‑Fusion, OKVIS.

- Occupancy Networks (neural occupancy/implicit fields, BEV occupancy)
  - A network predicts occupancy probabilities over 3D coordinates or a BEV grid, yielding free‑space vs obstacles. Needs training data; infers 3D from multi‑view or sequential camera frames.
  - Pros: Data‑driven robustness to poor texture/lighting, easy to fuse semantics, dense map outputs.
  - Cons: Training cost and inference compute, harder real‑time, generalization concerns.
  - Note: Tesla’s Occupancy Network uses cameras only (no LiDAR) to predict BEV occupancy—not “no cameras.”

## Round 1 — Setup & Cost
**Winner: Visual SLAM**  
No training data or large‑scale modeling required. Calibrate and go. Occupancy adds training and serving infra, increasing upfront cost.

## Round 2 — Compute & Real‑time
**Winner: Visual SLAM (usually)**  
SLAM often runs light with low latency. Occupancy can be real‑time with quantization/pruning and smaller BEV, but embedded budgets make it harder.

## Round 3 — Map Density & Semantics
**Winner: Occupancy**  
SLAM excels at pose and sparse maps; occupancy excels at dense free‑space/obstacle representation and semantic fusion.

- Visual SLAM
  - Pros: Low latency, consistent trajectory, relatively light compute. Very accurate with good features/texture.
  - Limits: Weak texture, repetitive patterns, harsh lighting, and dynamic objects can hurt. Monocular has scale ambiguity; long runs accumulate drift.

- Occupancy Networks
  - Pros: Dense 3D occupancy/free‑space, easy semantic fusion, wide FoV via multi‑camera.
  - Limits: Inference latency/compute, dataset bias and OOD generalization, stability for online updates.

## Round 4 — Robustness
**Draw (depends)**  
Feature‑poor or lighting‑harsh scenes break classic SLAM (stereo/IMU helps). Occupancy can learn around it, but OOD data still bites.

## Round 5 — Developer Experience
**Winner: Visual SLAM**  
Start without labels or a training pipeline; Occupancy wants MLOps.

## Round 6 — Scaling & Planning Fit
**Winner: Occupancy**  
BEV occupancy makes free‑space explicit, which planners love.

---

Mini scorecard (very subjective):
- Setup/Cost: SLAM ✅
- Real‑time: SLAM (usually) ✅
- Dense map/Semantics: Occupancy ✅
- Tough environments: Depends ⚖️
- DevEx: SLAM ✅
- Planning fit: Occupancy ✅

## When to use which? (serious mode)

- Tiny budget/embedded; stability now → Visual SLAM (+IMU).
- Wide‑FoV free‑space/obstacle/semantic richness → Occupancy/BEV (with quantization or offloading).
- Most practical: hybrid—SLAM for pose/loop‑closure; occupancy for dense space/semantics.

## Pipeline sketches

- Visual SLAM
  1) Camera/IMU calibration → 2) Feature tracking / pose estimation → 3) Loop closure / BA → 4) Local map → 5) Planning

- Occupancy Network (BEV example)
  1) Multi‑view/depth alignment → 2) Spatiotemporal encoding (backbone) → 3) 3D/BEV transform → 4) Occupancy field prediction → 5) Post‑processing/planning

## Minimal setups

- RPi 5 + monocular: ORB‑SLAM3 (mono+IMU) or VINS‑Fusion for VIO; start slow.
- Multi‑camera (or RGB‑D) + GPU: Light BEV occupancy model (e.g., Fast‑BEV/BEVFormer‑style), stabilized with SLAM poses.

## TL;DR

- Not “who’s universally best,” but “who fits today’s needs.”
- Need lightweight, stable navigation now → Visual SLAM.
- Want richer scene understanding (free‑space/semantics/dense map) → Occupancy.
- Honest answer: combine them—SLAM for pose, occupancy for space.

## 참고 자료

- ORB‑SLAM3: [https://arxiv.org/abs/2007.11898](https://arxiv.org/abs/2007.11898)
- VINS‑Fusion: [https://arxiv.org/abs/1712.00036](https://arxiv.org/abs/1712.00036)
- Occupancy Networks: [https://arxiv.org/abs/1812.03828](https://arxiv.org/abs/1812.03828)
- Tesla Occupancy Network(overview): [https://www.tesla.com/AI](https://www.tesla.com/AI)
