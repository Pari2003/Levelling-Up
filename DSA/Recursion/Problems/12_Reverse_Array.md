# 12. Reverse Array

## Intuition
To reverse an array in-place, swap the first and last elements, then move pointers inward.

## Code: Two Pointers
```python
def reverse_array_two_pointers(arr, left, right):
    if left >= right:
        return arr
    arr[left], arr[right] = arr[right], arr[left]
    return reverse_array_two_pointers(arr, left + 1, right - 1)
```

## Code: One Pointer
```python
def reverse_array_one_pointer(arr, i):
    n = len(arr)
    if i >= n // 2:
        return arr
    arr[i], arr[n - i - 1] = arr[n - i - 1], arr[i]
    return reverse_array_one_pointer(arr, i + 1)
```

## Dry Run (One Pointer)
`arr = [1, 2, 3, 4, 5]`
- `i = 0`: swap `arr[0]` and `arr[4]`. `[5, 2, 3, 4, 1]`.
- `i = 1`: swap `arr[1]` and `arr[3]`. `[5, 4, 3, 2, 1]`.
- `i = 2`: `2 >= 5//2 (2)`. Base case hit. Return.

## Complexity
- **Time:** `O(N/2)` = `O(N)`
- **Space:** `O(N/2)` = `O(N)`