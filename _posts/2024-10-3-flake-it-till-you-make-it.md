---
layout: post
title: Heterogeneous Multi-Robot Reinforcement Learning 
subtitle: model structure study#1 
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/GPPO_HetGPPPO.png
share-img: /assets/img/path.jpg
tags: [MARL,GNN]
author: Matteo Bettini, Ajay Shankar and Amanda Prorok 
---

### Before diving into the paper  

기존의 MARL frameworks에서는 제대로 된 heterogeneous policy들을 이용하는 능력이 부족할 뿐만 아니라 공유 파라미터를 사용하기에 decentralized agents을 만드는데에 한계가 있었습니다. 허나 저자는 제안하는 **HetGPPO**는 부분 관찰 가능한 환경에서의 한계를 극복하는 동시에 완전히 decentralized 학습을 가능하게 합니다. 

HetGPPO는 이 논문을 통해 다음 2 가지 결과를 보여줍니다:
1. 강한 heterogeneous 요구사항으로 homogeneous 방법이 실패할 시 HetGPPO는 성공하는 결과를 보여줍니다. 
2. homogeneous 방법이 heterogeneous한 행동을 학습하더라도, HetGPPO가 더 높은 성능을 보여줍니다.  

본격적으로 들어가기 전 우리는 확실히 정리해야 할 것이 있습니다. heterogeneous system이라는 것이 서로 다른 행동 혹은 특성을 가진 agents들이 이루는 시스템을 의미하는데, 서로 행동만 다르게 할 수도 있고, 아예 다른 종류의 agents들일 수도 있기 때문입니다. 

### Classification of Heterogeneous systems

논문에서는 두 가지 분류 기준을 제시합니다. P(physical)과 B(behavioral)입니다:

- P가 의미하는 것은 애초에 agent or robot의 종류 자체가 다르다는 것입니다. 예를 들면 드론과 사족보행 로봇, 수중 드론들이 이루는 시스템입니다. 
- B에서는 두 가지로 나뉘게 되는데 B<sub>s</sub>와 B<sub>d</sub>으로 나뉘게 됩니다. 복잡할 것 없습니다. 여기서 s,d는 각각 same, different을 의미합니다. 
  - B<sub>s</sub>은 같은 objective function을 위해 행동을 하겠다는 것입니다. 즉 같은 reward fuction을 사용한다는 것을 의미합니다. 
  - B<sub>d</sub>은 다른 objective function을 가지며, 다른 reward function을 사용합니다. 그렇기에 B<sub>d</sub>은 적대적 세팅이나 비협력적인 환경에서 쓰이기도 하지만, 다른 sub-function을 가지게 할 수도 있습니다. 이는 이 세팅을 어떻게 하냐에 따라 계층적 강화학습처럼 쓸 수 있다는 점을 시사합니다.

예를 들면 여러 같은 종류의 드론들이 있는데, 산불 구조 작전에 투입되었습니다. 몇 드론들은 그 안에 아직 남아있는 사람들이 있는지 확인하는 것이 임무이며, 나머지 드론들은 산불의 규모와 진행 방향을 조사하는 것으로 임무를 내리게 할 수 있습니다.(그냥 예시입니다.) 이제 이 요소들을 활용하여 heterogenous system을 분류해보겠습니다. 

1. P\B
    물리적인 요소만 다른 경우. 이 같은 경우에는 센서나 하드웨어가 다른 로봇들이 동일한 목표를 위해 움직입니다. 예를 들면 서로 다른 능력의 로봇들이 한 영역 탐색을 위해 같은 행동 정책을 받아 사용합니다. 
    -> 제 개인적으로는 1번은 별로인 것이 물리적 차이가 있음에도 같은 행동 정책을 사용한다는 것은 무리가 있다고 생각합니다. 

2. P ∩ B<sub>d</sub>
    모든 것이 다른 경우입니다. 가장 복잡한 형태이며, 서로 다른 로봇들이 서로 다른 목표를 위해 행동합니다. 

3. P ∩ B<sub>s</sub>
    하드웨어적으로는 다르지만, 같은 목표를 위해 행동하는 경우입니다. 예를 들면 서로 다른 능력을 가진 로봇들이 한 영역을 탐색하는 경우입니다. 

4. B<sub>s</sub>\P
    같은 로봇들이지만 같은 목표를 위해 행동합니다. 여러 대의 동일한 드론들이 나누어, 빠르고 넓게 탐색하고 & 느리게 자세히 탐색하는 경우입니다. 

