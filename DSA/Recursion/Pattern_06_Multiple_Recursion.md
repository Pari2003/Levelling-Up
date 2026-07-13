# Pattern 6: Multiple Recursion

Multiple recursion occurs when a function makes more than one recursive call within its body. 

## Fibonacci Example
```python
def fib(n):
    if n <= 1: return n
    return fib(n - 1) + fib(n - 2)
```

## Characteristics
- **Branching Factor:** How many calls it makes. (e.g., `fib` has a branching factor of 2).
- **Overlapping Subproblems:** `fib(5)` calls `fib(4)` and `fib(3)`. `fib(4)` ALSO calls `fib(3)`. The exact same work is done multiple times.
- **Motivation for DP:** Because multiple recursion often recalculates the same states, we use Dynamic Programming (Memoization) to cache results, dropping time complexity from exponential to linear.