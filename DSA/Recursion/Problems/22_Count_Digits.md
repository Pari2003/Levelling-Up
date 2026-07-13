# 22. Count Digits

## Code
```python
def count_digits(n):
    if n == 0:
        return 0
    return 1 + count_digits(n // 10)
```