# Pattern 5: Counting Recursion

When a problem asks "Count the number of ways...", use the Counting Pattern.

## The Rule
1. If the base case represents a **valid** path/solution, `return 1`.
2. If the base case represents an **invalid** path, `return 0`.
3. The recursive step should `sum` up the results of all its recursive calls.

```python
def count_ways(state):
    # Base cases
    if state_is_invalid: return 0
    if state_is_target: return 1
    
    # Sum up all possible next steps
    count = 0
    for choice in choices:
        count += count_ways(new_state(state, choice))
        
    return count
```