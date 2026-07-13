# 11. Count Even Numbers in Array

## Intuition
Use counting pattern. If the number is even, return `1 + recursive_call`. Else, `0 + recursive_call`.

## Code
```python
def count_evens(arr, i):
    if i == len(arr):
        return 0
    
    is_even = 1 if arr[i] % 2 == 0 else 0
    return is_even + count_evens(arr, i + 1)
```

## Complexity
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)`