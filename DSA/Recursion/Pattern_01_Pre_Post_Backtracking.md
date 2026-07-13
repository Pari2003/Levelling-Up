# Pattern 1: Preorder, Postorder & Backtracking

When a recursive function is called, the execution halts, pushes the current state to the call stack, and dives into the new function. Understanding *when* your code executes relative to that dive is crucial.

## 1. Preorder (Going Down)
Code executes **before** the recursive call.
```python
def preorder(n):
    if n == 0:
        return
    print(n)          # ACTION BEFORE
    preorder(n - 1)   # RECURSIVE CALL
```
*Use Case:* Passing information from parent to child.

## 2. Postorder (Coming Up)
Code executes **after** the recursive call returns.
```python
def postorder(n):
    if n == 0:
        return
    postorder(n - 1)  # RECURSIVE CALL
    print(n)          # ACTION AFTER (While returning)
```
*Use Case:* Gathering information from children to build the parent's answer (e.g., height of a tree).

## 3. Backtracking
Backtracking is postorder with state restoration. You modify a shared state, make the recursive dive, and when you return, you undo the modification.
```python
def backtrack(n, path):
    if n == 0:
        print(path)
        return
    
    path.append(n)       # ACTION (Modify state)
    backtrack(n - 1, path) # DIVE
    path.pop()           # UNDO ACTION (Backtrack)
```
*Use Case:* Finding all permutations/combinations where you reuse a single array to save space.