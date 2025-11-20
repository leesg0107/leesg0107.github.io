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

When first encountering reinforcement learning, you're faced with a seemingly chaotic array of algorithms: Q-Learning, SARSA, DQN, PPO, SAC, DDPG... It appears to be an unorganized collection of methods. However, **every RL algorithm is simply a different combination of answers to three core questions**:

1. **Do we know the environment's dynamics?** (Model-Based vs Model-Free)
2. **Are the target policy and behavior policy the same?** (On-Policy vs Off-Policy)
3. **What do we learn?** (Value-Based vs Policy-Based vs Actor-Critic)

Understanding these three axes allows you to immediately classify and understand the characteristics of any RL algorithm.

## 1. Model-Based vs Model-Free

### Core Question: Do we know the environment's dynamics?

- Do we know or learn the **state transition function** \\(P(s'|s,a)\\)?
- Do we explicitly model the **reward function** \\(R(s,a)\\)?

### Model-Based RL

**Core Concept**: Know or learn an environment model, then determine optimal actions through **planning**

```python
# Core of Model-Based approach
def plan_with_model(state, model, horizon):
    best_action = None
    best_value = -infinity
  
    for action in actions:
        # Predict future using the model
        next_state = model.predict_transition(state, action)
        reward = model.predict_reward(state, action)
        future_value = plan_recursively(next_state, horizon-1)
      
        total_value = reward + gamma * future_value
        if total_value > best_value:
            best_action = action
          
    return best_action
```

**Representative Algorithms**: Value Iteration, Policy Iteration, MCTS, Dyna-Q, MuZero

**Characteristics**:

- ✅ **Sample efficient**: Can generate many experiences through simulation
- ✅ **Interpretable**: Can understand why actions are chosen
- ❌ **Model bias**: Wrong models lead to wrong policies

### Model-Free RL

**Core Concept**: Learn directly from **experience** without an environment model

```python
# Core of Model-Free approach
def learn_from_experience(state, action, reward, next_state):
    # Direct Q-value update without a model
    current_q = Q[state][action]
    target = reward + gamma * max(Q[next_state].values())
    Q[state][action] += alpha * (target - current_q)
```

**Representative Algorithms**: Q-Learning, SARSA, DQN, PPO, SAC, A3C

**Characteristics**:

- ✅ **Robust**: No model errors
- ✅ **General**: Applicable even in complex environments
- ❌ **Sample hungry**: Requires much interaction data

### Important Note: Misconception about Prior Data

**Having demonstration data does not make it Model-Based.** These are separate concepts:

- **Model-Based/Free**: Whether we know the environment's transition dynamics
- **Demonstration learning**: Whether we utilize expert data (Imitation Learning domain)

## 2. On-Policy vs Off-Policy

### Core Question: Are Target Policy and Behavior Policy the same?

- **Target Policy** (\\(\pi_{target}\\)): The policy we want to evaluate and improve
- **Behavior Policy** (\\(\pi_{behavior}\\)): The policy that actually collects data

### On-Policy: Target Policy = Behavior Policy

**Core Concept**: Learn only about the policy we are currently following

```python
# SARSA (On-Policy)
def sarsa_update(s, a, r, s_next, a_next):
    # Learn using the action we actually took (a_next)
    target = r + gamma * Q[s_next][a_next]  # Use actual action
    Q[s][a] += alpha * (target - Q[s][a])
```

**Representative Algorithms**: SARSA, REINFORCE, PPO, A3C

**Characteristics**:

- ✅ **Stability**: Stable due to alignment between learning and behavior policies
- ✅ **Conservative**: Safe improvement near current policy
- ❌ **Data inefficient**: Can only use data from current policy

### Off-Policy: Target Policy ≠ Behavior Policy

**Core Concept**: Learn a target policy from data collected by a different policy

```python
# Q-Learning (Off-Policy)
def q_learning_update(s, a, r, s_next):
    # Learn using optimal action (regardless of actual action)
    target = r + gamma * max(Q[s_next].values())  # Use optimal action
    Q[s][a] += alpha * (target - Q[s][a])
```

**Representative Algorithms**: Q-Learning, DQN, DDPG, SAC, TD3

**Characteristics**:

- ✅ **Data efficient**: Can reuse past data and data from other policies
- ✅ **Flexibility**: Learn optimal policy from exploratory behavior
- ❌ **Instability**: Increased variance due to target-behavior policy mismatch

## 3. Value-Based vs Policy-Based vs Actor-Critic

### Core Question: What do we directly learn?

### Value-Based: Learning Value Functions

**Core Concept**: Learn \\(Q(s,a)\\) or \\(V(s)\\) and **implicitly** derive policy from them

\\[
\pi(s) = \arg\max_a Q(s,a)
\\]

```python
class ValueBasedAgent:
    def __init__(self):
        self.Q = defaultdict(float)
  
    def act(self, state):
        # Policy implicitly derived from Q-values
        return max(actions, key=lambda a: self.Q[(state, a)])
```

**Representative Algorithms**: Q-Learning, DQN, Double DQN

**Characteristics**:

- ✅ **Discrete actions**: Natural application to discrete action spaces
- ✅ **Deterministic**: Clear optimal action selection
- ❌ **Continuous actions**: Inefficient in continuous action spaces

### Policy-Based: Direct Policy Learning

**Core Concept**: **Explicitly** learn \\(\pi(a|s)\\)

\\[
\pi_\theta(a|s) = \text{probability distribution or deterministic function}
\\]

```python
class PolicyBasedAgent:
    def __init__(self):
        self.policy_network = NeuralNetwork()
  
    def act(self, state):
        # Direct action sampling from policy
        action_probs = self.policy_network(state)
        return sample_from_distribution(action_probs)
```

**Representative Algorithms**: REINFORCE, TRPO

**Characteristics**:

- ✅ **Continuous actions**: Natural application to continuous action spaces
- ✅ **Stochastic policies**: Can represent complex policies
- ❌ **High variance**: High variance in policy gradients

### Actor-Critic: Combining Both Approaches

**Core Concept**: **Simultaneously** learn both policy (Actor) and value function (Critic)

```python
class ActorCriticAgent:
    def __init__(self):
        self.actor = PolicyNetwork()    # π(a|s)
        self.critic = ValueNetwork()    # V(s) or Q(s,a)
  
    def learn(self, state, action, reward, next_state):
        # Critic: Learn value function
        td_error = reward + gamma * self.critic(next_state) - self.critic(state)
      
        # Actor: Improve policy using TD error
        self.actor.update(state, action, td_error)
        self.critic.update(state, reward + gamma * self.critic(next_state))
```

**Representative Algorithms**: A3C, A2C, SAC, TD3, PPO (Actor-Critic variant)

**Characteristics**:

- ✅ **Low variance**: Critic acts as baseline to reduce variance
- ✅ **Efficiency**: Utilizes both value and policy information
- ❌ **Complexity**: Complexity of simultaneously learning two networks

## Algorithm Classification Map

Now we can position all RL algorithms in this 3D space:

| Algorithm                  | Model | Policy | Learning     |
| -------------------------- | ----- | ------ | ------------ |
| **Q-Learning**       | Free  | Off    | Value        |
| **SARSA**            | Free  | On     | Value        |
| **DQN**              | Free  | Off    | Value        |
| **Double DQN**       | Free  | Off    | Value        |
| **Dueling DQN**      | Free  | Off    | Value        |
| **Rainbow**          | Free  | Off    | Value        |
| **REINFORCE**        | Free  | On     | Policy       |
| **TRPO**             | Free  | On     | Policy       |
| **PPO**              | Free  | On     | Actor-Critic |
| **A2C**              | Free  | On     | Actor-Critic |
| **A3C**              | Free  | On     | Actor-Critic |
| **SAC**              | Free  | Off    | Actor-Critic |
| **TD3**              | Free  | Off    | Actor-Critic |
| **DDPG**             | Free  | Off    | Actor-Critic |
| **IQN**              | Free  | Off    | Value        |
| **C51**              | Free  | Off    | Value        |
| **QMIX**             | Free  | Off    | Value        |
| **VDN**              | Free  | Off    | Value        |
| **MADDPG**           | Free  | Off    | Actor-Critic |
| **Value Iteration**  | Based | -      | Value        |
| **Policy Iteration** | Based | -      | Value        |
| **MCTS**             | Based | -      | Value        |
| **MuZero**           | Based | -      | Value        |
| **AlphaZero**        | Based | -      | Value        |
| **Dyna-Q**           | Based | Off    | Value        |
| **PILCO**            | Based | On     | Policy       |
| **MB-MPO**           | Based | Off    | Actor-Critic |

## Beyond the Basics: Orthogonal Dimensions

The three fundamental axes we discussed define the **core algorithmic structure** of RL methods. However, these are just the foundation. In practice, RL research extends into multiple **orthogonal dimensions** that address different aspects of learning and problem-solving.

### Understanding the Distinction

**Core Algorithm Structure (What we covered):**

- How does the algorithm learn? (Model-Based/Free, On/Off-Policy, Value/Policy/Actor-Critic)
- These define the fundamental computational approach

**Orthogonal Extensions (Beyond core algorithms):**

- What problem are we solving?
- How do we structure the learning process?
- What data sources do we use?

### Learning Strategy Dimension

These approaches modify **how** we apply any core algorithm:

- **Curriculum Learning**: Progressive task difficulty (Easy → Hard)
  - Can be applied to any algorithm: Curriculum Q-Learning, Curriculum PPO, etc.
- **Self-Play**: Learning through self-competition
  - Algorithm-agnostic: Self-Play DQN, Self-Play PPO
- **Meta-Learning**: Learning to learn across multiple tasks
  - Applies to any base algorithm: Model-Agnostic Meta-Learning (MAML)

```python
# Example: Curriculum Learning is orthogonal to base algorithm
class CurriculumWrapper:
    def __init__(self, base_algorithm, curriculum):
        self.base_algo = base_algorithm  # Could be DQN, PPO, SAC, etc.
        self.curriculum = curriculum
  
    def train(self):
        for task in self.curriculum.get_progression():
            self.base_algo.train_on_task(task)
```

### Problem Structure Dimension

These address **what type of problem** we're solving:

- **Hierarchical RL**: Multi-level decision making
  - Temporal abstraction (Options, Goal-conditioned RL)
- **Multi-Agent RL**: Multiple interacting agents
  - Coordination, competition, communication
- **Partial Observability**: Limited state information (POMDPs)
  - Belief states, memory architectures

### Data Source Dimension

These determine **what data** we learn from:

- **Imitation Learning**: Learning from expert demonstrations
  - Behavioral Cloning, Inverse RL, GAIL
- **Offline RL**: Learning from fixed datasets
  - Conservative Q-Learning, AWR, CQL
- **Transfer Learning**: Leveraging knowledge from related tasks
  - Domain adaptation, few-shot learning

### The Exponential Scope of RL

This orthogonal structure means RL's scope is **exponentially vast**:

```
Core Algorithms × Learning Strategies × Problem Structures × Data Sources
     = Enormous problem space
```

**Examples of Combined Approaches:**

- Hierarchical Multi-Agent Curriculum Learning with Imitation
- Meta-Learning for Few-Shot Offline RL
- Self-Play in Partially Observable Multi-Agent Environments

### Why This Matters

Each dimension addresses different challenges:

- **Core algorithms**: Computational efficiency and convergence
- **Learning strategies**: Sample efficiency and stability
- **Problem structures**: Scalability and generalization
- **Data sources**: Practical applicability and safety

The beauty of RL lies in this modularity - researchers can mix and match components from different dimensions to tackle increasingly complex real-world problems, from robotics and autonomous driving to game AI and financial trading.

## Conclusion

The complexity of reinforcement learning emerges from combinations of these three axes. When encountering a new algorithm, ask:

1. **Does it use a model?**
2. **What policy's data is used to learn which policy?**
3. **Does it directly learn values or policies?**

Answering these questions immediately reveals the algorithm's characteristics and applicable situations. The future of RL begins with understanding these fundamental principles and making informed choices based on your specific problem requirements.

---

## Essential References

1. **Sutton, R. S., & Barto, A. G. (2018).** *Reinforcement Learning: An Introduction*. MIT Press.
2. **Schulman, J., et al. (2017).** Proximal Policy Optimization Algorithms. *arXiv preprint*.
3. **Lillicrap, T. P., et al. (2015).** Continuous control with deep reinforcement learning. *arXiv preprint*.
4. **Mnih, V., et al. (2015).** Human-level control through deep reinforcement learning. *Nature*.

---

*Understanding RL's structure is not merely academic curiosity. It is practical wisdom for selecting the right algorithms and effectively solving problems.*
