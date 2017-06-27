# Markov Decision Processes
This is an abstract (basically a reduction of David Silver's Presentation)
http://www0.cs.ucl.ac.uk/staff/D.Silver/web/Teaching_files/MDP.pdf

# Markov Processes

## Introduction to MDPs
- Markov decision processes formally describe an environment for RL
- Where the environment is *fully* observable
- i.e. the current state completely characterises the process
- Almost all RL problems can be formalised as MDPs, e.g.
  + Optimal control primarily deals with continuous MDPs
  + Partially observable problems can be converted into MDPs
  + Bandits are MDPs with one state

## Markov Property
- "The future is independent of the past given the present"
- A state S_t is *Markov* if and only if
  `$$ P[S_{t+1} | S_t] = P[S_{t+1} | S_1, ..., S_t] $$`
- The state captures all relevant information from the history
- Once the state is known, the history may be thrown away
- i.e. The state is a sufficient statistic of the future

### State Transition Matrix
- For a Markov state `$s$` and successor state `$s'$`, the state transition probability is defined by
  `$$ \mathcal{P}_{s s'} = P[S_{t+1}=s'| S_t=s] $$`
- **State transition matrix** `$\mathcal{P}$` defines transition probabilities from all states `$s$` to all successor states `$s'$`,
  `$$ \mathcal{P} =  \begin{bmatrix}
                    \mathcal{P}_{1 1} & \dots  & \mathcal{P}_{1 n} \\ 
                    \vdots            & \ddots & \vdots \\ 
                    \mathcal{P}_{n 1} & \dots  & \mathcal{P}_{n n}
                    \end{bmatrix} $$`
  where each row of the matrix sums to 1
  + row: "from"
  + column: "to"

## Markov Process
- A Markov process is a memoryless random process, i.e. a sequence of random states `$S_1$, $S_2$, ...` with the Markov property
- A **Markov Process** (or **Markov Chain**) is a tuple `$< \mathcal{S}, \mathcal{P} >$`
  + `$ \mathcal{S} $` is a (finite) set of states
  + `$ \mathcal{P} $` is a state transition probability matrix,
    `$$ \mathcal{P}_{s s'} = P[S_{t+1}=s'| S_t=s] $$`
- **episode** for a Markov Chain: `$ S_1, S_2, ..., S_t $`



# Markov Reward Processes
- A Markov reward process is a Markov chain with values.
- A **Markov Reward Process** is a tuple `$< \mathcal{S}, \mathcal{P}, \mathcal{R}, \gamma >$`
  + `$ \mathcal{S} $` is a (finite) set of states
  + `$ \mathcal{P} $` is a state transition probability matrix,
    `$$ \mathcal{P}_{s s'} = P[S_{t+1}=s'| S_t=s] $$`
  + `$ \mathcal{R} $` is a reward function, `$ \mathcal{R}_s = E[R_{t+1} | S_t=s] $`
  + `$\gamma$` is a discount factor, `$\gamma \in [0,1]$`

## Return
- The **return** `$G_t$` is the total discounted reward from time-step `$t$`
  `$$ G_t = R_{t+1} + \gamma R_{t+2} + ... = \sum_{k=0}^\inf \gamma^k R_{t+k+1}$$`
- The **discount** `$\gamma \in [0,1]$` is the present value of future rewards
- The value of receiving reward `$R$` after `$k +1$` time-steps is `$\gamma^k R$`
- This values immediate reward above delayed reward
  + `$\gamma$` close to 0 leads to "myopic" evaluation
  + `$\gamma$` close to 1 leads to "far-sighted" evaluation

## Why discount?
- Most Markov reward and decision processes are discounted. Why?
  + Mathematically convenient to discount rewards
  + Avoids infinite returns in cyclic Markov processes
  + Uncertainty about the future may not be fully represented
  + If the reward is financial, immediate rewards may earn more interest than delayed rewards
  + Animal/human behaviour shows preference for immediate reward
  + It is sometimes possible to use undiscounted Markov reward processes (i.e. `$\gamma=1$`), e.g. if all sequences terminate.

## Value Function
- The value function `$v(s)$` gives the long-term value of state `$s$`
- The **state value function** `$v(s)$` of a MRP is the expected return starting from state `$s$`
  `$$ v(s) = E[G_t | S_t=s] $$`
- E.g. returns sampled from a MRP: `$G_1 = R_2 + \gammaR_3 + ... + \gamma^{T-2}R_T$`

## Bellman Equation for MRPs
- The value function can be decomposed into two parts:
  + immediate reward `$R_{t+1}$`
  + discounted value of successor state `$\gamma v(S_{t+1})$`

  `$$
  v(s) = E[G_t | S_t=s]
       = E[R_{t+1} + \gammaR_{t+2} + \gamma^2R_{t+3} + ...| S_t=s]
       = E[R_{t+1} + \gamma(R_{t+2} + \gammaR_{t+3} + ...)| S_t=s]
       = E[R_{t+1} + \gammaG_{t+1}| S_t=s]
       = E[R_{t+1} + \gamma v(S_{t+1})| S_t=s]
       = \mathcal{R}_s + \gamma \sum_{s' \in \mathcal{S}}\mathcal{P}_{s s'}_v(s')
  $$
  `

## Bellman Equation in Matrix Form
The Bellman equation can be expressed concisely using matrices,
`v = \mathcal{R} + \gamma \mathcal{P} v`
where `$v$` is a column vector with one entry per state
`$$
\begin{bmatrix} v(1) \\ \dots \\ v(n) \end{bmatrix} 
= 
\begin{bmatrix} \mathcal{R}_1 \\ \dots \\ \mathcal{R}_n \end{bmatrix} 
+
\gamma
\begin{bmatrix}
\mathcal{P}_{1 1} & \dots  & \mathcal{P}_{1 n} \\ 
\vdots            & \ddots & \vdots \\ 
\mathcal{P}_{n 1} & \dots  & \mathcal{P}_{n n}
\end{bmatrix}
\begin{bmatrix} v(1) \\ \dots \\ v(n) \end{bmatrix} 
$$`

## Solving the Bellman Equation
- The Bellman equation is a linear equation
- It can be solved directly:
  `
  $$
  v = \mathcal{R} + \gamma \mathcal{P} v
  (I - \gamma \mathcal{P}) v = \mathcal{R}
  v = (I - \gamma \mathcal{P})^{-1} \mathcal{R}
  $$
  `
- Computational complexity is `$O(n^3)$` for `$n$` states
- Direct solution only possible for small MRPs
- There are many iterative methods for large MRPs, e.g.
  + Dynamic programming
  + Monte-Carlo evaluation
  + Temporal-Difference learning



# Markov Decision Processes
- A Markov decision process (**MDP**) is a Markov reward process with decisions. It is an environment in which all states are Markov.
- A **Markov decision process** (**MDP**) is a tuple `< \mathcal{S}, \mathcal{A}, \mathcal{P}, \mathcal{R}, \gamma >`
  + `$\mathcal{S}$` is a finite set of states
  + **`$\mathcal{A}$` is a finite set of actions**
  + `$\mathcal{P}$` is a state transition probability matrix,
    `$$ \mathcal{P}_{s s'}^a = P[S_{t+1}=s' | S_t=s,A_t=a] $$`
  + `$mathcal{R}$` is a reward function, `$ \mathcal{R}_s^a = E[ \mathcal{R}_{t+1} | S_t=s,A_t=a ] $`
  + `$\gamma$` is a discount factor `$\gamma \in [0,1]$`

## Policies
- A **policy** `$\pi$` is a distribution over given states,
  `$$ \pi(a|s) = P[A_t=a | S_t=s] $$`
- A policy fully defines the behaviour of an agent
- MDP policies depend on the current state (not the history)
- i.e. Policies are *stationary* (time-independent),
  `$$ A_t \sim \pi(·|S_t), \forall t > 0 $$`
- Given a MDP `$ \mathcal{M} = < \mathcal{S}, \mathcal{A}, \mathcal{P}, \mathcal{R}, \gamma > $` and a policy `$\pi$`
- The state sequence `$ S_1, S_2, ... $` is a Markov process `$< \mathcal{S}, \mathcal{P}^\pi >$`
- The state and reward sequence `$ S_1, R_2, S_2, ... $` is a Markov reward process `$ \mathcal{S}, \mathcal{P}^\pi, \mathcal{R}^\pi, \gamma $`
- where
`
$$
\mathcal{P}_{s,s'}^\pi = \sum_{a \in \mathcal{A}} \pi(a|s)\mathcal
$$
`


# Extensions to MDPs

