# 26. Linear Search (Recursive)

## Intuition
Check if `arr[i] == target`. If yes, return True. Otherwise, check the rest of the array.

## Code
```python
def linear_search(arr, i, target):
    if i == len(arr):
        return False
    if arr[i] == target:
        return True
    return linear_search(arr, i + 1, target)
```