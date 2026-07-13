# 16. Count Odd Numbers

## Intuition
Use the counting pattern. Return 1 if odd, 0 if even, plus the count of the rest.

## Code
```python
def count_odd(arr, i):
    if i == len(arr):
        return 0
    return (1 if arr[i] % 2 != 0 else 0) + count_odd(arr, i + 1)
```

## Complexity
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)`