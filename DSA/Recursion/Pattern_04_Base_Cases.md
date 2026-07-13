# Pattern 4: Base Cases & Identity Elements

The base case prevents infinite recursion (a `RecursionError` / Stack Overflow). It represents the smallest, most trivial subproblem that can be answered immediately.

## Choosing the Right Base Case

Look at the operation you are performing. Your base case should return the **Identity Element** of that operation.

| Operation | Identity Element | Base Case Return Value |
| :--- | :--- | :--- |
| Addition / Sum | `x + 0 = x` | `return 0` |
| Multiplication | `x * 1 = x` | `return 1` |
| Finding Maximum | `max(x, -inf) = x` | `return float('-inf')` |
| Finding Minimum | `min(x, inf) = x` | `return float('inf')` |

## Trivial Subproblems
- **Reversing Array / Palindrome:** An array of size 0 or 1 is already reversed/palindromic. Base condition: `left >= right`.
- **Sorting:** An array of size 1 is already sorted.