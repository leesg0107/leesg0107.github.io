---
layout: post
title: "PPO in PettingZoo: When Simple Isn't Actually Simple"
subtitle: "My first real lesson in reading the docs (the hard way)"
tags: [dev-log, ppo, pettingzoo, reinforcement-learning, lessons-learned]
author: solgyu
category: journey
subcategory: dev
---

## The Plan (That Didn't Go According to Plan)

So there I was, feeling pretty confident about implementing PPO in a PettingZoo simple environment. I mean, it's called "simple," right? How hard could it be? 

*Narrator: It was, in fact, harder than expected.*

## The Reality Check

I grabbed some PPO code from GitHub (you know, like we all do 😅) and started adapting it for the PettingZoo environment. Everything looked good on paper, but when I ran it... the performance was absolutely terrible. Like, embarrassingly bad.

My first thought was classic: "It must be a hyperparameter issue." So I spent way too much time tweaking learning rates, batch sizes, and every other knob I could find. Nothing worked.

## The "Aha!" Moment (AKA The Facepalm Moment)

After digging deeper into the problem, I finally found the culprit: **action clipping**. But not just any action clipping issue - I was trying to use a PPO implementation designed for **continuous action spaces** in a **discrete action environment**.

The mismatch was causing all sorts of chaos:
- Actions were being converted incorrectly from continuous to discrete
- The clipping logic was completely wrong for discrete spaces
- The environment was probably thinking my agent had lost its mind

## The Lesson That Should Have Been Obvious

Here's the kicker - all of this could have been avoided if I had just **read the environment documentation properly** from the start. 

Understanding the environment's input/output specifications isn't just "nice to have" - it's absolutely crucial for RL implementation. The environment is literally the foundation of everything your agent does, and I was trying to build on quicksand.

## What I Learned (The Hard Way)

1. **RTFM (Read The Fine Manual)**: Environment docs exist for a reason
2. **Action spaces matter**: Continuous vs discrete isn't just a detail - it's fundamental
3. **Assumptions kill**: "Simple" environments can still trip you up if you don't understand them
4. **Debug systematically**: Instead of random hyperparameter tuning, understand the problem first

## Issue, cause, and fix

- Issue: PPO performed poorly in a PettingZoo environment labeled as simple.
- Root cause: Used a PPO implementation for continuous action spaces in a discrete action environment, leading to invalid action clipping and wrong action mapping.
- Fix:
  - Use a discrete policy with a categorical action distribution.
  - Remove continuous-space clipping logic and any Box-space assumptions.
  - Ensure the selected action indexes map directly to the environment's discrete space.

## Quick checklist

- Confirm `env.action_space` is `Discrete` and record its `n`.
- Use a categorical distribution for policy output; no clipping on actions.
- Add assertions for action shape/range and a minimal rollout smoke test.

---

**Current status**: Correct policy/action mapping applied; PPO training behaves as expected.

