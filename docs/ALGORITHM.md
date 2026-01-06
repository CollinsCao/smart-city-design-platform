# Optimization Algorithm Details

## Overview

This document describes the multi-objective optimization algorithm used to evaluate urban design scenarios.

## Problem Definition

### Decision Variables
```python
x = [h₁, h₂, ..., hₙ,  # Building heights
     g₁, g₂, ..., gₘ,  # Green space allocations
     u₁, u₂, ..., uₖ]  # Land use distributions
```

### Objective Function
```python
f(x) = w₁ · Livability(x) + 
       w₂ · Sustainability(x) - 
       w₃ · Cost(x)

where:
    Livability(x) = α · Green_Space(x) + 
                    β · Walkability(x) + 
                    γ · Mixed_Use(x)
    
    Sustainability(x) = δ · Energy_Efficiency(x) + 
                        ε · Carbon_Reduction(x)
    
    Cost(x) = Infrastructure_Cost(x) + Development_Cost(x)
```

## Grid Search Implementation

### Basic Algorithm
```python
def grid_search_optimization(parameter_grid, objectives, constraints):
    """
    Grid search with constraint satisfaction
    
    Args:
        parameter_grid: Dict of parameter ranges
        objectives: List of objective functions
        constraints: List of constraint functions
    
    Returns:
        best_solution: Optimal parameter set
        best_score: Objective function value
    """
    best_score = -float('inf')
    best_solution = None
    
    # Generate all parameter combinations
    for params in generate_grid(parameter_grid):
        # Check constraints
        if not all(c(params) for c in constraints):
            continue
        
        # Calculate objective
        score = sum(w * obj(params) for w, obj in zip(weights, objectives))
        
        # Update best
        if score > best_score:
            best_score = score
            best_solution = params
    
    return best_solution, best_score
```

### Optimization Techniques

#### 1. Memoization

Cache expensive calculations:
```python
from functools import lru_cache

@lru_cache(maxsize=10000)
def calculate_walkability(building_config_hash):
    """Cached walkability calculation"""
    # Expensive computation
    # Called multiple times with same parameters
    return score
```

**Impact**: 30% reduction in computation time

#### 2. Vectorization

Use NumPy for batch operations:
```python
# Slow: Python loops
distances = []
for building in buildings:
    for amenity in amenities:
        d = sqrt((building[0]-amenity[0])**2 + (building[1]-amenity[1])**2)
        distances.append(d)

# Fast: NumPy vectorization
distances = np.sqrt(
    ((buildings[:, None, :2] - amenities[:, :2]) ** 2).sum(axis=2)
)
```

**Impact**: 50% faster distance calculations

#### 3. Spatial Indexing

PostGIS R-tree indexes:
```sql
-- Create spatial index
CREATE INDEX idx_buildings_geom 
ON buildings USING GIST(geometry);

-- Fast spatial queries
SELECT * FROM buildings
WHERE ST_DWithin(geometry, ST_MakePoint(lon, lat), 500);
```

**Impact**: 60% faster spatial queries

## Constraint Handling

### Hard Constraints

Must be satisfied:
```python
def density_constraint(scenario):
    """Maximum density constraint"""
    return scenario.density <= MAX_DENSITY

def green_space_constraint(scenario):
    """Minimum green space constraint"""
    return scenario.green_space_ratio >= MIN_GREEN_SPACE

def budget_constraint(scenario):
    """Budget constraint"""
    return scenario.total_cost <= BUDGET
```

### Soft Constraints

Penalized in objective function:
```python
def energy_penalty(scenario):
    """Penalty for exceeding energy target"""
    excess = max(0, scenario.energy - ENERGY_TARGET)
    return -PENALTY_WEIGHT * excess
```

## Performance Optimizations

### Parallel Processing
```python
from multiprocessing import Pool

def evaluate_scenarios_parallel(scenarios):
    """Evaluate scenarios in parallel"""
    with Pool(processes=4) as pool:
        scores = pool.map(evaluate_scenario, scenarios)
    return scores
```

### Early Termination
```python
def grid_search_with_pruning(params):
    """Grid search with branch pruning"""
    for param_set in param_grid:
        # Quick constraint check
        if not quick_feasibility_check(param_set):
            continue  # Skip expensive evaluation
        
        # Full evaluation only for feasible solutions
        score = full_evaluation(param_set)
```

## Results Analysis

### Pareto Frontier

Identify non-dominated solutions:
```python
def find_pareto_frontier(solutions):
    """Find Pareto-optimal solutions"""
    pareto = []
    for sol in solutions:
        dominated = False
        for other in solutions:
            if dominates(other, sol):
                dominated = True
                break
        if not dominated:
            pareto.append(sol)
    return pareto
```

### Sensitivity Analysis

Test parameter sensitivity:
```python
def sensitivity_analysis(base_params, param_name, range):
    """Analyze parameter sensitivity"""
    results = []
    for value in range:
        params = base_params.copy()
        params[param_name] = value
        score = evaluate(params)
        results.append((value, score))
    return results
```

## Complexity Analysis

### Time Complexity
```
Grid Search: O(n^d)
where:
    n = samples per dimension
    d = number of dimensions

Our case: 100^3 ≈ 1,000,000 evaluations
With optimizations: ~300,000 actual evaluations
```

### Space Complexity
```
O(k · m)
where:
    k = number of scenarios stored
    m = memory per scenario

With memoization: Additional O(c) for cache
where c = unique parameter combinations
```

## Alternative Algorithms

### Genetic Algorithm (Future Work)
```python
def genetic_algorithm(population_size, generations):
    """Genetic algorithm for optimization"""
    population = initialize_population(population_size)
    
    for gen in range(generations):
        # Evaluate fitness
        fitness = [evaluate(ind) for ind in population]
        
        # Selection
        parents = select_parents(population, fitness)
        
        # Crossover
        offspring = crossover(parents)
        
        # Mutation
        offspring = mutate(offspring)
        
        # Next generation
        population = offspring
    
    return best_individual(population)
```

### Simulated Annealing
```python
def simulated_annealing(initial_state, temp, cooling_rate):
    """Simulated annealing optimization"""
    current = initial_state
    current_score = evaluate(current)
    
    while temp > MIN_TEMP:
        # Generate neighbor
        neighbor = perturb(current)
        neighbor_score = evaluate(neighbor)
        
        # Accept if better or probabilistically
        delta = neighbor_score - current_score
        if delta > 0 or random() < exp(delta / temp):
            current = neighbor
            current_score = neighbor_score
        
        # Cool down
        temp *= cooling_rate
    
    return current
```

## Validation

### Cross-Validation

Compare with real-world outcomes:
```python
def validate_predictions(model, real_outcomes):
    """Validate model predictions"""
    predictions = [model.predict(x) for x in scenarios]
    
    mae = mean_absolute_error(real_outcomes, predictions)
    rmse = root_mean_square_error(real_outcomes, predictions)
    
    return {'MAE': mae, 'RMSE': rmse}
```

---

**Last Updated**: January 2026  
**Author**: Yuqi Cao
