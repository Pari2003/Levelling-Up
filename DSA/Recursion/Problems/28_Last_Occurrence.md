# 28. Last Occurrence

## Intuition
Instead of returning immediately when we see the target, we first check if the target exists in the *rest* of the array.
If it does, we return that index (since it's further right). If it doesn't, we return the current index.

## Code
```python
def last_occurrence(arr, i, target):
    if i == len(arr):
        return -1
        
    # Check the rest of the array first! (Postorder)
    rest_ans = last_occurrence(arr, i + 1, target)
    
    if rest_ans != -1:
        return rest_ans
        
    if arr[i] == target:
        return i
        
    return -1
```

## Complexity
- **Time Complexity:** `O(N)`
- **Space Complexity:** `O(N)`