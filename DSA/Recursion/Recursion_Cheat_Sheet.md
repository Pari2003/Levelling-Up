# Recursion Cheat Sheet 📋

Review this 30 minutes before your interview.

## 1. Choosing a Base Case
- **Addition/Summation:** Return `0`
- **Multiplication/Product:** Return `1`
- **Max/Min:** Return `-infinity` or `infinity` (or `float('-inf')` / `float('inf')`)
- **Strings/Arrays:** If `len(s) == 0` or `start >= end`, return a trivial answer.

## 2. Recursive Relation & Master Pattern
Always build your function based on the **Faith / Trust Principle**.
```python
def solve(problem_state):
    # 1. Base Case
    if base_case_condition:
        return trivial_answer
    
    # 2. Current Contribution
    current = process(problem_state)
    
    # 3. Recursive Call (The Leap of Faith)
    remaining = solve(smaller_problem_state)
    
    # 4. Combine
    return combine(current, remaining)
```

## 3. Preorder vs Postorder vs Backtracking
- **Preorder:** `action() -> recursive_call()`
  - Best for: Printing path from root to leaf, accumulating state going down.
- **Postorder:** `recursive_call() -> action()`
  - Best for: Gathering results from children to parent (e.g., Tree heights).
- **Backtracking:** `action() -> recursive_call() -> undo_action()`
  - Best for: Exploring all combinations/permutations, returning state to original for the next branch.

## 4. Parameterized vs Functional
- **Parameterized:** Passing the answer parameter (like `sum`) down the recursive tree. The final base case prints or stores the answer.
- **Functional:** The function explicitly returns the answer (e.g., `return n + sum(n-1)`). The calling function combines the result.

## 5. Multiple Recursion & Counting
- **Multiple Calls:** `func(n-1) + func(n-2)` (e.g., Fibonacci). Time is `O(branches^depth)`. Space is `O(depth)`.
- **Counting:** Base case returns `1` (if successful) or `0` (if invalid). Return `sum` of all recursive calls.

## 6. Interview Checklist
- [ ] Did I write the Base Case first?
- [ ] Will the Base Case definitely be hit (no infinite loop)?
- [ ] What is the Time Complexity? `O(Branches ^ Depth)`
- [ ] What is the Space Complexity? `O(Max depth of recursive tree)` (Call Stack memory)
- [ ] Can this be optimized with DP (Memoization) due to overlapping subproblems?

## 7. Common Mistakes
- **Forgetting the base case:** Leads to `RecursionError: maximum recursion depth exceeded`.
- **Modifying global variables poorly:** E.g., `list.append()` without `.pop()` in backtracking.
- **`return` inside loops incorrectly:** Returning immediately inside a for loop instead of aggregating results.