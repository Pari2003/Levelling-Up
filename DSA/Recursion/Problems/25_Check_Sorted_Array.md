# 25. Check Sorted Array

## Intuition
An array is sorted if `arr[i] <= arr[i+1]` AND the rest of the array is sorted.

## Code
```python
def is_sorted(arr, i):
    if i == len(arr) - 1:
        return True
    
    if arr[i] > arr[i + 1]:
        return False
        
    return is_sorted(arr, i + 1)
```