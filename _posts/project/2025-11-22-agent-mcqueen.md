---
layout: post
title: Agent Mcqueen
subtitle: Head-to-Head Competitive Racing using PPO
tags: [rl, ppo, f1tenth, marl]
author: solgyu lee
category: project
mathjax: true
mermaid: true
thumbnail-img: "/assets/img/Agent-Mcqueen/agent-mcqueen-thumnail.png"
---
Are you an F1 fan? With the recent F1-themed movie release, racing has gained tremendous popularity, drawing many new enthusiasts into the sport. While I'm not a hardcore fan myself, I do enjoy catching clips from time to time.

I've thought about why people are so captivated by racing. My answer: the dynamic driving and the strategic battles such as overtaking, defending, positioning. These behaviors are nearly impossible to capture with traditional control algorithms where inputs and outputs are rigidly defined. That's where reinforcement learning comes in. Through trial and error, RL can learn the kind of dynamic racing that professional drivers exhibit.

This sparked my idea: train an agent to race competitively. I thoroughly enjoy naming projects, and when I think of racing, the movie "Cars" immediately comes to mind, specifically Lightning McQueen. Hence, Agent Mcqueen was born.

---

## Why PPO?

Before diving into the implementation, let me explain why I chose PPO (Proximal Policy Optimization) for this project.

| Algorithm Type            | Examples       | Characteristics                         |
| ------------------------- | -------------- | --------------------------------------- |
| **Value-based**     | DQN, DDQN      | Discrete actions only, sample efficient |
| **Policy Gradient** | REINFORCE, A2C | High variance, continuous actions       |
| **Actor-Critic**    | PPO, SAC, TD3  | Balanced stability and efficiency       |

PPO belongs to the Actor-Critic family, combining the best of both worlds:

<div class="mermaid">
flowchart LR
    subgraph AC["Actor-Critic Architecture"]
        STATE[State s]
        ACTOR["🎭 Actor<br/>π(a|s)"]
        CRITIC["📊 Critic<br/>V(s)"]
        ACTION[Action a]
        ADV["Advantage<br/>A = R - V(s)"]
    end

    STATE --> ACTOR
    STATE --> CRITIC
    ACTOR --> ACTION
    CRITIC --> ADV
    ADV -->|"Updates"| ACTOR

</div>

### Key PPO Properties

| Property                     | Description                                 |
| ---------------------------- | ------------------------------------------- |
| **On-Policy**          | Uses data from current policy only          |
| **Clipping Mechanism** | Prevents destructively large policy updates |
| **Trust Region**       | Keeps new policy close to old policy        |

The clipping mechanism is what makes PPO stable:

$$
L^{CLIP}(\theta) = \mathbb{E}_t \left[ \min \left( r_t(\theta) A_t, \text{clip}(r_t(\theta), 1-\epsilon, 1+\epsilon) A_t \right) \right]
$$

where $r_t(\theta) = \frac{\pi_\theta(a_t|s_t)}{\pi_{\theta_{old}}(a_t|s_t)}$ is the probability ratio.

---

## Two-Stage Training Approach

I knew from the start that jumping straight into competitive racing would be futile. Computers are surprisingly dumb at first. So I divided the training into two stages:

| Stage             | Goal                  | Environment       | Agents |
| ----------------- | --------------------- | ----------------- | ------ |
| **Stage 1** | Solo track completion | 450 random tracks | 1      |
| **Stage 2** | Competitive racing    | F1tenth tracks    | 2      |

---

## Stage 1: Solo Track Completion

<video width="70%" controls>
  <source src="/assets/img/Agent-Mcqueen/agent-mcqueen-stage1-render.webm" type="video/webm">
  Your browser does not support the video tag.
</video>

