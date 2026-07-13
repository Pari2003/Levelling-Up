# Pattern Recognition in Recursion

How do you know what pattern to use when you see a problem?

## Master Pattern Template

```python
def solve(problem):
    # 1. Base Case (When to stop)
    if is_base_case(problem):
        return trivial_answer

    # 2. Current Contribution (Work done at this node)
    current = get_contribution(problem)

    # 3. Recursive Call (Trust it solves the smaller problem)
    remaining = solve(smaller_problem)

    # 4. Combine (Join current work with recursive work)
    return combine(current, remaining)
```

## Pattern Selection Guide

1. **Do you just need to print or traverse?**
   - Use **Parameterized Recursion** (pass state down).
   - Use **Preorder** (action before call) or **Postorder** (action after call).
2. **Do you need a final computed value (Sum, Max, Min)?**
   - Use **Functional Recursion**. Return values up the tree.
3. **Do you need to explore ALL paths/combinations?**
   - Use **Multiple Recursion** (inside a loop or multiple explicit calls).
4. **Do you need to explore paths but reuse the same state (like a list)?**
   - Use **Backtracking**. Make change -> Recursive Call -> Undo change.
5. **Are you counting the number of valid paths?**
   - Use **Counting Recursion**. Base case returns `1` for valid, `0` for invalid. Return `sum` of calls.