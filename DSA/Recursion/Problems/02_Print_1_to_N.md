# 02. Print 1 to N

## Intuition
We want to print numbers sequentially. We can pass the current number `i` and the target `N`.

## Recursive Thinking
### Single Parameter vs Two Parameters
- **Two Parameters:** `f(i, N)`. Base case: `i > N`.
- **Single Parameter:** `f(N)`. We can use backtracking (or postorder) to print 1 to N using only N. Let's look at the basic two parameter first.

## Code (Two Parameters)
```python
def print_1_to_n(i, n):
    if i > n:
        return
    print(i)
    print_1_to_n(i + 1, n)

print_1_to_n(1, 5)
```

## Complexity
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)` auxiliary call stack space.