# 01. Print Name N Times

## Intuition
To print something N times, we maintain a counter `i`. We start at 1, print the name, and increment `i` until it exceeds N.

## Recursive Thinking
- **Base Case:** If `i > N`, we stop.
- **Recursive Step:** Print the name, then call `solve(i+1, N)`.

## Dry Run
`f(1, 3)`
- `1 <= 3` -> Print "Maitraiyee"
- calls `f(2, 3)`
  - `2 <= 3` -> Print "Maitraiyee"
  - calls `f(3, 3)`
    - `3 <= 3` -> Print "Maitraiyee"
    - calls `f(4, 3)`
      - `4 > 3` -> Base Case Hit. Return.

## Code
```python
def print_name(i, n, name):
    if i > n:
        return
    print(name)
    print_name(i + 1, n, name)

print_name(1, 5, "Maitraiyee")
```

## Complexity
- **Time Complexity:** `O(N)` since the function is called N times.
- **Space Complexity:** `O(N)` auxiliary space for the call stack.