# Pattern 9: Tail vs Non-Tail Recursion

## Tail Recursion
A recursive function is "tail-recursive" if the recursive call is the VERY LAST operation executed in the function. Nothing happens after it returns.

```python
def tail_fact(n, accumulator=1):
    if n == 0: return accumulator
    # The return value is exactly the recursive call.
    return tail_fact(n - 1, n * accumulator)
```
*Advantage:* Some languages (like C++) optimize this so it uses `O(1)` space instead of `O(N)`. Python **does not** support tail call optimization.

## Non-Tail Recursion
There is still work to do after the recursive call returns.
```python
def non_tail_fact(n):
    if n == 0: return 1
    # Must wait for call to finish, then multiply by n
    return n * non_tail_fact(n - 1)
```