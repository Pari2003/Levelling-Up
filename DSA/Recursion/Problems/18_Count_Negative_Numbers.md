# 18. Count Negative Numbers

## Code
```python
def count_negative(arr, i):
    if i == len(arr):
        return 0
    return (1 if arr[i] < 0 else 0) + count_negative(arr, i + 1)
```