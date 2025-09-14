# Track 2: Quantum Optimization for Impact

### Introduction to Knapsack Problem
The knapsack problem is a combinatorial optimization task that involves selecting a subset of items from a larger collection. The objective is to maximize the total value of the selected items, subject to the constraint that their combined weight does not surpass a predefined capacity, known as the *knapsack*.
### Problem 
Optimizing the allocation of budget for the best overall societal outcome is a difficult task because of multiple sectors with the limited budget. Mixing budget to several sources require identifying the optimum combination returning best overall value. The problem shifts from simply funding needs to maximizing the "return on investment" for social good. This requires an optimization model that can find out optimum budget allocated to each sector with the best social impact. We can model this problem as a *Knapsack* problem and use *Quantum Approximate Optimization Algorithm (QAOA)* to find out the best solution.

We have simulated a budget allocation problem with some dummy data with societal impact score for each sector. 
Imagine we are managing a $100,000 grant budget and we must choose among several nonprofit projects. Each project has:

- A cost (how much funding it requires)

- An impact score (how much good it does)

Our goal: maximize total impact without exceeding the budget.

### Project candidates
| Emoji | Project Name           | Cost (k$) | Impact Score |
|-------|------------------------|-----------|---------------|
| 🚰    | Clean Water            | 30        | 80            |
| 📖    | Literacy Program       | 20        | 50            |
| 🔋    | Renewable Energy       | 50        | 120           |
| 🍽️    | Food Security          | 40        | 70            |
| 🧠    | Mental Health          | 10        | 30            |
| 🌳    | Urban Greening         | 25        | 65            |
| 🎓    | STEM Education         | 35        | 90            |
| 🏥    | Mobile Clinics         | 45        | 110           |
| 🧪    | Disease Research       | 15        | 40            |
| 🎭    | Arts Access            | 10        | 25            |
| 🧼    | Hygiene Kits           | 12        | 35            |
| 🌍    | Climate Action         | 38        | 95            |
| 📡    | Rural Internet         | 22        | 60            |
| 🚸    | Child Safety           | 18        | 45            |
| 🧯    | Fire Prevention        | 20        | 55            |
| 📦    | Disaster Relief        | 8         | 20            |
| 🧃    | School Nutrition       | 5         | 15            |
| 🧩    | Disability Inclusion   | 30        | 85            |
| 🧤    | Winter Clothing        | 4         | 10            |
| 🧬    | Genetic Health Equity  | 28        | 75            |


**Total budget: $100k**

**Optimize: Budget Impact Score**

## Our approach



Problem -------> QUBO representation --------> QAOA

### QUBO Formulation

To solve this problem with a quantum algorithm, we must first translate it into a **QUBO (Quadratic Unconstrained Binary Optimization)** format.  
A QUBO is a specific type of optimization problem that uses binary variables and is the native format for many quantum computing and annealing approaches.

---

#### General Form

The goal of a QUBO is to find the binary vector $\mathbf{x}$ (where each $x_i \in \{0,1\}$) that minimizes the objective function:

$$
\min_{\mathbf{x}} \mathbf{x}^T Q \mathbf{x} \;=\; 
\min_{\mathbf{x}} \left( \sum_{i} Q_{ii} x_i \;+\; \sum_{i<j} Q_{ij} x_i x_j \right)
$$

where:

- $\mathbf{x}$ is the vector of binary decision variables ($x_i = 1$ if we select item $i$, and $0$ otherwise).  
- $Q$ is an upper-triangular matrix that encodes the problem’s objective and constraints.  
- The diagonal terms ($Q_{ii}$) represent linear costs, and the off-diagonal terms ($Q_{ij}$) represent quadratic costs.

---

#### Applying QUBO to the Knapsack Problem

Our problem has two parts: an objective to maximize and a constraint to obey. Both must be encoded into the $Q$ matrix.

1. **Objective:**  
   We want to maximize the total impact score:

   $$
   \sum_i v_i x_i
   $$

   Since QUBOs are designed to *minimize*, we flip the sign and aim to minimize:

   $$
   -\sum_i v_i x_i
   $$

   These terms contribute to the diagonal elements of the $Q$ matrix ($Q_{ii}$).

2. **Constraint:**  
   We have a budget constraint:

   $$
   \sum_j w_j x_j \leq W_{\text{max}}
   $$

   QUBOs are *unconstrained*, so we enforce this rule by adding a **penalty term** to our objective function.  
   This term is designed to be zero if the constraint is satisfied and a large positive number if it’s violated.  

   A common way is to introduce slack variables to turn it into an equality, then square it and add with a penalty factor $\lambda$:

   $$
   \text{Objective}_{\text{QUBO}} \;=\; -\sum_i v_i x_i \;+\; \lambda \left( \sum_j w_j x_j + s - W_{\text{max}} \right)^2
   $$

When expanded, this penalty term creates the quadratic ($x_i x_j$) interactions that populate the off-diagonal elements of the $Q$ matrix.  
By finding the binary vector $\mathbf{x}$ that minimizes this combined objective, we obtain the set of items that maximizes the impact score while respecting the budget.



### Solving with quantum Approximate Optimization Algorithm (QAOA)

The core idea is to prepare a quantum state by repeatedly applying two distinct operators: a **problem Hamiltonian** and a **mixer Hamiltonian**.

#### 1. Initialization
First, we initialize the qubits in an equal superposition of all possible computational basis states. For a system of $n$ qubits, this state $|\psi_0\rangle$ is created by applying a Hadamard gate ($H$) to each qubit, starting from the $|0\rangle^{\otimes n}$ state:

$$|\psi_0\rangle = H^{\otimes n} |0\rangle^{\otimes n} = \frac{1}{\sqrt{2^n}} \sum_{z=0}^{2^n-1} |z\rangle$$

#### 2. Applying the Unitary Operators
The algorithm proceeds by applying a sequence of two unitary operators for $p$ layers.

* **The Problem Hamiltonian ($U_C$)**: This unitary encodes the cost function of the problem, which we want to minimize. It is derived from the problem Hamiltonian $H_C$ (QUBO formulation in our case) and is applied for a duration determined by the parameter $\gamma$:

    $$
    U_C(\gamma) = e^{-i\gamma H_C}
    $$

* **The Mixer Hamiltonian ($U_B$)**: This unitary, derived from a mixer Hamiltonian $H_B$, allows the quantum state to explore different possible solutions. A common choice for $H_B$ is the sum of Pauli-X operators. It is applied for a duration determined by the parameter $\beta$:

    $$
    U_B(\beta) = e^{-i\beta H_B} \quad \text{where} \quad H_B = \sum_{j=1}^{n} X_j
    $$

For a circuit with $p$ layers, the final state $|\psi_p(\boldsymbol{\gamma}, \boldsymbol{\beta})\rangle$ is prepared by applying these operators alternately:

$$|\psi_p(\boldsymbol{\gamma}, \boldsymbol{\beta})\rangle = U_B(\beta_p) U_C(\gamma_p) \cdots U_B(\beta_1) U_C(\gamma_1) |\psi_0\rangle$$

#### 3. Measurement and Classical Optimization
After preparing the state, the qubits are measured in the computational basis. This process is repeated many times to estimate the expectation value $\langle\psi_p|H_C|\psi_p\rangle$. A classical computer then optimizes the parameters $(\boldsymbol{\gamma}, \boldsymbol{\beta})$ to find the set of angles that minimizes this expectation value. The resulting state corresponds to an approximate solution to the optimization problem.




