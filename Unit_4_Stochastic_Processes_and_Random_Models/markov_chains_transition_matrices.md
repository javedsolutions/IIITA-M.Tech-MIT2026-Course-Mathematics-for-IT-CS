# :classical_building: Mathematics for IT Course - M.Tech. 1st Semester, IIIT Allahabad
## Unit 4: Stochastic Processes and Random Models
* ### Current Topic: Markov Chains, Transition Probability Matrices, and Steady-State Analysis
* #### introducing Markov chains, transition probabilities, transition matrices, multi-step transitions, stationary distributions, steady-state analysis, absorbing chains, and practical Python implementations
---
## 👥 Instructor Information
* **Edited by Instructor:** [Dr. Mohammed Javed](https://sites.google.com/site/mohammedjaved2016/)
* **Email:** javed@iiita.ac.in
* **Teaching Assistants:** Mr. Apurba Chakraborty (rsi2024005@iiita.ac.in)
---
## 🎯 Learning Objectives
After studying this chapter, you should be able to:

- define a Markov chain and explain the Markov property;
- identify states and transitions in a stochastic system;
- construct a transition probability matrix;
- calculate one-step and multi-step transition probabilities;
- propagate a probability distribution using matrix multiplication;
- find and interpret a stationary or steady-state distribution;
- understand irreducibility, periodicity, and ergodicity at an introductory level;
- distinguish transient and absorbing states;
- simulate Markov chains using Python;
- use steady-state analysis to interpret long-run behavior.

---

# 1. Introduction

Many real-world systems move between a finite or countable collection of states.

Examples include:

- weather conditions changing from day to day;
- customers moving between stages of a service system;
- machines moving between operating and failure states;
- users moving between pages or activities;
- inventory moving between stock levels;
- credit ratings changing over time.

In many such systems, the next state is uncertain.

A **Markov chain** is a mathematical model for a system that moves randomly between states while satisfying a special memory property.

---

# 2. The Markov Property

Let:

\[
X_0,X_1,X_2,\ldots
\]

be a stochastic process.

It is a **Markov chain** if:

\[
\boxed{
P(X_{n+1}=j\mid X_n=i,X_{n-1},\ldots,X_0)
=
P(X_{n+1}=j\mid X_n=i)
}
\]

In words:

> Given the current state, the future does not depend on the past.

This is called the **Markov property**.

The past may have influenced the current state, but once the current state is known, it contains all the information needed for predicting the next state.

---

# 3. Intuition: The Memoryless State Description

Imagine a weather model with three states:

- \(S\): Sunny
- \(C\): Cloudy
- \(R\): Rainy

Suppose today's weather is Sunny.

If tomorrow's weather probabilities depend only on today's weather, then:

\[
P(X_{n+1}=R\mid X_n=S,X_{n-1},\ldots)
=
P(X_{n+1}=R\mid X_n=S).
\]

The weather from several days ago does not provide additional information once today's state is known.

![Markov chain state diagram](figures/01_markov_chain_state_diagram.png)

---

# 4. States and Transitions

A Markov chain consists of:

1. a collection of **states**;
2. transition probabilities between states.

For example:

\[
\mathcal S=\{A,B,C\}.
\]

The process occupies one state at each time step.

A transition is movement from one state to another.

For example:

\[
A\rightarrow B
\]

means the system moves from state \(A\) to state \(B\).

A transition may also remain in the same state:

\[
A\rightarrow A.
\]

This is called a **self-transition**.

---

# 5. Transition Probabilities

For states \(i\) and \(j\), define:

\[
\boxed{
p_{ij}=P(X_{n+1}=j\mid X_n=i)
}
\]

where:

- \(i\) is the current state;
- \(j\) is the next state.

For a finite-state Markov chain, the transition probabilities satisfy:

\[
p_{ij}\ge0
\]

and:

\[
\boxed{
\sum_jp_{ij}=1
}
\]

for every state \(i\).

Thus, every row of the transition matrix represents a probability distribution.

---

# 6. Transition Probability Matrix

The transition probabilities can be organized into a matrix:

\[
\boxed{
P=
\begin{bmatrix}
p_{11}&p_{12}&\cdots&p_{1m}\\
p_{21}&p_{22}&\cdots&p_{2m}\\
\vdots&\vdots&\ddots&\vdots\\
p_{m1}&p_{m2}&\cdots&p_{mm}
\end{bmatrix}
}
\]

where:

\[
p_{ij}=P(X_{n+1}=j\mid X_n=i).
\]

For three states:

\[
P=
\begin{bmatrix}
p_{AA}&p_{AB}&p_{AC}\\
p_{BA}&p_{BB}&p_{BC}\\
p_{CA}&p_{CB}&p_{CC}
\end{bmatrix}.
\]

![Transition matrix heatmap](figures/02_transition_probability_matrix.png)

---

# 7. Properties of a Transition Matrix

A transition matrix must satisfy:

### Non-negativity

\[
p_{ij}\ge0.
\]

### Row sums equal one

\[
\sum_jp_{ij}=1.
\]

Therefore:

\[
\boxed{
P\mathbf 1=\mathbf1
}
\]

where \(\mathbf1\) is a vector of ones.

Such a matrix is called a **stochastic matrix** or **row-stochastic matrix**.

---

# 8. Example Transition Matrix

Consider:

\[
P=
\begin{bmatrix}
0.7&0.2&0.1\\
0.1&0.6&0.3\\
0.2&0.2&0.6
\end{bmatrix}.
\]

The first row means that if the process is currently in state \(A\):

\[
P(A\to A)=0.7
\]

\[
P(A\to B)=0.2
\]

\[
P(A\to C)=0.1.
\]

The probabilities sum to:

\[
0.7+0.2+0.1=1.
\]

---

# 9. Python Representation

```python
import numpy as np

P = np.array([
    [0.7, 0.2, 0.1],
    [0.1, 0.6, 0.3],
    [0.2, 0.2, 0.6]
])

print(P)
print("Row sums:", P.sum(axis=1))
```

Output:

```text
Row sums: [1. 1. 1.]
```

---

# 10. One-Step Transition Probabilities

The entries of \(P\) give one-step transition probabilities.

If:

\[
X_n=A,
\]

then:

\[
P(X_{n+1}=B\mid X_n=A)=p_{AB}.
\]

For the example matrix:

\[
P(A\to B)=0.2.
\]

Similarly:

\[
P(C\to A)=0.2.
\]

---

# 11. Multi-Step Transition Probabilities

What if we want the probability of moving from state \(i\) to state \(j\) in two or more steps?

The answer is given by powers of the transition matrix.

### Two steps

\[
\boxed{
P^{(2)}=P^2
}
\]

### Three steps

\[
\boxed{
P^{(3)}=P^3
}
\]

### \(n\) steps

\[
\boxed{
P^{(n)}=P^n.
}
\]

The element:

\[
(P^n)_{ij}
\]

is the probability of being in state \(j\) after \(n\) steps given that the process started in state \(i\).

![Multi-step transition probabilities](figures/03_multistep_transition_probabilities.png)

---

# 12. Why Matrix Multiplication Works

For two steps:

\[
P(X_{n+2}=j\mid X_n=i)
\]

can be computed by conditioning on the intermediate state \(k\):

\[
P(X_{n+2}=j\mid X_n=i)
=
\sum_k
P(X_{n+2}=j\mid X_{n+1}=k)
P(X_{n+1}=k\mid X_n=i).
\]

Therefore:

\[
\boxed{
p_{ij}^{(2)}
=
\sum_kp_{ik}p_{kj}
}
\]

which is exactly the \((i,j)\)-entry of:

\[
P^2.
\]

---

# 13. Python: Multi-Step Transitions

```python
import numpy as np

P = np.array([
    [0.7, 0.2, 0.1],
    [0.1, 0.6, 0.3],
    [0.2, 0.2, 0.6]
])

P2 = np.linalg.matrix_power(P, 2)
P3 = np.linalg.matrix_power(P, 3)

print("P^2:")
print(P2)

print("P^3:")
print(P3)
```

---

# 14. Initial Probability Distribution

Suppose the initial distribution is:

\[
\pi_0=
\begin{bmatrix}
1&0&0
\end{bmatrix}.
\]

This means the process starts in state \(A\) with probability 1.

If:

\[
\pi_n=
\begin{bmatrix}
P(X_n=A)&P(X_n=B)&P(X_n=C)
\end{bmatrix},
\]

then:

\[
\boxed{
\pi_{n+1}=\pi_nP.
}
\]

After \(n\) steps:

\[
\boxed{
\pi_n=\pi_0P^n.
}
\]

---

# 15. Matrix Evolution of a Markov Chain

The basic workflow is:

![Markov matrix workflow](figures/07_markov_matrix_workflow.png)

\[
\boxed{
\pi_{n+1}=\pi_nP
}
\]

Repeatedly applying this equation allows us to study the evolution of the probability distribution.

Python:

```python
pi = np.array([1.0, 0.0, 0.0])

for n in range(10):
    print(n, pi)
    pi = pi @ P
```

---

# 16. Steady State and Stationary Distribution

A probability distribution:

\[
\pi=
\begin{bmatrix}
\pi_1&\pi_2&\cdots&\pi_m
\end{bmatrix}
\]

is called a **stationary distribution** if:

\[
\boxed{
\pi P=\pi.
}
\]

It must also satisfy:

\[
\boxed{
\sum_i\pi_i=1
}
\]

and:

\[
\pi_i\ge0.
\]

The stationary distribution describes a probability distribution that remains unchanged after one transition.

---

# 17. Interpretation of Steady State

Suppose:

\[
\pi=
\begin{bmatrix}
0.6&0.4
\end{bmatrix}.
\]

This means that in the long run, the process spends approximately:

- 60% of its time in state 1;
- 40% of its time in state 2.

The stationary distribution is not necessarily the probability distribution at the beginning.

It describes **long-run behavior** under appropriate conditions.

---

# 18. Example: Finding a Stationary Distribution

Consider:

\[
P=
\begin{bmatrix}
0.8&0.2\\
0.3&0.7
\end{bmatrix}.
\]

Let:

\[
\pi=
\begin{bmatrix}
\pi_1&\pi_2
\end{bmatrix}.
\]

We need:

\[
\pi P=\pi.
\]

Therefore:

\[
0.8\pi_1+0.3\pi_2=\pi_1
\]

and:

\[
0.2\pi_1+0.7\pi_2=\pi_2.
\]

Together with:

\[
\pi_1+\pi_2=1.
\]

Solving gives:

\[
\boxed{
\pi=
\begin{bmatrix}
0.6&0.4
\end{bmatrix}.
}
\]

---

# 19. Python: Solving for the Stationary Distribution

One convenient numerical method is to find the eigenvector associated with eigenvalue 1.

```python
import numpy as np

P = np.array([
    [0.8, 0.2],
    [0.3, 0.7]
])

eigenvalues, eigenvectors = np.linalg.eig(P.T)

index = np.argmin(np.abs(eigenvalues - 1))

pi = np.real(eigenvectors[:, index])
pi = pi / pi.sum()

print("Stationary distribution:", pi)
```

Expected result:

```text
[0.6 0.4]
```

We use \(P^T\) because the stationary equation for a row vector is:

\[
\pi P=\pi.
\]

---

# 20. Iterative Steady-State Calculation

An alternative is to repeatedly apply the transition matrix.

Start with:

\[
\pi_0=[1,0].
\]

Then:

\[
\pi_1=\pi_0P
\]

\[
\pi_2=\pi_1P
\]

and so on.

Python:

```python
import numpy as np

P = np.array([
    [0.8, 0.2],
    [0.3, 0.7]
])

pi = np.array([1.0, 0.0])

for n in range(30):
    pi = pi @ P

print(pi)
```

The result approaches:

\[
[0.6,0.4].
\]

![Steady-state convergence](figures/04_steady_state_convergence.png)

---

# 21. Dependence on the Initial Distribution

For many well-behaved Markov chains, different initial distributions converge to the same stationary distribution.

For example, we can start with:

\[
[1,0],
\]

\[
[0,1],
\]

or:

\[
[0.2,0.8].
\]

Under suitable conditions, all converge to the same steady state.

![Convergence from different initial distributions](figures/06_initial_distribution_convergence.png)

This property is fundamental to steady-state analysis.

---

# 22. Irreducibility

A Markov chain is **irreducible** if every state can eventually be reached from every other state.

Informally:

> The chain is one communicating system rather than separate disconnected groups.

For states \(i\) and \(j\), there must exist some \(n\ge1\) such that:

\[
(P^n)_{ij}>0.
\]

If this holds for every pair \(i,j\), the chain is irreducible.

---

# 23. Periodicity

A state can have a **period** describing the possible return times to that state.

The period of state \(i\) is:

\[
d(i)=
\gcd\{n\ge1:(P^n)_{ii}>0\}.
\]

If:

\[
d(i)=1,
\]

the state is **aperiodic**.

For an irreducible finite Markov chain, all states have the same period.

Aperiodicity is important because it helps ensure convergence to a steady state rather than persistent oscillation.

---

# 24. Ergodic Markov Chains

For a finite Markov chain, a common sufficient setting for steady-state convergence is that the chain is:

- irreducible;
- aperiodic.

Such a chain has a unique stationary distribution, and under the standard finite-state assumptions:

\[
\boxed{
\pi_0P^n\to\pi
}
\]

as:

\[
n\to\infty.
\]

The limiting distribution does not depend on the initial distribution.

---

# 25. Absorbing Markov Chains

A state \(i\) is **absorbing** if:

\[
\boxed{
p_{ii}=1.
}
\]

Once the process enters an absorbing state, it never leaves.

![Absorbing Markov chain](figures/05_absorbing_markov_chain.png)

Examples include:

- machine permanently failed;
- customer permanently churned;
- game reaching a terminal state;
- process reaching a final success or failure condition.

---

# 26. Transient and Absorbing States

A **transient state** is one that may be left and not necessarily revisited.

An **absorbing state** traps the process permanently.

An absorbing chain typically contains:

- one or more transient states;
- at least one absorbing state.

A major question is:

> What is the probability that the process eventually reaches each absorbing state?

---

# 27. Canonical Form of an Absorbing Chain

After rearranging states, a transition matrix can often be written as:

\[
P=
\begin{bmatrix}
Q&R\\
0&I
\end{bmatrix}.
\]

Here:

- \(Q\) describes transitions among transient states;
- \(R\) describes transitions from transient to absorbing states;
- \(I\) represents absorbing states.

The **fundamental matrix** is:

\[
\boxed{
N=(I-Q)^{-1}.
}
\]

It can be used to calculate quantities such as expected numbers of visits to transient states and absorption probabilities.

---

# 28. Long-Run State Probabilities

Suppose the chain has stationary distribution:

\[
\pi=[0.6,0.4].
\]

If the system is observed for a very long period, the fraction of time spent in each state approaches these values under appropriate ergodicity assumptions.

Thus:

\[
\boxed{
\text{Long-run fraction in state }i
\approx\pi_i.
}
\]

This interpretation makes stationary distributions especially useful in applications.

---

# 29. Example: Weather Markov Chain

Suppose:

\[
P=
\begin{bmatrix}
0.8&0.2\\
0.4&0.6
\end{bmatrix}
\]

where:

- state 1 = Sunny;
- state 2 = Rainy.

Then:

\[
P(\text{Sunny tomorrow}\mid\text{Sunny today})=0.8.
\]

And:

\[
P(\text{Rainy tomorrow}\mid\text{Sunny today})=0.2.
\]

The transition matrix allows us to answer questions about weather several days ahead.

---

# 30. Python: Weather Chain Simulation

```python
import numpy as np

rng = np.random.default_rng(42)

P = np.array([
    [0.8, 0.2],
    [0.4, 0.6]
])

state = 0
states = [state]

for _ in range(100):
    state = rng.choice([0, 1], p=P[state])
    states.append(state)

print(states)
```

Here, `0` represents Sunny and `1` represents Rainy.

---

# 31. Simulating Many Markov Chains

Simulation can help verify theoretical steady-state behavior.

```python
import numpy as np
import matplotlib.pyplot as plt

rng = np.random.default_rng(42)

P = np.array([
    [0.8, 0.2],
    [0.3, 0.7]
])

state = 0
states = [state]

for _ in range(1000):
    state = rng.choice([0, 1], p=P[state])
    states.append(state)

states = np.array(states)

running_fraction_state0 = np.cumsum(states == 0) / np.arange(1, len(states) + 1)

plt.plot(running_fraction_state0)
plt.axhline(0.6, linestyle="--", label="Steady-state probability")
plt.xlabel("Time")
plt.ylabel("Observed fraction in state 0")
plt.title("Empirical Convergence to the Steady State")
plt.legend()
plt.show()
```

The observed long-run fraction should approach the theoretical stationary probability.

---

# 32. A Useful Matrix Identity

If:

\[
\pi P=\pi,
\]

then:

\[
\pi P^2=(\pi P)P=\pi P=\pi.
\]

By induction:

\[
\boxed{
\pi P^n=\pi
}
\]

for every:

\[
n\ge1.
\]

Therefore, once a Markov chain starts in its stationary distribution, its distribution remains unchanged over time.

---

# 33. Detailed Balance and Reversibility

A Markov chain satisfies **detailed balance** with respect to \(\pi\) if:

\[
\boxed{
\pi_i p_{ij}
=
\pi_j p_{ji}
}
\]

for all states \(i,j\).

This means the stationary probability flow from \(i\) to \(j\) equals the flow from \(j\) to \(i\).

If a distribution satisfies detailed balance, it is stationary:

\[
\pi P=\pi.
\]

Reversible Markov chains are important in statistical physics and Monte Carlo methods.

---

# 34. Markov Chains in Data Science

Markov chains appear in many applications.

### Web navigation

A user moves among web pages.

### Recommendation systems

A user moves among categories or content states.

### Queueing systems

A service system moves among different queue lengths.

### Finance

Credit ratings transition among risk categories.

### Natural language

Words or linguistic states can be modeled sequentially.

### Reinforcement learning

State-transition models are central to Markov Decision Processes.

### Monte Carlo methods

Markov Chain Monte Carlo (MCMC) constructs a Markov chain whose stationary distribution is the target probability distribution.

---

# 35. Python: Basic Validation of a Transition Matrix

```python
import numpy as np

def is_transition_matrix(P, tol=1e-10):
    P = np.asarray(P)

    # Probabilities must be non-negative
    if np.any(P < -tol):
        return False

    # Each row must sum to one
    if not np.allclose(P.sum(axis=1), 1, atol=tol):
        return False

    return True


P = np.array([
    [0.7, 0.2, 0.1],
    [0.1, 0.6, 0.3],
    [0.2, 0.2, 0.6]
])

print(is_transition_matrix(P))
```

---

# 36. Python: General Stationary Distribution Function

```python
import numpy as np

def stationary_distribution(P):
    P = np.asarray(P, dtype=float)

    eigenvalues, eigenvectors = np.linalg.eig(P.T)

    index = np.argmin(np.abs(eigenvalues - 1))

    pi = np.real(eigenvectors[:, index])

    # Normalize
    pi = pi / pi.sum()

    return pi


P = np.array([
    [0.8, 0.2],
    [0.3, 0.7]
])

pi = stationary_distribution(P)

print(pi)
print("Check:", pi @ P)
```

The final two quantities should agree:

```text
pi
pi @ P
```

up to numerical rounding.

---

# 37. Important Formulas

### Markov property

\[
\boxed{
P(X_{n+1}=j\mid X_n=i,\ldots,X_0)
=
P(X_{n+1}=j\mid X_n=i)
}
\]

### Transition probability

\[
\boxed{
p_{ij}=P(X_{n+1}=j\mid X_n=i)
}
\]

### Transition matrix

\[
\boxed{
P=[p_{ij}]
}
\]

### Multi-step transition matrix

\[
\boxed{
P^{(n)}=P^n
}
\]

### Distribution update

\[
\boxed{
\pi_{n+1}=\pi_nP
}
\]

### Distribution after \(n\) steps

\[
\boxed{
\pi_n=\pi_0P^n
}
\]

### Stationary distribution

\[
\boxed{
\pi P=\pi
}
\]

with:

\[
\boxed{
\sum_i\pi_i=1,\qquad\pi_i\ge0.
}
\]

---

# 38. Common Misconceptions

### Misconception 1: Markov means independent.

**False.**

A Markov chain can have strong dependence between consecutive states.

The Markov property says that the current state contains the necessary information about the past for predicting the next state.

### Misconception 2: Every Markov chain has a unique steady state.

**False.**

Uniqueness and convergence require additional conditions.

### Misconception 3: The stationary distribution is the initial distribution.

**False.**

The initial distribution is chosen at time zero. A stationary distribution is invariant under the transition matrix.

### Misconception 4: A transition matrix must be symmetric.

**False.**

A transition matrix only needs nonnegative entries with rows summing to one.

### Misconception 5: A steady state means the chain stops changing.

**False.**

The states may continue changing. The **probability distribution** remains stable in steady state.

---

# 39. Practice Questions

1. Define a Markov chain.
2. Explain the Markov property in your own words.
3. What is a state?
4. What is a transition probability?
5. What conditions must a transition matrix satisfy?
6. Construct a transition matrix for a two-state weather model.
7. What does \(P^2\) represent?
8. What does \(P^n\) represent?
9. If \(\pi_0=[1,0]\), how do you calculate \(\pi_3\)?
10. Define a stationary distribution.
11. Solve \(\pi P=\pi\) for a two-state chain.
12. Explain the meaning of a steady-state probability.
13. What does irreducibility mean?
14. What is an aperiodic Markov chain?
15. What is an absorbing state?
16. Explain the difference between transient and absorbing states.
17. Simulate a two-state Markov chain in Python.
18. Write Python code to compute \(P^5\).
19. Write Python code to find a stationary distribution.
20. Simulate several different initial distributions and check whether they converge to the same steady state.
21. Explain why the stationary distribution does not mean that individual states stop changing.
22. Give three real-world applications of Markov chains.

---

# 40. Summary

A Markov chain models a random system that moves between states while satisfying the Markov property:

\[
\boxed{
\text{Future depends on the present, not the full past.}
}
\]

The transition probability matrix:

\[
P=[p_{ij}]
\]

contains the probabilities of moving between states.

Matrix powers provide multi-step probabilities:

\[
\boxed{
P^n.
}
\]

Probability distributions evolve according to:

\[
\boxed{
\pi_{n+1}=\pi_nP.
}
\]

A stationary distribution satisfies:

\[
\boxed{
\pi P=\pi.
}
\]

Under suitable conditions such as irreducibility and aperiodicity for a finite chain, the distribution can converge to a unique steady state:

\[
\boxed{
\pi_0P^n\to\pi.
}
\]

The central picture is therefore:

```text
States
  |
  v
Transition probabilities
  |
  v
Transition matrix P
  |
  v
Repeated transitions P^n
  |
  v
Evolution of probability distributions
  |
  v
Long-run behavior
  |
  v
Steady-state / stationary distribution
```

Markov chains provide a powerful framework for analyzing sequential random systems and are fundamental to probability, statistics, operations research, queueing theory, machine learning, and Monte Carlo simulation.

---

# 41. Final Takeaway

The most important ideas can be summarized as:

\[
\boxed{
\text{Markov property}
\Rightarrow
\text{transition probabilities}
\Rightarrow
\text{transition matrix}
\Rightarrow
\text{matrix powers}
\Rightarrow
\text{long-run behavior}.
}
\]

Once a stochastic system can be represented by states and transition probabilities, matrix algebra gives us a practical way to analyze its future behavior and, under appropriate conditions, its steady state.

---
## ❓: CHALLENGING Questions - Check Your Understanding 
* ➡️ **[Q-01]**
* ➡️ 

---
## 📚 References 
* **[R-01]** Linear Algebra by Gilbert Strang, MIT Press
* **[R-02]** ChatGPT - for examples and codes
