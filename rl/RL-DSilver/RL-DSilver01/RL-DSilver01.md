# Introduction to RL
This is an abstract (basically a reduction of David Silver's Presentation)
http://www0.cs.ucl.ac.uk/staff/d.silver/web/Teaching_files/intro_RL.pdf


# The RL Problem

## Reward
- A reward $R_t$ is a scalar feedback signal
- Indicates how well agent is doing at step $t$
- The agent’s job is to maximise cumulative reward
- **Reward Hypothesis**: All goals can be described by the maximisation of expected cumulative reward

### SDM: Sequential Decision Making
- Goal: select actions to maximise total future reward
- Actions may have long term consequences
- Reward may be delayed
- It may be better to sacrifice immediate reward to gain more long-term reward

## Environments
### Agent and Environment
- At each step $t$ the agent:
  + Executes action $A_t$
  + Receives observation $O_t$
  + Receives scalar reward $R_t$
- The environment:
  + Receives action $A_t$
  + Emits observation $O_{t+1}$
  + Emits scalar reward $R_{t+1}$
- $t$ increments at env. step


## State
### History and State
- **History**: sequence of observations, actions, rewards
  $$ H_t = O_1, R_1, A_1, ..., A_{t-1}, O_t, R_t $$
- i.e.  all observable variables up to time $t$
- i.e.  the sensorimotor stream of a robot or embodied agent
- What happens next depends on the history:
  + The agent selects actions
  + The environment selects observations/rewards
- **State**: the information used to determine what happens next
- Formally, state is a function of the history:
  $$ S_t = f(H_t) $$

### Environment State
- The **environment state** $S^e_t$  is the environment’s private representation
- i.e.  whatever data the environment uses to pick the next observation/reward
- The environment state is not usually visible to the agent
- Even if $S^e_t$ is visible, it may contain irrelevant information

### Agent State
- $S^a_t$ is the agent’s internal representation
- i.e.  whatever information the agent uses to pick the next action
- i.e.  it is the information used by reinforcement learning algorithms
- It can be any function of history:
  $$ S^a_t = f(H_t) $$
  
### Information State
- **Information state** (a.k.a.  **Markov state**) contains all useful information from the history.
- A state $S_t$ is Markov if and only if:
  `$$ P[S_t | S_{t-1}] = P[S_t | S_1, ..., S_{t-1}] $$`
- “The future is independent of the past given the present”
  $$ H_{1:t} → S_t → H_{t+1:∞}$$
- Once the state is known, the history may be thrown away
- i.e.  The state is a sufficient statistic of the future
- The environment state $S^e_t$ is Markov
- The history $H_t$ is Markov
  
### Fully Observable Environments
- **Full observability**:  agent **directly** observes environment state
  $$ O_t = S^a_t = S^e_t $$
- Agent state = environment state = information state
- Formally, this is a **Markov decision process** (**MDP**)

### Partially Observable Environments
- **Partial observability**:  agent **indirectly** observes environment:
  + A robot with camera vision isn’t told its absolute location
  + A trading agent only observes current prices
  + A poker playing agent only observes public cards
- Now agent state ≠ environment state
- Formally this is a **partially observable Markov decision process** (**POMDP**)
- Agent must construct its own state representation $S^a_t$, e.g.
  + Complete history: $S^a_t = H_t$
  + **Beliefs** of environment state: $S^a_t = ( P[S^e_t = s^1], ..., P[S^e_t = s^n] )$
  + Recurrent neural network: $S^a_t = \sigma( S^a_{t-1} W_s + O_t W_o )$
  
  
# Inside a RL agent
## Major Components of an RL Agent
- An RL agent may include one or more of these components:
  + Policy:  agent’s behaviour function
  + Value function:  how good is each state and/or action
  + Model:  agent’s representation of the environment
  
### Policy
- A **policy** is the agent’s behaviour
- It is a map from state to action, e.g.
- Deterministic policy: $a = \pi(s) $
- Stochastic policy: $ \pi(a|s) = P[A_t=a | S_t=s] $

### Value Function
- **Value function** is a prediction of future reward
- Used to evaluate the goodness/badness of states
- And therefore to select between actions, e.g.:
  `$$ v_{\pi}(s) = E_{\pi}[  R_{t+1} + \gamma R_{t+2} + \gamma^2 R_{t+3} + ... | S_t = s] $$`
  
### Model
- A **model** predicts what the environment will do next
- $\mathcal{P}$ predicts the next state
  `$$ \mathcal{P}_{s s'}^a = P[ S_{t+1}=s' | S_t=s, A_t=a ] $$`
- `$\mathcal{R}$` predicts the next(inmediate) reward
  `$$ \mathcal{R} = E[ R_{t+1} | S_t=s, A_t=a ] $$`


## Categorizing RL agents
### (1) Value function / Policy
- Value based
  + No Policy (implicit)
  + Value Function
- Policy based
  + Policy
  + No Value Function
- Actor Critic
  + Policy
  + Value Function
  
### (2) Model
- Model Free
  + Policy and/or Value Function
  + No Model
- Model Based
  + Policy and/or Value Function
  + Model

# Problems within RL
## Learning and Planning
2 fundamental problems in sequential decision making:
- Reinforcement Learning
  + The environment is initially unknown
  + The agent interacts with the environment
  + The agent improves its policy
- Planning
  + A model of the environment is known
  + The agent performs computations with its model (without any external interaction)
  + The agent improves its policy
  + a.k.a.  deliberation, reasoning, introspection, pondering, thought, search
  
## Exploration and Exploitation
- Reinforcement learning is like trial-and-error learning
- The agent should discover a good policy:
  + From its experiences of the environment
  + Without losing too much reward along the way
- **Exploration** finds more information about the environment
- **Exploitation** exploits known information to maximise reward
- It is usually important to explore as well as exploit

## Prediction and Control
- **Prediction**:  evaluate the future
  + Given a policy
  + *What is the value function for the uniform random policy?*
- **Control**:  optimise the future
  + Find the best policy
  + *What is the optimal value function over all possible policies?*
  + *What is the optimal policy?*
