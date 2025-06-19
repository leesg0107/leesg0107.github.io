---
layout: post
title: "The Architecture of Reinforcement Learning: A Structural Guide"
subtitle: "Understanding RL through its fundamental dichotomies and design choices"
tags: [RL, taxonomy, model-free, model-based, on-policy, off-policy, value-based, policy-based]
author: solgyu
category: study
---

## Introduction

When I first started learning reinforcement learning, I was overwhelmed by the sheer number of algorithms: Q-Learning, SARSA, DQN, PPO, SAC, DDPG... It felt like a zoo of methods with no clear organizing principle.

But then I realized that **all of RL can be understood through just a few fundamental design choices**. Every algorithm is simply a different combination of answers to three core questions:

1. **Do we know how the world works?** (Model-Based vs Model-Free)
2. **Do we learn from our current strategy or from any strategy?** (On-Policy vs Off-Policy)  
3. **What do we learn: values or actions?** (Value-Based vs Policy-Based)

This post will guide you through these fundamental dichotomies, showing how they create the entire landscape of reinforcement learning.

## The First Dichotomy: Model-Based vs Model-Free

### The Core Question: Do We Know How the World Works?

Imagine you're learning to drive a car. There are two ways you could approach this:

**Approach 1: Study the Manual First**
- Learn how the steering wheel affects the car's direction
- Understand how the brake pedal relates to stopping distance
- Master the physics of acceleration and turning
- *Then* plan your driving strategy based on this knowledge

**Approach 2: Just Start Driving**
- Get in the car and try different actions
- Learn directly from experience what works and what doesn't
- Gradually improve without ever explicitly modeling the car's mechanics

This is exactly the difference between **Model-Based** and **Model-Free** RL.

### Model-Based RL: "Study the World, Then Act"

