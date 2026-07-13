# 20. Sum of Digits

## Intuition
Extract the last digit using `% 10`. Pass the remaining number using `// 10`.

## Code
```python
def sum_digits(n):
    if n == 0:
        return 0
    return (n % 10) + sum_digits(n // 10)
```

## Complexity
- **Time Complexity:** `O(log10(N))` (Number of digits)
- **Space Complexity:** `O(log10(N))`