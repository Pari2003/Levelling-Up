# 08. Product of Array

## Intuition
Similar to factorial, but we iterate over an array via index `i`.

## Code
```python
def product_arr(arr, i):
    if i == len(arr):
        return 1  # Multiplicative identity
    return arr[i] * product_arr(arr, i + 1)
```

## Complexity
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)`