5. B<sub>d</sub>\P
    같은 로봇들이 서로 다른 목표를 위해 행동합니다. 처음 언급한 산불 구조 작전의 경우입니다. 

-> 주의할 점은 같은 목표를 위해 행동한다는 것이 같은 행동을 한다는 것이 아닌, 서로 다른 행동을 한다는 것을 전제로 합니다.  

이제 어떻게 이것들을 가능하게 했는지 알아보도록 하겠습니다. 또한 저자가 당부한 것은 이 분류 기준이 이질성의 척도를 측정하려 한 것은 아니며, dynamic P (다른 배터리 잔량이나 하드웨어 노후화) 은 고려하지 않았다는 것입니다. 

이제 대략 어떤 종류의 문제들이 있는지 확인해보았습니다. 이제 본격적으로 파고 들어가보도록 하겠습니다. 

### POMDP(Partially Observable Markov Decision Process) + Communication Graph 

우리가 길을 걸을 때 모든 위험 변수들에 대해 계산하면서 걷지는 않습니다. 불가능이죠. 그 이유는 당연하게도 우리는 모든 것을 파악할 능력을 가지고 있지 않기 때문입니다. 당연한 말이죠. 이러한 이유로 인해 우리는 환경에 대해 부분적으로 관찰할 수 밖에 없습니다. 여러 사람들이 모여도 이 사실은 변하지 않습니다. 이것은 로봇들에게도 적용됩니다. 그래서 이러한 환경을 부분 관찰가능한 MDP, POMDP라고 부릅니다.(이 개념은 RL에서 굉장히 중요한 개념이기에 추후 포스트에서 따로 다루겠습니다.)

이것은 여러 변수들로 이루어진 tuple로 정의되는데:
(V, S, O, {σ<sub>i</sub>}<sub>i∈V</sub>, A, {R<sub>i</sub>}<sub>i∈V</sub>, T, γ)

- V: agents의 수 
- S: state space 
- O: observation space 
- {σ<sub>i</sub>}<sub>i∈V</sub>: the observation of agent i 
- A: action space 
- {R<sub>i</sub>}<sub>i∈V</sub>: the reward of agent i ,R<sub>i</sub>: SxAxS -> R
- T: state transition function, T:SxAxS -> [0,1] 
- γ: discount factor 

여기까지는 POMDP를 조금 알고 계시는 분들은 모두 아시는 내용이지만, 저자는 communication graph G=(V,E) 를 추가로 정의합니다. V는 agent들의 집합이며, E는 edge를 의미하는데, 이는 communication link를 의미합니다. 이것의 집합은 최대 agent 소통 범위를 의미하며, 시간에 따라 변합니다. 

이제 저자가 제안하는 두 가지 model들에 대해 살펴볼 차례입니다.  

![GPPO vs HetGPPO](/assets/img/GPPO_HetGPPPO.png)

### 1. GPPO(Graph Neural Network Proximal policy Optimization) 

우선 GPPO는 IPPO(independent proximal policy optimization)에서 발전된 것입니다. IPPO에서 각각의 agent는 본인의 critic과 actor를 가지며 본인의 observation에 근거하여 계산을 하게 됩니다. 이것의 문제는 다른 agents는 본인과는 전혀 상관이 없기에 환경의 일부로 여기게 되며, 각 agent들의 정책이 변화되어 환경이 변한다면 학습의 안전성에 방해를 받습니다. 

장점이라면 공통의 정보가 필요없기에 모두 관찰 가능한 환경에서 꽤 두각을 나타내었습니다. GPPO는 이러한 IPPO의 장점은 계승하며, 단점은 극복한 모델입니다. 이것은 GNN communicaiton layer를 통해 이웃 간 agents이 정보를 공유하며 부분 관찰성을 완화시킴으로 단점은 극복하게 됩니다. 

