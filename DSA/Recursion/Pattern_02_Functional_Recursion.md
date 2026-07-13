# Pattern 2: Functional Recursion

Instead of carrying an accumulator variable through all your recursive calls (parameterized recursion), you let the function return the answer for its subproblem.

## Parameterized (Passing state)
```python
def sum_param(i, current_sum):
    if i < 1:
        print(current_sum)
        return
    sum_param(i - 1, current_sum + i)
```

## Functional (Returning state)
```python
def sum_func(n):
    if n == 0:
        return 0
    
    # Return my contribution (n) + the answer to the smaller problem
    return n + sum_func(n - 1)
```

**Why Functional is better for interviews:**
1. Easier to reason about (Faith principle).
2. Pure functions (no side effects).
3. Easily translated into Dynamic Programming.