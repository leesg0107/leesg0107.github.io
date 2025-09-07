---
layout: post
title: "Soltrone Development Log #1"
subtitle: "When hardware meets reality: My first lessons in drone engineering"
tags: [soltrone, drone, raspberry-pi, computer-vision, reinforcement-learning, hardware]
author: solgyu
category: journey
subcategory: dev
---

I thought “Pi 5 + a camera, how hard could it be?” The answer arrived the first time I held a soldering iron. One shaky joint later, two pads decided to become best friends and an ESC paid the price. While waiting for replacements, I moved everything I could into IsaacSim and started building the project like LEGO blocks in ROS2: one package per function, neat and modular in theory, a little chaotic in practice.

The vision plan looked simple on paper: there’s a regular camera and a depth camera, so let’s use both. Then the reality check: fancy pre‑trained models are nice until you try to marry them with depth properly; end‑to‑end RL is elegant until the problem wants more semantics than reflexes. The compromise is boring but sane: start with a single sensor and a narrow goal (obstacle avoidance), leave the door open for a proper vision stack when the airframe and budget are less fragile.

Speaking of budget, this build has taught me that money is also a sensor. Five successful spins, one cooked ESC, one suspicious depth camera, and a very lucky Raspberry Pi later, I now test like I’m paying per mistake—because I am. Heat‑shrink found its way onto every exposed lead, continuity tests became a ritual, and I stopped pretending that “just one more try” will fix wiring.

What tripped me up this round was the depth camera occasionally acting like a diva. It smells like power noise or a flaky unit; the plan is to test it with a clean supply, new cable, and a firmware reset before I call it guilty. Until then, I won’t hinge any milestone on it.

So the v1 charter is now humble on purpose: keep control stable, avoid walls with one sensor, and make every connection strong enough that a wobbly joint can’t ruin the afternoon. It’s not the sci‑fi dream yet, but it’s the kind of progress that actually sticks.

Next time, with a working ESC and fewer sparks, I’ll try to make the sim and the bench feel like the same machine. 

Status: v1 scope is locked; hardware reworked; sim‑first rhythm is working. 🚁
