# 04. Print 1 to N (Using Backtracking)

## Intuition
Normally, we go `1 -> N` and print along the way.
In backtracking, we go `N -> 1` without printing. When we hit the base case and start returning (unwinding the stack), we print. Since the last call was 1, 1 is printed first, then 2, etc.

## Code
```python
def print_1_to_n_backtrack(i, n):
    if i < 1:
        return
    print_1_to_n_backtrack(i - 1, n)
    print(i)

print_1_to_n_backtrack(5, 5)
```

## Dry Run
`f(5, 5)`
- `f(4, 5)`
  - `f(3, 5)`
    - `f(2, 5)`
      - `f(1, 5)`
        - `f(0, 5)` -> Base case. Return.
      - print(1). Return.
    - print(2). Return.
  - print(3). Return.
...and so on.

## Complexity
- **Time:** `O(N)`
- **Space:** `O(N)`