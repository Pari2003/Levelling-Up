# Pattern 7: Backtracking Problems

Backtracking is a form of recursion where you build a solution incrementally, and when you realize a partial solution cannot lead to a valid full solution, you abandon it ("backtrack") and try another path.

## The Core Concept: Reverting State
Why do work while returning? Because arrays and objects in Python are passed by reference.
If you append to a list and pass it down, the child modifies that SAME list in memory.
When the child finishes, the parent must `pop()` the element to return the list to its original state before trying the next branch.

```python
def find_subsets(index, arr, current_subset):
    if index == len(arr):
        print(current_subset)
        return
        
    # TAKE the element
    current_subset.append(arr[index])
    find_subsets(index + 1, arr, current_subset)
    
    # REVERT STATE (Backtrack)
    current_subset.pop()
    
    # DON'T TAKE the element
    find_subsets(index + 1, arr, current_subset)
```