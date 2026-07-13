# 27. First Occurrence

## Intuition
Traverse from `0` to `N`. The first time we see the target, return the index.

## Code
```python
def first_occurrence(arr, i, target):
    if i == len(arr):
        return -1
    if arr[i] == target:
        return i
    return first_occurrence(arr, i + 1, target)
```