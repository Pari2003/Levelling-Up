# 17. Count Positive Numbers

## Code
```python
def count_positive(arr, i):
    if i == len(arr):
        return 0
    return (1 if arr[i] > 0 else 0) + count_positive(arr, i + 1)
```