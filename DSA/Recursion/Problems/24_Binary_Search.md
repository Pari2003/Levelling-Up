# 24. Binary Search (Recursive)

## Intuition
Search in a sorted array by dividing it in half.
Base case 1: Element found (`arr[mid] == target`).
Base case 2: Element not found (`low > high`).

## Code
```python
def binary_search(arr, low, high, target):
    if low > high:
        return -1
        
    mid = low + (high - low) // 2
    if arr[mid] == target:
        return mid
    elif arr[mid] > target:
        return binary_search(arr, low, mid - 1, target)
    else:
        return binary_search(arr, mid + 1, high, target)
```

## Complexity
- **Time Complexity:** `O(log N)`
- **Space Complexity:** `O(log N)`