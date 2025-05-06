# Supply Chain Planning Module: Forecasting + Optimization

This project presents a fully modular and scalable **Supply Chain Planning System** built in Python. It includes two critical components:

1. **Forecasting Model** – Implements *Simple Exponential Smoothing (SES)* completely **from scratch** to predict future demand based on historical data.
2. **Optimization Model** – Solves the *Transportation Problem* using the **PuLP** linear programming solver to minimize the cost of moving goods from suppliers to consumers.

Both modules are implemented as reusable Python classes, making the system flexible and extensible for various supply chain planning needs.

##  Objective

The aim of this project is to:
* Forecast future demand using a custom-built statistical algorithm.
* Optimize resource allocation and transportation between multiple supply and demand points.
  
##  Technologies Used

Python 3.6+ – Core programming language
PuLP – Linear programming solver used for optimization
NumPy – Used for numerical operations 
Pandas – Used to format and display results cleanly 

Install required packages using:

```bash
pip install pulp numpy pandas
```

## Forecasting – Simple Exponential Smoothing

### Why Forecasting?

Forecasting is vital in supply chain management for inventory planning, production scheduling, and budgeting.

### Model Description

I have implemented Simple Exponential Smoothing (SES) from scratch. This algorithm uses the most recent demand and previous forecast to calculate a smoothed forecast value.

### Formula Used:

Ft = α × Y(t−1) + (1 − α) × F(t−1)
Where:

* Ft is the forecast at time t
* Y(t−1) is the actual value at time t-1
* α is the smoothing factor (0 < α < 1)

### Code Sample:

```python
class ExponentialSmoothing:
    def __init__(self, alpha):
        self.alpha = alpha

    def forecast(self, data):
        forecast = [data[0]]
        for t in range(1, len(data)):
            next_val = self.alpha * data[t-1] + (1 - self.alpha) * forecast[-1]
            forecast.append(next_val)
        return forecast
```

##  Optimization – Transportation Problem

### Why Optimization?

The transportation problem helps minimize distribution cost while satisfying supply and demand constraints.

### Problem:

* We have `m` suppliers and `n` consumers.
* We want to transport goods from suppliers to consumers at the minimum cost.
* Each supplier has a supply limit.
* Each consumer has a demand requirement.
* A cost matrix defines the per-unit shipping cost.

### Code Sample:

```python
class TransportationModel:
    def __init__(self, supply, demand, cost_matrix):
        self.supply = supply
        self.demand = demand
        self.cost = cost_matrix

    def solve(self):
        model = LpProblem("Transportation_Problem", LpMinimize)
        x = [[LpVariable(f"x_{i}_{j}", lowBound=0) 
              for j in range(len(self.demand))] 
              for i in range(len(self.supply))]

        model += lpSum(self.cost[i][j] * x[i][j] 
                       for i in range(len(self.supply)) 
                       for j in range(len(self.demand)))

        for i in range(len(self.supply)):
            model += lpSum(x[i][j] for j in range(len(self.demand))) <= self.supply[i]

        for j in range(len(self.demand)):
            model += lpSum(x[i][j] for i in range(len(self.supply))) >= self.demand[j]

        model.solve()

        return {
            "Status": LpStatus[model.status],
            "Total Cost": value(model.objective),
            "Allocations": [[x[i][j].varValue for j in range(len(self.demand))] for i in range(len(self.supply))]
        }
```

## How to Run

Step 1: Install the required libraries:

```bash
pip install -r requirements.txt
```

Step 2: Run the module:

```bash
python main.py
```

Step 3: Review forecast and optimization results printed to the console.


