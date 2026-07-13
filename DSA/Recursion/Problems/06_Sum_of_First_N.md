# 06. Sum of First N Numbers

## Intuition
Calculate `1 + 2 + ... + N`.

## Approaches
We can use Parameterized or Functional recursion. Functional is preferred.

### Parameterized
```python
def sum_param(i, current_sum):
    if i < 1:
        print(current_sum)
        return
    sum_param(i - 1, current_sum + i)
```

### Functional
```python
def sum_func(n):
    if n == 0:
        return 0
    return n + sum_func(n - 1)
```

## Complexity
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)`