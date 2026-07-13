# 05. Print N to 1 (Using Backtracking)

## Intuition
Covered in Problem 03. By moving from 1 to N, and printing *after* the recursive call, we print N down to 1.

## Code
```python
def print_n_to_1_backtrack(i, n):
    if i > n:
        return
    print_n_to_1_backtrack(i + 1, n)
    print(i)

print_n_to_1_backtrack(1, 5)
```