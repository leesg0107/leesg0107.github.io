---
layout: post
title: "The Architecture of Reinforcement Learning: A Structural Guide"
subtitle: "Understanding RL through its fundamental dichotomies and design choices"
tags: [RL, taxonomy, model-free, model-based, on-policy, off-policy, value-based, policy-based]
author: solgyu
category: study
mathjax: true
---

## Introduction

Why am I writing this post about the structural understanding of reinforcement learning? Here's the thing: new algorithms keep emerging constantly, and research never stops. But here's my personal opinion—**truly original ideas that were never discussed before simply can't exist in a vacuum**. New breakthroughs emerge from upgrading existing methods or challenging conventional wisdom. 

Think about it this way: if you understand the historical flow and evolution of ideas, you can quickly grasp any new RL algorithm that comes out. That's why today's topic is crucial. We're going to talk about **the framework of reinforcement learning itself**. Let me explain the RL taxonomy as I understand it.

## 1. Model-Free vs Model-Based

You probably already know how the RL pipeline works—an agent interacts with an environment, receives rewards, and learns. The first fundamental question is: **Does the agent know anything about how the environment works?**

### Model-Based RL

**The agent knows (or learns) the environment dynamics and reward structure.** 

In mathematical terms, the agent has access to:
- **Transition dynamics**: \\(P(s'|s,a)\\) - the probability of reaching state \\(s'\\) when taking action \\(a\\) in state \\(s\\)
- **Reward function**: \\(R(s,a)\\) - the immediate reward for taking action \\(a\\) in state \\(s\\)

Think of it like playing chess with the rulebook. You know exactly what happens when you move each piece. With this knowledge, you can **plan ahead** by simulating future scenarios in your mind before making a move.

**Representative Algorithms**: Value Iteration, Policy Iteration, MCTS (Monte Carlo Tree Search), Dyna-Q, MuZero, AlphaZero

### Model-Free RL

**The agent doesn't know the environment dynamics or reward structure.**

The agent learns purely from **trial and error**—like learning to ride a bike. You don't need to understand the physics of balance and momentum; you just keep trying until you get it right.

**Representative Algorithms**: Q-Learning, SARSA, DQN, PPO, SAC, A3C

### Important Clarification: Prior Data ≠ Model-Based

**Having demonstration data doesn't make an algorithm Model-Based!** These are orthogonal concepts:
- **Model-Based/Free**: Whether you know the environment's transition dynamics
- **Imitation Learning**: Whether you use expert demonstrations (a different dimension entirely)

That's it! Pretty straightforward, right?

## 2. On-Policy vs Off-Policy

Let's think about what a policy actually is. **A policy is like the agent's brain—it decides what actions to take.** Now, there are two types of policies we need to understand:

- **Target Policy** (\\(\pi_{target}\\)): The policy the agent is trying to learn and improve
- **Behavior Policy** (\\(\pi_{behavior}\\)): The policy the agent actually uses to collect data

### On-Policy: Target = Behavior

**The agent learns from its own experiences using the same policy it's trying to improve.**

It's like a chef who tastes their own cooking while preparing a dish. They adjust the recipe based on what they themselves experience.

\\[
\pi_{target} = \pi_{behavior}
\\]

**Representative Algorithms**: SARSA, REINFORCE, PPO, A2C, A3C

### Off-Policy: Target ≠ Behavior

**The agent can learn one policy while following a different policy to collect data.**

Think of it as learning from someone else's mistakes. You watch other drivers (or even your past self) and think, "Hmm, they took that turn, but I know a better way."

\\[
\pi_{target} \neq \pi_{behavior}
\\]

**Representative Algorithms**: Q-Learning, DQN, DDPG, SAC, TD3

## 3. Value-Based vs Policy-Based vs Actor-Critic

Now let's get to the core. In RL, we have two main components:
- **Value Function** (\\(V(s)\\) or \\(Q(s,a)\\)): Evaluates how good a state (or state-action pair) is
- **Policy** (\\(\pi(a|s)\\)): Decides which action to take

### Value-Based: Learning Values, Deriving Actions

**Learn value functions, then derive the policy implicitly from those values.**

The value function \\(Q(s,a)\\) tells you: "If I take action \\(a\\) in state \\(s\\), how much total reward can I expect?" Once you know this, the policy is simple:

\\[
\pi(s) = \arg\max_a Q(s,a)
\\]

Just pick the action with the highest value!

**The Q-function update formula:**

\\[
Q(s,a) \leftarrow Q(s,a) + \alpha \left[ r + \gamma \max_{a'} Q(s',a') - Q(s,a) \right]
\\]

where:
- \\(\alpha\\) is the learning rate
- \\(\gamma\\) is the discount factor
- \\(r\\) is the immediate reward

**Representative Algorithms**: Q-Learning, DQN, Double DQN, Dueling DQN, Rainbow, IQN, C51

### Policy-Based: Learning Actions Directly

**Directly learn the policy \\(\pi_\theta(a|s)\\) without explicitly computing values.**

Instead of asking "How good is this state?" we directly learn "What should I do here?" The policy can be:
- **Stochastic**: \\(\pi_\theta(a|s)\\) outputs a probability distribution over actions
- **Deterministic**: \\(\pi_\theta(s)\\) directly outputs a single action

**The policy gradient theorem:**

\\[
\nabla_\theta J(\theta) = \mathbb{E}_{\pi_\theta} \left[ \nabla_\theta \log \pi_\theta(a|s) \cdot G_t \right]
\\]

where:
- \\(J(\theta)\\) is the expected return
- \\(G_t = \sum_{k=0}^{\infty} \gamma^k r_{t+k}\\) is the return from time \\(t\\)

**Representative Algorithms**: REINFORCE, TRPO (Trust Region Policy Optimization), PPO (pure policy version)

### Actor-Critic: The Best of Both Worlds

**Use two networks: Actor (policy) + Critic (value function).**

Here's a great analogy: imagine an actor performing on stage, and a critic reviewing the performance. The **actor** decides what to do (the policy), while the **critic** evaluates how good those actions were (the value function). The actor improves by listening to the critic's feedback!

\\[
\begin{align}
\text{Actor: } & \pi_\theta(a|s) \\\\
\text{Critic: } & V_\phi(s) \text{ or } Q_\phi(s,a)
\end{align}
\\]

**The advantage function (TD error):**

\\[
A(s,a) = r + \gamma V(s') - V(s)
\\]

This measures: "Was this action better or worse than expected?"

**Representative Algorithms**: A3C, A2C, SAC, TD3, DDPG, PPO (Actor-Critic variant)

## 4. Beyond the Core: Concepts in RL

So far, we've covered the **structural framework**—the blueprint of how RL algorithms are built. But these three axes (Model, Policy, Learning) aren't everything. There's another dimension: **concepts or flavors** that can be added to any algorithm.

Think of it like cooking. The three axes are your main ingredients—meat, vegetables, seasoning. But the **concept** is like the cooking style: Are you grilling? Stir-frying? Making a soup? You could grill chicken (value-based + model-free + off-policy) or make chicken soup (same structure, different approach).

**These concepts enhance learning efficiency, handle specific problem structures, or tackle particular challenges.** Imagine eating barbecue without sauce or salt—technically edible, but not exactly delicious. These concepts are your condiments, making RL practical and powerful.

### Curriculum Learning

Remember learning calculus in school? Imagine if your teacher started with the derivative definition on day one without teaching you addition, subtraction, multiplication, or polynomials first. You'd probably run out of the classroom! 

We learned math gradually: \\(1+1=2\\) → multiplication → polynomials → derivatives → integrals. **Curriculum learning applies the same principle to RL: start with easy tasks, gradually increase difficulty.**

For example, teaching a robot to organize a warehouse:
1. **Easy**: Pick up a single object
2. **Medium**: Lift and carry the object
3. **Hard**: Navigate while carrying
4. **Very Hard**: Sort and organize multiple objects efficiently

\\[
\text{Task Difficulty: } \tau_1 \rightarrow \tau_2 \rightarrow \cdots \rightarrow \tau_n
\\]

**Key idea**: The agent builds foundational skills before tackling complex tasks. This prevents frustration (getting stuck) and speeds up learning dramatically.

**Can be applied to**: Any base algorithm (Curriculum Q-Learning, Curriculum PPO, etc.)

### Meta-Learning (Learning to Learn)

Humans are incredible learners. Once you learn to ride a bicycle, learning to ride a motorcycle is much easier. Once you speak one programming language, picking up another takes days, not years. **Meta-learning teaches agents how to adapt quickly to new tasks based on experience with related tasks.**

Instead of learning a single task, the agent learns a **learning strategy** that works across many tasks:

\\[
\theta^* = \text{Meta-Learner}(\mathcal{T}_1, \mathcal{T}_2, \ldots, \mathcal{T}_n)
\\]

where \\(\theta^*\\) are meta-parameters that can quickly adapt to any new task \\(\mathcal{T}_{new}\\).

**Example**: Train a robot on 100 different manipulation tasks (picking up cups, opening doors, turning knobs). Then when you give it a new task (opening a jar), it can learn it in just a few tries because it has meta-knowledge about manipulation.

**Representative Algorithms**: MAML (Model-Agnostic Meta-Learning), Meta-RL, RL²

**Key idea**: "Learning to learn" creates agents that generalize across problem distributions, not just single problems.

### Hierarchical RL

Some tasks are naturally hierarchical. Think about "making breakfast":
- **High-level**: Decide to make eggs, then toast, then coffee
- **Mid-level**: For eggs—get pan, crack eggs, cook, serve
- **Low-level**: Motor commands—move arm 20cm, rotate wrist 30°, apply force

**Hierarchical RL decomposes complex tasks into subtasks and sub-goals:**

\\[
\pi_{\text{high}}(g|s) \quad \text{and} \quad \pi_{\text{low}}(a|s,g)
\\]

where \\(g\\) is a goal or subgoal, \\(\pi_{\text{high}}\\) chooses goals, and \\(\pi_{\text{low}}\\) executes low-level actions to achieve those goals.

**Representative Algorithms**: Options Framework, Feudal Networks, HAC (Hierarchical Actor-Critic)

### Multi-Agent RL (MARL)

What if multiple agents are learning simultaneously in the same environment? This is **multi-agent RL**—think of it as RL in a social context. I'll cover this in detail in a future post since it's a vast and challenging field with many unique considerations.

**Key Challenges**: 
- **Non-stationarity**: The environment keeps changing as other agents learn
- **Credit assignment**: Which agent contributed to success/failure?
- **Scalability**: How many agents can be effectively trained?
- **Equilibrium selection**: Which equilibrium should be selected? This depends on game-theoretic considerations and reward structure.

**Representative Algorithms**: QMIX, MADDPG (Multi-Agent DDPG), VDN

### Other Important Concepts

**Offline RL (Batch RL)**: Learn from a fixed dataset without environment interaction. Critical for safety-critical domains (healthcare, autonomous driving) where exploration can be dangerous.

**Imitation Learning**: Learn from expert demonstrations. Includes behavioral cloning (supervised learning from demos) and inverse RL (infer the expert's reward function).

**Transfer Learning**: Use knowledge from one task to jumpstart learning on a related task. Like how knowing English helps when learning Spanish.

**Partial Observability (POMDPs)**: The agent doesn't see the full state—like playing poker where you can't see opponents' cards. Requires memory and belief state estimation.

## Algorithm Classification Map

Now we can position all major RL algorithms in this 3D space:

| Algorithm | Model | Policy | Learning |
|-----------|-------|--------|----------|
| **Q-Learning** | Free | Off | Value |
| **SARSA** | Free | On | Value |
| **DQN** | Free | Off | Value |
| **Double DQN** | Free | Off | Value |
| **Dueling DQN** | Free | Off | Value |
| **Rainbow** | Free | Off | Value |
| **IQN** | Free | Off | Value (Distributional) |
| **C51** | Free | Off | Value (Distributional) |
| **REINFORCE** | Free | On | Policy |
| **TRPO** | Free | On | Policy |
| **PPO** | Free | On | Actor-Critic |
| **A2C** | Free | On | Actor-Critic |
| **A3C** | Free | On | Actor-Critic |
| **SAC** | Free | Off | Actor-Critic |
| **TD3** | Free | Off | Actor-Critic |
| **DDPG** | Free | Off | Actor-Critic |
| **QMIX** | Free | Off | Value (Multi-Agent) |
| **VDN** | Free | Off | Value (Multi-Agent) |
| **MADDPG** | Free | Off | Actor-Critic (Multi-Agent) |
| **Value Iteration** | Based | - | Value |
| **Policy Iteration** | Based | - | Value |
| **MCTS** | Based | - | Search + Value |
| **MuZero** | Based | Off | Value (Learned Model) |
| **AlphaZero** | Based | - | Search + Policy |
| **Dyna-Q** | Based | Off | Value (Model + RL) |
| **PILCO** | Based | On | Policy |
| **MBPO** | Based | Off | Actor-Critic (Model-Based) |

## Understanding the Bigger Picture

The beauty of this taxonomy is that **RL research is modular**. The three core axes define the computational structure, and the concepts can be mixed and matched:

\\[
\text{Any Algorithm} = \text{(Model Choice)} \times \text{(Policy Type)} \times \text{(Learning Method)} \times \text{(Optional Concepts)}
\\]

**Examples of combined approaches**:
- **Hierarchical Multi-Agent Curriculum RL with Imitation**: Teaching multiple robots to cooperate on complex tasks using expert demonstrations and progressive difficulty
- **Meta-Learning for Offline Actor-Critic**: Quick adaptation to new tasks using only logged data
- **Model-Based Hierarchical RL with Curriculum**: Planning over abstract goals with learned models, gradually increasing task complexity

The scope of RL is exponentially vast because each dimension addresses different challenges:
- **Core structure**: Computational tractability and convergence
- **Concepts**: Sample efficiency, generalization, scalability, safety

## Conclusion: The Framework is Your Compass

The complexity of reinforcement learning emerges from combinations of these axes and concepts. When you encounter any new RL algorithm (and trust me, new ones come out constantly), ask yourself:

1. **Does it use a model of the environment?** (Model-Based vs Model-Free)
2. **Does it learn from its own actions or can it learn from other policies?** (On-Policy vs Off-Policy)
3. **Does it learn values, policies, or both?** (Value-Based vs Policy-Based vs Actor-Critic)
4. **What additional concepts does it employ?** (Curriculum, Meta-Learning, Hierarchical, etc.)

Answering these questions immediately reveals the algorithm's characteristics, strengths, weaknesses, and where it fits in the grand landscape of RL.

**The future of RL begins with understanding these fundamental principles.** New research will continue to emerge—new architectures, new tricks, new applications. But beneath the surface, they're all answering these same core questions in different ways. Once you internalize this framework, you'll never be lost in the sea of RL algorithms again.

---

## Essential References

1. **Sutton, R. S., & Barto, A. G. (2018).** *Reinforcement Learning: An Introduction* (2nd ed.). MIT Press.

2. **Mnih, V., et al. (2015).** Human-level control through deep reinforcement learning. *Nature*, 518(7540), 529-533.

3. **Schulman, J., et al. (2017).** Proximal Policy Optimization Algorithms. *arXiv preprint arXiv:1707.06347*.

4. **Lillicrap, T. P., et al. (2015).** Continuous control with deep reinforcement learning. *arXiv preprint arXiv:1509.02971*.

5. **Haarnoja, T., et al. (2018).** Soft Actor-Critic: Off-Policy Maximum Entropy Deep Reinforcement Learning with a Stochastic Actor. *ICML*.

6. **Silver, D., et al. (2017).** Mastering the game of Go without human knowledge. *Nature*, 550(7676), 354-359.

---

*Understanding RL's structure is not merely academic curiosity—it's practical wisdom for choosing the right tool for the right job. With this framework, you're now equipped to navigate the ever-evolving landscape of reinforcement learning research.*
