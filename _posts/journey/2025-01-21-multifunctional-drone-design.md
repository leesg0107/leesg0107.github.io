---
layout: post
title: "Soltrone Development Log #1"
subtitle: "When hardware meets reality: My first lessons in drone engineering"
tags: [soltrone, drone, raspberry-pi, computer-vision, reinforcement-learning, hardware]
author: solgyu
category: journey
subcategory: dev
---

## Nothing Ever Goes According to Plan

Meet **Soltrone** - my ambitious attempt at building a multifunctional drone. The concept seemed straightforward enough: Raspberry Pi 5 + camera + depth camera. Simple, right?

*Spoiler alert: It wasn't.*

## The Current Setup

I'm using ROS2 as middleware, which feels like the right choice for modular development. The plan was to build each functionality as separate packages - clean, organized, professional.

But here's where reality decided to crash the party: this is my first time soldering. Ever. And let's just say my soldering skills are... well, they exist. Barely.

The result? I accidentally created a bridge between components, which promptly fried some parts. Now I'm waiting for replacement components while trying to push forward with simulation using IsaacSim.

## The Plot Thickens: Technical Dilemmas

### 1. The Vision vs. Reinforcement Learning Debate

This is where things get philosophically interesting. I have two sensors - a regular camera and a depth camera. The question is: how do I best utilize this data?

**Option 1: Pre-trained Vision Models**
- Pros: Excellent performance out of the box
- Cons: Integrating depth camera data requires additional work/training

**Option 2: End-to-End Reinforcement Learning**
- Pros: Can train on both camera and depth data simultaneously
- Cons: Limited scalability for complex vision tasks

Here's my dilemma: I want to start with Option 2 for obstacle avoidance (it makes sense for this use case), but what about owner recognition? Object detection? More sophisticated vision tasks? RL might hit a ceiling there.

It's that classic engineering trade-off again - immediate simplicity vs. long-term flexibility.

### 2. The Feature Creep Problem

The more I think about Soltrone's potential, the more complex it becomes:
- Vision processing
- Small LLM for user interaction
- Multiple mission modes (security, surveillance, escort)
- Hardware constraints
- Battery limitations

Every addition compounds the complexity exponentially.

## The Harsh Reality of Budget Constraints

Let's be brutally honest: **I'm broke.** 

University funding is limited, and my personal budget for this project is... let's call it "creative." This means every component choice becomes a careful balance between cost and performance.

The learning curve has been expensive:
- 5 successful test flights ✅
- 1 burnt ESC ❌
- 1 potentially defective depth camera ❌
- 1 near-miss with the Raspberry Pi 5 ❌

Each failure is a lesson, but also a financial hit. I need to be more careful, more methodical.

## Current Status and Next Steps

**Where I am now:**
- Waiting for replacement ESC
- Depth camera issues need troubleshooting
- Simulation environment set up in IsaacSim
- Basic control algorithms in development

**What I've learned:**
1. **Hardware debugging is expensive** - both in time and money
2. **Every design decision has cascading effects** - there's no such thing as a simple change
3. **Simulation first, hardware second** - validate everything virtually before touching real components
4. **Budget constraints force creative solutions** - sometimes limitations lead to better designs

## The Path Forward

Soltrone v1 will be conservative by design. I'm focusing on:
- Stable basic control
- Single-sensor obstacle avoidance
- Robust hardware connections (no more soldering disasters!)
- Incremental feature additions

The dream of a fully autonomous, multi-modal AI drone is still there. But I'm learning that the path to that dream is paved with burnt ESCs, budget constraints, and the occasional hardware catastrophe.

## SOLIFE Reflection

This project is teaching me that engineering isn't just about algorithms and code - it's about managing complexity, working within constraints, and learning from spectacular failures.

Every burnt component, every failed test, every "why isn't this working?" moment is part of the journey. The goal isn't to avoid mistakes - it's to learn from them faster than they can bankrupt me.

---

*Next update: Hopefully with a working ESC and fewer sparks flying.* ⚡

**Status**: Soltrone v1 development continues. The dream is alive, even if the ESC isn't. 🚁✨
