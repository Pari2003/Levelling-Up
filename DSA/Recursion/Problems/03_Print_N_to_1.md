# 03. Print N to 1

## Intuition
This is the reverse of printing 1 to N. We can use a single parameter `N`, print it, and then recurse for `N-1`.

## Code (Single Parameter - Preorder)
```python
def print_n_to_1(n):
    if n < 1:
        return
    print(n)
    print_n_to_1(n - 1)

print_n_to_1(5)
```

## Backtracking version of Print N to 1
What if we are NOT allowed to use `n - 1` and print in Preorder? We can use Backtracking (Postorder)!
We start from 1, go up to N, and print *after* returning from the recursive call.
```python
def print_n_to_1_backtrack(i, n):
    if i > n:
        return
    print_n_to_1_backtrack(i + 1, n)
    print(i) # Prints while returning (from N down to 1)

print_n_to_1_backtrack(1, 5)
```

## Complexity
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)`