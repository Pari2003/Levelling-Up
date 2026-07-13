# 19. Count Occurrences of X

## Intuition
Similar to counting odd/even, but our condition is whether `arr[i] == x`.

## Code
```python
def count_x(arr, i, x):
    if i == len(arr):
        return 0
    return (1 if arr[i] == x else 0) + count_x(arr, i + 1, x)
```