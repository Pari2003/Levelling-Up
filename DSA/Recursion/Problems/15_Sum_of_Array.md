# 15. Sum of Array

## Intuition
The sum of an array is the first element plus the sum of the rest of the array.

## Code
```python
def sum_array(arr, i):
    if i == len(arr):
        return 0  # Identity element for addition
    return arr[i] + sum_array(arr, i + 1)
```

## Complexity
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)` auxiliary space.