# 14. Fibonacci Number

## Intuition
`Fib(N) = Fib(N-1) + Fib(N-2)`

## Recursive Tree
```text
                          fib(4)
                      /            \
                fib(3)              fib(2)
               /      \            /      \
          fib(2)     fib(1)    fib(1)    fib(0)
         /      \
     fib(1)    fib(0)
```

## Detailed Explanation
- **Why two recursive calls happen:** Because the formula inherently relies on the previous two terms. This is Multiple Recursion.
- **Why Time is O(2^n):** Every node branches into 2. The height is N. Total nodes ≈ `2^N`. This is exponential time.
- **Why Space is O(n):** The maximum active frames on the stack is the longest path from root to leaf, which is N.
- **Why recursion becomes slow:** Because of Overlapping Subproblems. `fib(2)` is computed completely multiple times. For `fib(50)`, `fib(2)` is computed millions of times. 
- **Motivation for Dynamic Programming:** We can store (memoize) `fib(2)` in an array or dictionary the first time we calculate it. Next time we need it, we fetch it in O(1) time.

## Code
```python
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)
```