GPPO critic V<sub>i</sub>(o<sub>N<sub>i</sub></sub>) 와 actor 𝝅<sub>i</sub>(o<sub>N<sub>i</sub></sub>) 는 이웃 observation o<sub>N<sub>i</sub></sub> 을 반영합니다. 이는 IPPO에서 다른 agents들을 전혀 고려하지 않았다는 점에 비해 학습을 안전하게 만듭니다. 방 안에서 좀비 놀이를 하는데, 눈 가린 한 사람만 술래를 하면 잡는데 오래 걸리지만, 여러 사람들이 눈을 가리면 찾는다면 서로 "여기있다" 식의 소통을 통해 더 효율적으로 사람을 잡을 수 있다는 부분인 것 같습니다. agent i는 관찰을 통해 observation을 얻는데, 여기에는 절대적 특징을 포함합니다. 그것은 위치와 속도입니다. 그밖에 나머지 부붙들은 비절대적 특징으로 포함되고 것들은 z<sub>i</sub>으로 임베딩되어 a MLP(Multi Layer Perceptron) encoder로 넘겨집니다. 절대적 특징인 위치 p<sub>i</sub> 와 속도 v<sub>i</sub>는 edge 계산에 쓰이며, 다른 agent j가 있다고 했을 때, 
p<sub>ij</sub>=p<sub>i</sub>-p<sub>j</sub>, v<sub>ij</sub>=v<sub>i</sub>-v<sub>j</sub>으로 상대적 위치, 속도로 계산되어 edge feature 
e<sub>ij</sub>=p<sub>ij</sub>||v<sub>ij</sub>이 됩니다. 

이렇게 계산이 된 e<sub>ij</sub> 와 z<sub>i</sub>는 message-passing GNN kernel에 쓰이게 됩니다.
message-passing GNN kernel:<br>
h<sub>i</sub> = 𝛙<sub>𝜃<sub>i</sub></sub>(z<sub>i</sub>) + ⨁<sub>j∈N<sub>i</sub></sub> 𝛟<sub>𝜃<sub>i</sub></sub>(z<sub>j</sub> || e<sub>ij</sub>)

여기서 𝛙<sub>𝜃<sub>i</sub></sub> 와 𝛟<sub>𝜃<sub>i</sub></sub> 는 두 개의 MLP들이며, ⨁ 는 집계 연산(sum)입니다. 여기서는 이웃 노드들로부터 오는 정보를 결합하는데에 쓰입니다. 위의 복잡해보이는 식을 해석하자면 GNN output인 h<sub>i</sub>이 두 개의 MLP decoder들로 보내지게 됩니다.
𝜃<sub>i</sub>는 파라미터인데, 모든 agents의 이 𝜃<sub>i</sub>값이 동일합니다. 이런 parameter sharing을 통해 샘플 효율성을 증가시키는 효과를 불러오고 IPPO의 정보 폐쇄성을 극복합니다.
여기서 아쉬운 점이 발생하는데, 동일한 𝜃<sub>i</sub>들을 사용한다는 것은 이 학습은 centralized하다는 점이 발생합니다. 또한 agents들이 같아진다는 즉 homogeneous하게 변한다는 점이 있습니다. 이제 HetGPPO를 알아봅시다.

### 2. HetGPPO(Heterogeneous Graph Neural Network Proximal policy Optimization)
HetGPPO에서는 이러한 parameter sharing을 과감히 제거합니다. 이로써 순열 등가성, permutation equivariance가 사라집니다.
순열 등가성이란 입력 노드의 순서를 바꿔도 출력 결과가 이에 상응하여 바뀌는 속성을 뜻합니다. 즉 그래프의 순서는 중요하지 않다! 를 의미합니다. GPPO의 그림를 보면 동일한 파라미터를 쓰기에 입력 순서에 상관없이 결과는 같게 됩니다. 하지만 HetGPPO에서는 서로 다른 parameter들을 쓰니 이 순열 등가성은 사라지게 되는 것입니다.
이제 agents는 다른 메세지 encoding과 해석법을 학습합니다. 이것은 확실한 단점이 있습니다. 서로 다른 parameter들을 씀으로써 일반화의 능력과 샘플 효율성이 떨어집니다. 그럼에도 이 단점을 능가하는 장점들이 있습니다.
이제는 global 정보 없이, agent들 간 통신만을 사용하여 학습이 가능해집니다. 또한 이웃을 통해 gradient가 backpropagation이 되어 주변과의 상호작용으로 학습합니다. 이로써 HetGPPO는 Decentralized Training, Decentralized Execution을 달성하게 됩니다. 


### 결론 
나머지 실험 결과나 내용들은 논문을 통해 확인해보시면 좋을 것 같습니다 :D 감사합니다. 

### Reference

[1] Bettini, M., Shankar, A., & Prorok, A. (2023). Heterogeneous Multi-Robot Reinforcement Learning. 

논문 링크: [https://arxiv.org/abs/2301.07137](https://arxiv.org/abs/2301.07137)