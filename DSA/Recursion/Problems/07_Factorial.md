# 07. Factorial of N

## Intuition
`N! = N * (N-1) * ... * 1`. 
Identity element of multiplication is 1.

## Recursive Thinking
- **Base Case:** `if n == 0 or n == 1: return 1`
- **Recursive Step:** `return n * factorial(n - 1)`

## Code
```python
def factorial(n):
    if n == 0 or n == 1:
        return 1
    return n * factorial(n - 1)
```

## Complexity
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)`