I referenced [this GitHub repository](https://github.com/meraccos/f1tenth_reinforcement_learning) to build Stage 1. However, the code appeared incomplete like missing environment initialization and other critical components, so I had to fill in the gaps myself.

### Observation and Action Space

<div class="mermaid">
flowchart LR
    subgraph OBS["📡 Observation Space"]
        LIDAR["LiDAR Scan<br/>1080 rays"]
        VEL["Linear Velocity<br/>1 value"]
    end

    subgraph AGENT["🧠 PPO Agent"]
        ACTOR["Actor Network"]
        CRITIC["Critic Network"]
    end

    subgraph ACT["🎮 Action Space"]
        STEER["Steering Angle`<br/>`[-0.4, 0.4] rad"]
        SPEED["Target Speed`<br/>`[0, 8] m/s"]
    end

    OBS --> AGENT
    AGENT --> ACT

</div>

| Component          | Specification              |
| ------------------ | -------------------------- |
| **LiDAR**    | 1080 rays, 270° FOV       |
| **Velocity** | Scalar linear velocity     |
| **Steering** | Continuous [-0.4, 0.4] rad |
| **Speed**    | Continuous [0, 8] m/s      |

### Reward Structure

The reward function uses **Frenet coordinates** to measure progress along the track centerline:

<div class="mermaid">
flowchart LR
    POSE["Agent Pose<br/>(x, y, θ)"] --> KD["K-D Tree<br/>Nearest Waypoint"] --> FRENET["Frenet Transform"]

    FRENET --> PROG["📈 Progress<br/>+Δs"]
    FRENET --> LAT["📏 Lateral<br/>-|d|"]
    FRENET --> COL["💥 Collision<br/>-10"]
</div>

| Reward Component            | Formula       | Purpose                    |
| --------------------------- | ------------- | -------------------------- |
| **Progress**          | $+\Delta s$ | Encourage forward movement |
| **Lateral Deviation** | $-\|d\|$    | Stay near centerline       |
| **Collision**         | $-10$       | Avoid crashes              |

### Domain Randomization

<img src="/assets/img/Agent-Mcqueen/stage1-domain-randomization.png" alt="Domain Randomization with Obstacles" style="width:70%;">

To improve model generalization, I implemented domain randomization by randomly placing obstacles on the tracks during training. This ensured the agent could handle various track configurations and obstacles, leading to robust performance across most maps.

| Randomization                | Range                             |
| ---------------------------- | --------------------------------- |
| **Track Selection**    | 450 procedurally generated tracks |
| **Obstacle Placement** | Random positions and sizes        |
| **Starting Position**  | Random spawn along centerline     |

### Training Results

<img src="/assets/img/Agent-Mcqueen/stage1-tensorboard.png" alt="Stage 1 Training Progress" style="width:70%;">

| Metric                   | Value                      |
| ------------------------ | -------------------------- |
| **Training Steps** | 10 million                 |
| **Training Time**  | ~13 hours                  |
| **Success Rate**   | 91% (21/23 F1tenth tracks) |

<video width="70%" controls>
  <source src="/assets/img/Agent-Mcqueen/agent-mcqueen-stage1-eval.webm" type="video/webm">
  Your browser does not support the video tag.
</video>

There were countless trial-and-error moments, mostly related to environment initialization. The centerline dataset needed to reload correctly for each randomly selected track, but improper initialization caused the agent to only drive perfectly on map #50. I had trusted the reference GitHub too much, my mistake entirely. Lesson learned.

---

## Stage 2: Competitive Racing

Now the real challenge began. I initially thought switching from PPO to MAPPO (Multi-Agent PPO) would be straightforward (how naive).

### The MARL Challenge

Transitioning from single-agent RL to multi-agent RL (MARL) completely changes the game:

<div class="mermaid">
flowchart LR
    subgraph CHALLENGES["⚠️ MARL Challenges"]
        NS["🔄 Non-Stationarity"]
        CA["🎯 Credit Assignment"]
        EQ["⚖️ Equilibrium Selection"]
    end

    subgraph MAPPO["MAPPO"]
        COOP["Cooperative Design"]
    end

    subgraph RACING["Racing"]
        COMP["Zero-Sum Competition"]
    end

    CHALLENGES --> MAPPO
    MAPPO -.->|"Incompatible"| RACING
</div>

| Challenge                       | Description                         | Impact on Racing                       |
| ------------------------------- | ----------------------------------- | -------------------------------------- |
| **Non-Stationarity**      | Other agents change during training | Moving "obstacles" disrupt learning    |
| **Credit Assignment**     | Hard to attribute rewards           | Who caused the collision?              |
| **Equilibrium Selection** | Multiple optimal strategies         | Agents may converge to suboptimal play |

I restructured the code to handle zero-sum rewards, but whenever I started training both agents, they would eventually lose even their basic driving ability. This was particularly frustrating since I loaded perfectly trained models from Stage 1, only to watch them regress.

### Solution: Residual Learning with Frozen Expert

I made a drastic decision: abandon MARL entirely. Instead, I developed a **residual learning** approach with a frozen expert agent.

<div class="mermaid">
flowchart LR
    subgraph AGENT0["🥶 Agent 0 (Frozen)"]
        OBS0["LiDAR+Vel"] --> F0["feature_net ❄️"] --> M0["mean/log_std ❄️"] --> ACT0["Action"]
    end

    subgraph AGENT1["🔥 Agent 1 (Trainable)"]
        OBS1["LiDAR+Vel"] --> LN1["lidar_net ❄️"]
        OPPINFO["Opponent Info"] --> ON["opponent_net 🔥"]
        LN1 --> OAN["adjustment_net 🔥"]
        ON --> OAN
        OAN --> ACT1["Action"]
    end
</div>

### Extended Observation Space for Agent 1

| Observation               | Dimension | Description                  |
| ------------------------- | --------- | ---------------------------- |
| **LiDAR Scan**      | 1080      | Same as Stage 1              |
| **Linear Velocity** | 1         | Same as Stage 1              |
| **Delta S (Δs)**   | 1         | Longitudinal gap to opponent |
| **Delta Vs (Δvs)** | 1         | Relative velocity            |
| **Ahead Flag**      | 1         | 1 if ahead, 0 otherwise      |

This design allows Agent 1 to "see" its opponent and learn competitive behaviors while maintaining the expert driving foundation from Stage 1.

### Stage 2 Reward Structure

<div class="mermaid">
flowchart LR
    subgraph REWARDS["Stage 2 Rewards"]
        BASE["📈 Base Driving<br/>(from Stage 1)"]
        GAP["📏 Gap Reward<br/>+Δs (close the gap)"]
        PASS["🏆 Overtake Bonus<br/>+50 (when ahead changes)"]
        SAFE["🛡️ Safety Penalty<br/>-10 (collision)"]
    end
</div>

| Reward                | Value               | Condition                  |
| --------------------- | ------------------- | -------------------------- |
| **Progress**    | $+\Delta s$       | Always                     |
| **Gap Closing** | $+\Delta s_{gap}$ | When behind opponent       |
| **Overtake**    | $+50$             | Successfully pass opponent |
| **Collision**   | $-10$             | Any collision              |

### Training Configuration

| Parameter                       | Value                |
| ------------------------------- | -------------------- |
| **Frozen Agent Speed**    | 80% of trained speed |
| **Opponent Info Scaling** | 0.01 (very small)    |
| **Training Steps**        | 5 million            |

One crucial trick: I scaled down the opponent information in the observations to very small values. When I used larger scaling factors, the agent became too focused on the opponent and its driving capability deteriorated.

<video width="70%" controls>
  <source src="/assets/img/Agent-Mcqueen/IMG_6667.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

---

## ROS2 Integration

The entire system is integrated with ROS2 for deployment on the F1tenth platform:

<div class="mermaid">
flowchart LR
    GYM["🎮 F1tenth Gym / ForzaETH"] <--> BRIDGE["gym_bridge_node"]

    BRIDGE --> SCAN["/scan"]
    BRIDGE --> ODOM["/odom"]

    SCAN --> AGENT["🤖 agent_node"]
    ODOM --> AGENT

    AGENT --> CMD["/cmd_vel"] --> BRIDGE
</div>

This allows seamless transition from simulation to real hardware deployment.

---

## Results

### Stage 1 Performance

<video width="70%" controls>
  <source src="/assets/img/Agent-Mcqueen/f1tenth_stage1.webm" type="video/webm">
  Your browser does not support the video tag.
</video>

| Track Dataset                | Success Rate |
| ---------------------------- | ------------ |
| **F1tenth Racetracks** | 91% (21/23)  |
| **Training Tracks**    | 95%+         |

### Stage 2 Performance

<video width="70%" controls>
  <source src="/assets/img/Agent-Mcqueen/f1tenth_overtake.webm" type="video/webm">
  Your browser does not support the video tag.
</video>

The agent successfully learned aggressive overtaking behavior while maintaining stable driving.

---

## An Interesting Discovery

During my extensive testing in Stage 2, I made an interesting observation in my `render_initial.py` script. I wrote this to render the initial state, allowing me to intuitively check for wall collisions and spawn positions. After getting all the settings right and running it a few times, something remarkable happened: with Agent 0 (frozen) running at 80% speed, Agent 1 (trainable) naturally overtook Agent 0—without any additional training. This gave me confidence that I didn't need to heavily weight overtaking behavior. I simply reduced the opponent information scaling factor even further and proceeded with training.

---

## Lessons Learned

| Challenge             | Solution                             |
| --------------------- | ------------------------------------ |
| MARL non-stationarity | Freeze one agent                     |
| Credit assignment     | Residual learning architecture       |
| Opponent awareness    | Extended observation with Δs, Δvs  |
| Driving stability     | Small opponent info scaling (0.01)   |
| Generalization        | Domain randomization with 450 tracks |

---

## Limitations

While Agent Mcqueen successfully demonstrates competitive racing behavior, several limitations remain:

| Limitation                              | Description                                                                     | Impact                                          |
| --------------------------------------- | ------------------------------------------------------------------------------- | ----------------------------------------------- |
| **Lack of Strategy Diversity**    | Both agents load the same Stage 1 model, resulting in identical base strategies | Homogeneous racing without unexpected variables |
| **Incomplete Stage 2 Evaluation** | No rigorous quantitative evaluation of overtaking improvement                   | Difficult to measure exact performance gains    |
| **Centerline vs Racing Line**     | Training follows centerline rather than optimal racing trajectory               | Suboptimal lap times and cornering              |
| **Exploration over Mastery**      | Training on 450 random tracks instead of mastering specific tracks              | More like exploration than true racing          |

### Missing Racing Elements

Real racing involves far more complexity than what Agent Mcqueen currently handles:

<div class="mermaid">
flowchart LR
    subgraph CURRENT["✅ Current"]
        DRIVE["Basic Driving"]
        OVERTAKE["Simple Overtaking"]
    end

    subgraph MISSING["❌ Not Modeled"]
        ACCEL["Acceleration"]
        BRAKE["Braking"]
        CORNER["Cornering"]
        DEFEND["Defense"]
    end

    CURRENT -.->|"Future Work"| MISSING
</div>

Professional F1 drivers study their tracks until they can drive them blindfolded. Agent Mcqueen, in contrast, encounters new tracks constantly—making it more of an explorer than a racer. The nuanced decision-making that makes racing exciting (when to brake, how to defend a position, optimal corner entry angles) remains beyond the current implementation.

A potential improvement would be combining traditional control algorithms for low-level vehicle dynamics with RL for high-level strategic decisions. This hierarchical approach could enable more sophisticated competitive behavior.

---

## Conclusion

Agent Mcqueen demonstrates that competitive racing AI doesn't require complex MARL frameworks. By combining:

1. **Solid foundation** from Stage 1 solo training
2. **Residual learning** with frozen expert
3. **Carefully designed observations** for opponent awareness
4. **Appropriate reward shaping** for competitive behavior

We can achieve aggressive, dynamic racing behavior that captures the essence of what makes motorsport exciting.

However, this project also revealed how much complexity real racing entails. The current approach produces functional overtaking but lacks the strategic depth that makes professional racing truly compelling. Future iterations could benefit from racing line optimization, track-specific training, and hierarchical control architectures.

All code will be uploaded to GitHub. However, I need some time to clean it up and make it immediately usable for others who want to test it.
