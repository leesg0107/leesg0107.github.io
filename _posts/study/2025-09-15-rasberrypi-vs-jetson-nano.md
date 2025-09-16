---
layout: post
title: "Raspberry Pi 5 VS Jetson Orin Nano Super"
subtitle: "The Ultimate Edge AI Showdown"
tags: [robotics, hardware, ai, edge-computing, jetson, raspberry-pi]
author: solgyu
category: study
mathjax: true
---

## Jetson Orin Nano Super: The New Game Changer in Edge AI

When you're working on AI-heavy projects like drones, robots, or autonomous agents, there's always that moment when "performance" becomes the bottleneck. I've been hitting those limits with Raspberry Pi 5 while building Soltrone v1. That's why I decided to invest in the Jetson Orin Nano Super for future projects like Soltrone v2. This post dives into what's actually changed with the Orin Nano Super and what becomes possible when compared to the Raspberry Pi family. 

## Jetson Orin Nano Super Core Specifications

Here are the key specs of the Orin Nano Super based on official announcements and NVIDIA documentation:

| Specification | Jetson Orin Nano Super Developer Kit |
|---------------|--------------------------------------|
| **GPU + Core Acceleration** | Ampere architecture, 1,024 CUDA cores & 32 Tensor cores |
| **CPU** | 6-core ARM Cortex-A78AE (v8.2, 64-bit) CPU, L2 1.5MB + L3 4MB cache |
| **Clock Speed Boost** | Increased from 1.5 GHz to 1.7 GHz |
| **Memory** | 8 GB, 128-bit LPDDR5, memory bandwidth up to 102 GB/s |
| **AI Performance (Est.)** | Sparse INT8: ≈ 67 TOPS (significant improvement over original Orin Nano)<br>Dense INT8 / FP16 modes also available (slightly lower) |
| **Power Consumption** | 7W ~ 25W (depending on load/configuration mode) |
| **I/O & Expandability** | External NVMe support, microSD slot, camera interfaces (MIPI CSI), USB, expansion headers |
| **Price** | US$249 for developer kit |

### 🤔 What These Numbers Actually Mean

All these specs might look overwhelming, so let me break down what each one actually means:

**1,024 CUDA Cores**  
CUDA cores are the basic computational units of the GPU. Think of them as "general-purpose workers" that each handle 32-bit floating-point operations (FP32). Having 1,024 of them means you can process 1,024 independent calculations simultaneously in parallel.

**Key Functions:**
- Graphics rendering (per-pixel color calculations)
- General parallel computing (vector operations, matrix operations)
- Basic computer vision filtering (convolution, pooling)
- Physics simulations

It's like having 1,024 general technicians, each with their own calculator, working simultaneously. Each one is simple, but together they deliver massive throughput.

**32 Tensor Cores**  
Tensor cores are **dedicated matrix operation accelerators** designed specifically for the AI era. They have a completely different architecture from regular CUDA cores.

