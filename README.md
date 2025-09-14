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

### Our approach

In our solution, we will start with tackle the challenge with smaller 5-project problem first, then try to deal with a fully-project problem. First, we will try to solve the combinatoire problem classically, we will show the limitation of this method as the number of projects increases in classical computers. In quantum computers, we will use two common methods for encoding constraints : slack variables and unbalanced penalization. The The slack variable method is the most common method for solving 
