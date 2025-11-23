---
layout: post
title: Agent Mcqueen
subtitle: Head-to-Head Competitive Racing using PPO
tags: [rl, ppo, f1tenth]
author: solgyu lee
category: project
mathjax: false
thumbnail-img: "/assets/img/Agent-Mcqueen/agent-mcqueen-thumnail.png"
---
Are you an F1 fan? With the recent F1-themed movie release, racing has gained tremendous popularity, drawing many new enthusiasts into the sport. While I'm not a hardcore fan myself, I do enjoy catching clips from time to time.

I've thought about why people are so captivated by racing. My answer: the dynamic driving and the strategic battles—overtaking, defending, positioning. These behaviors are nearly impossible to capture with traditional control algorithms where inputs and outputs are rigidly defined. That's where reinforcement learning comes in. Through trial and error, RL can learn the kind of dynamic racing that professional drivers exhibit.

This sparked my idea: train an agent to race competitively. I thoroughly enjoy naming projects, and when I think of racing, the movie "Cars" immediately comes to mind—specifically Lightning McQueen. Hence, Agent Mcqueen was born.

## Two-Stage Training Approach

I knew from the start that jumping straight into competitive racing would be futile. Computers are surprisingly dumb at first. So I divided the training into two stages:

- **Stage 1**: Train a single agent to complete various tracks independently
- **Stage 2**: Load two trained models from Stage 1 and have them compete head-to-head

## Stage 1: Solo Track Completion

I referenced [this GitHub repository](https://github.com/meraccos/f1tenth_reinforcement_learning) to build Stage 1. However, the code appeared incomplete—missing environment initialization and other critical components—so I had to fill in the gaps myself.

The training uses the PPO (Proximal Policy Optimization) reinforcement learning algorithm. I randomly generated 450 tracks with their corresponding centerlines, and each episode randomly selects one of these maps for the agent to drive on. The agent perceives the world solely through its observations: lidar scans and linear velocity. It has no knowledge of the reward structure. If you're unfamiliar with this concept, check out my reinforcement learning taxonomy post!

Behind the scenes, the system uses a k-d tree to find the nearest centerline waypoint from the agent's pose, converts it to Frenet coordinates, and uses this for reward calculation. The agent naturally learns that following the centerline maximizes its reward.

To improve model generalization, I implemented domain randomization by randomly placing obstacles on the tracks during training. This ensured the agent could handle various track configurations and obstacles, leading to robust performance across most maps. I trained for 10 million steps, which took approximately 13 hours on my host machine.

<img src="/assets/img/Agent-Mcqueen/stage1-domain-randomization.png" alt="Domain Randomization with Obstacles" style="width:100%;">

<img src="/assets/img/Agent-Mcqueen/stage1-tensorboard.png" alt="Stage 1 Training Progress" style="width:100%;">

<video width="100%" controls>
  <source src="/assets/img/Agent-Mcqueen/agent-mcqueen-stage1-eval.webm" type="video/webm">
  Your browser does not support the video tag.
</video>

There were countless trial-and-error moments, mostly related to environment initialization. The centerline dataset needed to reload correctly for each randomly selected track, but improper initialization caused the agent to only drive perfectly on map #50. I had trusted the reference GitHub too much, my mistake entirely. Lesson learned.

After training completed, I tested on 23 tracks from the f1tenth-racetracks dataset. The agent successfully completed 21 out of 23 tracks. This entire stage took about a week.

<video width="100%" controls>
  <source src="/assets/img/Agent-Mcqueen/agent-mcqueen-stage1-f1tenth.webm" type="video/webm">
  Your browser does not support the video tag.
</video>

## Stage 2: Competitive Racing

Now the real challenge began. I initially thought switching from PPO to MAPPO (Multi-Agent PPO) would be straightforward(how naive). Transitioning from single-agent RL to multi-agent RL (MARL) completely changes the game. Adding just one agent introduces a cascade of problems: non-stationarity, credit assignment, and more. Moreover, MAPPO uses cumulative rewards, making it suitable for cooperative settings. But racing is competitive with a zero-sum reward structure. MAPPO was fundamentally incompatible with my task.

I restructured the code to handle zero-sum rewards, but whenever I started training both agents, they would eventually lose even their basic driving ability. This was particularly frustrating since I loaded perfectly trained models from Stage 1, only to watch them regress.

I made a drastic decision: abandon MARL entirely. Instead, I froze one agent and trained only the other. But this still had issues, likely due to non-stationarity, a chronic MARL problem. The agent perceives the world only through observations, meaning it sees the other agent as just another obstacle, like a wall. But this obstacle keeps moving, making the environment continuously shift from the agent's perspective. This disrupts learning significantly.

So I added the opponent agent's pose to the observations. This makes sense like F1 drivers don't race without knowing who they're competing against. Allowing the agent to observe its opponent felt like a natural addition.

The result: the agent successfully learned aggressive overtaking behavior. However, I imposed one condition. Since both agents used the same model, they drove equally well. Having the frozen agent run at full speed made winning nearly impossible. So I reduced the frozen agent's speed to 80%.

One additional trick: I scaled down the opponent information in the observations to very small values. When I used larger scaling factors, the agent became too focused on the opponent and its driving capability deteriorated.

<video width="100%" controls>
  <source src="/assets/img/Agent-Mcqueen/agent-mcqueen-stage2.webm" type="video/webm">
  Your browser does not support the video tag.
</video>

## An Interesting Discovery

During my extensive testing in Stage 2, I made an interesting observation in my `render_initial.py` script. I wrote this to render the initial state, allowing me to intuitively check for wall collisions and spawn positions. After getting all the settings right and running it a few times, something remarkable happened: with Agent 0 (frozen) running at 80% speed, Agent 1 (trainable) naturally overtook Agent 0—without any additional training. This gave me confidence that I didn't need to heavily weight overtaking behavior. I simply reduced the opponent information scaling factor even further and proceeded with training.

## Conclusion(working on...)

All code will be uploaded to GitHub. However, I need some time to clean it up and make it immediately usable for others who want to test it.