**Key Insight**: If we know (or can learn) the transition dynamics P(s'|s,a) and reward function R(s,a), we can **plan** optimal behavior.

```python
# Model-Based Approach
class ModelBasedAgent:
    def __init__(self):
        self.model = WorldModel()  # P(s'|s,a) and R(s,a)
        
    def plan_action(self, state):
        # Use the model to simulate future trajectories
        best_action = None
        best_value = -inf
        
        for action in self.actions:
            # Simulate what happens if we take this action
            next_state = self.model.predict_next_state(state, action)
            reward = self.model.predict_reward(state, action)
            
            # Plan ahead using the model
            future_value = self.plan_ahead(next_state, depth=5)
            total_value = reward + gamma * future_value
            
            if total_value > best_value:
                best_action = action
                best_value = total_value
                
        return best_action
```

**Advantages:**
- **Sample efficient**: Can plan without trial-and-error
- **Interpretable**: We understand why actions are chosen
- **Transferable**: Models can generalize to new scenarios

**Challenges:**
- **Model accuracy**: Wrong models lead to bad policies
- **Complexity**: Real worlds are hard to model accurately

### Model-Free RL: "Learn Directly from Experience"

**Key Insight**: Instead of modeling the world, directly learn what actions lead to good outcomes.

```python
# Model-Free Approach (Q-Learning)
class ModelFreeAgent:
    def __init__(self):
        self.Q = defaultdict(lambda: defaultdict(float))
        
    def update(self, state, action, reward, next_state):
        # Learn directly from experience
        current_q = self.Q[state][action]
        max_next_q = max(self.Q[next_state].values()) if self.Q[next_state] else 0
        
        # Update Q-value based on actual experience
        self.Q[state][action] = current_q + alpha * (
            reward + gamma * max_next_q - current_q
        )
    
    def choose_action(self, state):
        # Choose based on learned Q-values
        if random.random() < epsilon:
            return random.choice(self.actions)
        return max(self.Q[state], key=self.Q[state].get)
```

**Advantages:**
- **Robust**: No model to get wrong
- **General**: Works in any environment
- **Practical**: Don't need to understand the world's mechanics

**Challenges:**
- **Sample hungry**: Learning through trial-and-error is expensive
- **Less interpretable**: Harder to understand the learned behavior

## The Second Dichotomy: On-Policy vs Off-Policy

### The Core Question: Do We Learn from Our Current Strategy?

This distinction is subtle but crucial. It's about **what data we use to learn**.

### On-Policy: "Learn from What We Actually Do"

**Key Insight**: We evaluate and improve the **same policy** that we're currently following.

Think of learning to cook by following a recipe:
- You follow Recipe A to make dinner
- You evaluate how well Recipe A worked
- You improve Recipe A based on this experience
- You use the improved Recipe A for the next meal

```python
# On-Policy Example: SARSA
def sarsa_update(state, action, reward, next_state, next_action):
    """
    Learn from the action we actually took (next_action)
    """
    current_q = Q[state][action]
    next_q = Q[next_state][next_action]  # Use the action we'll actually take
    
    Q[state][action] = current_q + alpha * (
        reward + gamma * next_q - current_q
    )
```

### Off-Policy: "Learn from What We Could Have Done"

**Key Insight**: We can learn about one policy while following a different policy.

Think of learning to cook by watching cooking shows:
- You watch a chef follow Recipe A
- But you evaluate what would happen if you followed Recipe B
- You improve Recipe B based on watching Recipe A
- You can learn about many recipes from watching just one

```python
# Off-Policy Example: Q-Learning
def q_learning_update(state, action, reward, next_state):
    """
    Learn about the optimal policy (max action) 
    regardless of what we actually did
    """
    current_q = Q[state][action]
    max_next_q = max(Q[next_state].values())  # Best possible action, not what we took
    
    Q[state][action] = current_q + alpha * (
        reward + gamma * max_next_q - current_q
    )
```

### Why Does This Matter?

**On-Policy Advantages:**
- **Stable**: We learn exactly about what we're doing
- **Consistent**: No mismatch between learning and acting
- **Safe**: More conservative, less likely to try dangerous actions

**Off-Policy Advantages:**
- **Data efficient**: Can learn from any experience, even old data
- **Flexible**: Can learn multiple policies simultaneously
- **Exploratory**: Can use exploratory data to learn greedy policies

### A Concrete Example

Imagine learning to play tennis:

**On-Policy (SARSA-like):**
- You play with 30% random shots (exploration)
- You evaluate your performance including those random shots
- You improve your strategy that includes 30% randomness
- Result: You learn to be good at "playing with 30% random shots"

**Off-Policy (Q-Learning-like):**
- You play with 30% random shots (exploration)
- But you evaluate what would happen if you played optimally
- You improve your optimal strategy using the exploratory data
- Result: You learn to be good at "playing optimally" using random practice data

## The Third Dichotomy: Value-Based vs Policy-Based

### The Core Question: What Do We Learn?

This is about the **representation** we use to capture our knowledge.

### Value-Based: "Learn How Good Each Action Is"

**Philosophy**: If we know the value of every action in every state, we can just pick the best one.

```python
# Value-Based Approach
class ValueBasedAgent:
    def __init__(self):
        self.Q = {}  # Q(s,a) = expected return from taking action a in state s
    
    def choose_action(self, state):
        # Policy is implicit: pick the action with highest value
        return max(self.actions, key=lambda a: self.Q.get((state, a), 0))
    
    def learn(self, state, action, reward, next_state):
        # Learn the value function
        target = reward + gamma * max(self.Q.get((next_state, a), 0) 
                                     for a in self.actions)
        self.Q[(state, action)] += alpha * (target - self.Q.get((state, action), 0))
```

**Intuition**: Like having a GPS that tells you the "goodness score" of every possible route. You always pick the route with the highest score.

### Policy-Based: "Learn What Action to Take Directly"

**Philosophy**: Skip the middleman. Directly learn a policy π(a|s) that tells us what to do.

```python
# Policy-Based Approach
class PolicyBasedAgent:
    def __init__(self):
        self.policy = NeuralNetwork()  # π(a|s) = probability of action a in state s
    
    def choose_action(self, state):
        # Policy is explicit: sample from the learned distribution
        action_probs = self.policy.forward(state)
        return np.random.choice(self.actions, p=action_probs)
    
    def learn(self, trajectory):
        # Learn the policy directly using policy gradients
        for state, action, reward in trajectory:
            # Increase probability of good actions, decrease bad ones
            grad = self.policy.gradient(state, action)
            self.policy.parameters += alpha * reward * grad
```

**Intuition**: Like having a driving instructor in your head who directly tells you "turn left here, slow down there" without explaining why.

### Actor-Critic: "Why Not Both?"

The best of both worlds: learn both values and policies.

```python
# Actor-Critic Approach
class ActorCriticAgent:
    def __init__(self):
        self.actor = PolicyNetwork()   # Learns what to do
        self.critic = ValueNetwork()   # Learns how good it is
    
    def choose_action(self, state):
        return self.actor.sample_action(state)
    
    def learn(self, state, action, reward, next_state):
        # Critic: learn value function
        value_target = reward + gamma * self.critic.value(next_state)
        td_error = value_target - self.critic.value(state)
        self.critic.update(state, value_target)
        
        # Actor: learn policy using critic's assessment
        self.actor.update(state, action, td_error)
```

**Intuition**: Like having both a GPS (critic) that evaluates routes AND a driving instructor (actor) that tells you what to do, where the instructor learns from the GPS's feedback.

## Putting It All Together: The RL Algorithm Map

Every RL algorithm can be located in this 3D space:

```
Model-Free + On-Policy + Value-Based  → SARSA
Model-Free + Off-Policy + Value-Based → Q-Learning, DQN
Model-Free + On-Policy + Policy-Based → REINFORCE, PPO
Model-Free + Off-Policy + Policy-Based → DDPG (deterministic)
Model-Free + On-Policy + Actor-Critic → A3C, A2C
Model-Free + Off-Policy + Actor-Critic → SAC, TD3

Model-Based + Value-Based → Value Iteration, Policy Iteration
Model-Based + Policy-Based → Model Predictive Control
Model-Based + Actor-Critic → Model-Based Actor-Critic (MBAC)
```

## Practical Implications: When to Use What?

### Choose Model-Based When:
- You can model the environment accurately (games, simulations)
- Sample efficiency is critical (expensive real-world interactions)
- You need interpretability (robotics, safety-critical applications)

### Choose Model-Free When:
- The environment is too complex to model (real-world, high-dimensional)
- You have lots of interaction data available
- Robustness is more important than sample efficiency

### Choose On-Policy When:
- Stability is important
- You're in safety-critical domains
- Your policy needs to be consistent with your training data

### Choose Off-Policy When:
- Sample efficiency matters
- You want to reuse old data
- You need to learn from demonstrations or sub-optimal data

### Choose Value-Based When:
- Discrete action spaces
- You want deterministic policies
- Simplicity is preferred

### Choose Policy-Based When:
- Continuous action spaces
- You need stochastic policies
- The action space is very large

### Choose Actor-Critic When:
- You want the benefits of both approaches
- You're dealing with continuous control
- You need both policy and value estimates

## Conclusion: The Beauty of Structure

What I find beautiful about this framework is how it brings order to the apparent chaos of RL algorithms. Every method is just a different answer to these fundamental questions.

When you encounter a new RL algorithm, ask yourself:
1. **Does it learn a model of the world?**
2. **Does it learn from its current policy or from any policy?**
3. **Does it learn values, policies, or both?**

These questions will immediately tell you where the algorithm fits in the landscape and what its strengths and weaknesses are likely to be.

The future of RL isn't just about inventing new algorithms—it's about understanding these fundamental trade-offs and making informed choices based on your specific problem's requirements.

---

## Essential References

1. **Sutton, R. S., & Barto, A. G. (2018).** *Reinforcement Learning: An Introduction*. MIT Press.
   - Chapters 4-6 for model-based methods, Chapter 7 for on/off-policy distinction

2. **Schulman, J., et al. (2017).** Proximal Policy Optimization Algorithms. *arXiv preprint*.
   - Excellent example of on-policy, model-free, policy-based method

3. **Lillicrap, T. P., et al. (2015).** Continuous control with deep reinforcement learning. *arXiv preprint*.
   - DDPG as an example of off-policy, model-free, actor-critic method

4. **Deisenroth, M., Neumann, G., & Peters, J. (2013).** A survey on policy search for robotics. *Foundations and Trends in Robotics*.
   - Comprehensive survey covering the policy-based perspective

---

*Understanding RL's structure isn't just academic—it's practical wisdom that will guide your algorithmic choices and help you navigate this rich and complex field.* 