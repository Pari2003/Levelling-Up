# 09. Maximum in Array

## Intuition
The max of an array is the maximum between the current element and the max of the remaining array.

## Code
```python
def max_arr(arr, i):
    if i == len(arr):
        return float('-inf')  # Identity for Maximum
    return max(arr[i], max_arr(arr, i + 1))
```

## Complexity
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)`