**Key Differences:**
- **Computation Method**: While CUDA cores calculate one by one, Tensor cores process **4x4 matrix blocks** at once
- **Data Types**: Specialized for **low-precision operations** like FP16, BF16, INT8, INT4 (AI models don't need high precision)
- **Throughput**: **10-20x faster AI computations** compared to CUDA cores with the same power

**Advanced Features:**
- **Mixed Precision Training**: Fast training with FP16 while maintaining FP32-level accuracy
- **Sparse Matrix Support**: Efficiently handles matrices with many zeros (characteristic of AI models)
- **Transformer Optimization**: Ultra-fast processing of Attention mechanism's core operations (Q×K^T, Attention×V)

To put it simply: if CUDA cores are "general calculators," Tensor cores are "AI-dedicated supercomputers." Though fewer in number (32), each one delivers the performance of dozens of CUDA cores for AI tasks.

**Real Performance Comparison Examples:**  
- BERT model inference: CUDA cores 100ms vs Tensor cores 10ms
- Image classification (ResNet): CUDA cores 50ms vs Tensor cores 5ms

**6-core ARM CPU**  
These are the "chief commanders." They manage the overall program flow and handle complex logical processing that GPUs can't do. Think of them as 6 smart managers, each handling different tasks simultaneously.

**8GB LPDDR5 Memory (102 GB/s)**  
This is the "workspace table" size. 8GB represents how spacious the table is (how much data you can place at once), while 102 GB/s shows how quickly you can retrieve and place materials on that table. Having a large and fast workspace means you can handle bigger projects more efficiently.

**67 TOPS (AI Performance)**  
This means "67 trillion AI operations per second." The number is so huge that to put it in perspective: it's like the entire world population each hitting a calculator 10,000 times per second. That's why it can analyze video in real-time or run language models.

**7W~25W Power Consumption**  
Since smartphone chargers are typically around 20W, this means even at maximum performance, it consumes similar power to charging a smartphone. In low-power mode (7W), it can perform AI computations with just the power needed to light an LED bulb.

## What's Actually Changed?

The Orin Nano Super doesn't differ much from the original Orin Nano in terms of basic hardware (chip, core configuration, etc.), but the significant performance boost comes from software and power/clock adjustments (power mode + latest JetPack updates).

**Key Improvements:**
- **Memory bandwidth increase (up to 102 GB/s)** → Reduces bottlenecks for large models or parallel processing with multi-channel input/video/vision + LLM combinations
- **Overall clock increases** for GPU/CPU/memory → Can handle heavier workloads than before
- **Price maintained or reduced** while improving developer accessibility

## Orin Nano Super vs Raspberry Pi 5: Real-World Comparison

This comparison is based on realistic predictions + benchmarks I've seen + experience-based estimates. No exaggeration - focusing on practical possibilities and limitations.

| Comparison Category | Raspberry Pi 5 | Jetson Orin Nano Super |
|-------------------|----------------|-------------------------|
| **Lightweight Computer Vision / Object Detection**<br>(Small models, e.g., MobileNet / Tiny-YOLO) | Runs well. For single camera, low resolution (e.g., 640×480), frame rates are decent and latency is acceptable. | Much higher frame rates with the same models, can increase resolution/pipeline complexity (e.g., multiple cameras, preprocessing + postprocessing). |
| **LLM / Transformer Model Inference** | Only possible with very small quantized models or very limited cases (typically server/cloud dependent). Latency increases dramatically with larger batch sizes or token lengths. | LLM models (few billion parameters, e.g., 3-8B) can run at reasonably practical speeds on edge in INT8/Sparse mode. Token generation and response latency drop to acceptable levels. |
| **Reinforcement Learning, Agent Training** | Running RL training locally without environment simulation or cloud support leads to resource shortage → slow speeds, many limitations on batch size/environment complexity. | Faster simulation/inference/feedback loops thanks to dedicated GPU. Good potential for offline RL or lightweight environments, and combinations of real-time control loops + visual input + action planning become feasible. |
| **Energy Efficiency / Battery/Edge Devices** | Good in low power consumption modes. However, high loads cause heat + power consumption spikes → increased battery/cooling requirements. | Adjustable power modes (7-25W) allow energy saving when not needed. But high-performance mode requires consideration of battery weight/cooling. |
| **Multi-task / Complex Pipelines**<br>(e.g., Vision + Language + Control + SLAM) | Can handle tasks that "do one thing well," but running multiple tasks in parallel causes resource conflicts and severe speed degradation. | Much more headroom for running multiple components simultaneously. For example, vision-language model (VLM) + camera + flight control loop + obstacle avoidance combinations can achieve useful real-time response rates. |


## My Perspective: How I'd Use Orin Nano Super in Soltrone v2

**Flight Control & Stabilization**: Orin Nano Super + external IMU/sensors + lightweight control loop combination.

**Obstacle Avoidance / Path Planning**: Front/bottom cameras + depth sensor (or stereo/TOF) → vision-based obstacle recognition + avoidance commands.

**Natural Language Command Interface + Mission Planning**: For example, processing commands like "climb 10m over flat terrain, take a photo, and return."

**Short-term Real-time Adaptation**: Simple fine-tuning or module retraining possible for lighting changes or background complexity variations (with small datasets).

**Multi-bot/Drone Swarm Control (Initial Version)**: Equipping each drone with Orin Nano Super significantly increases possibilities for communication + visual recognition + shared information exchange.

## Conclusion: Jetson Orin Nano Super Takes the Win!

The Orin Nano Super isn't simply a "faster version of the Orin Nano" - I think of it as a kit that truly enables running 'generative AI' or complex vision + language + control pipelines at the edge. The Raspberry Pi 5 is still an excellent choice, but as required tasks become even slightly more complex and real-time performance and accuracy become critical, the performance-value proposition of the Orin Nano Super really shines through. 

**So the winner of this competition is Jetson!** 

But we need to think about this objectively. If you're not doing real-time control or running heavy models, the Raspberry Pi 5's cost-effectiveness is still incredible. It really comes down to your specific use case - but for pushing the boundaries of what's possible at the edge, the Orin Nano Super is the clear champion.  