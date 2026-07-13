# 10. Minimum in Array

## Intuition
The min of an array is the minimum between the current element and the min of the remaining array.

## Code
```python
def min_arr(arr, i):
    if i == len(arr):
        return float('inf')  # Identity for Minimum
    return min(arr[i], min_arr(arr, i + 1))
```

## Complexity
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)`