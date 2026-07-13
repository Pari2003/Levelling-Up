# 23. Power(x, n)

## Intuition
`x^n` is `x * x^(n-1)`. 
However, we can optimize this:
If `n` is even, `x^n = x^(n/2) * x^(n/2)`.
If `n` is odd, `x^n = x * x^(n-1)`.

## Code
```python
def power(x, n):
    if n == 0:
        return 1
    
    if n % 2 == 0:
        half = power(x, n // 2)
        return half * half
    else:
        return x * power(x, n - 1)
```

## Complexity
- **Time Complexity:** `O(log N)`
- **Space Complexity:** `O(log N)`