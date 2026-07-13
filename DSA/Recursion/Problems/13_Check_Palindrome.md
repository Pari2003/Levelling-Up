# 13. Check Palindrome

## Intuition
Compare first and last characters. If they match, recursively check the inner substring.

## Recursive Relation
`isPal(s, left, right) = (s[left] == s[right]) AND isPal(s, left+1, right-1)`

## Code
```python
def is_palindrome(s, left, right):
    if left >= right:
        return True
    if s[left] != s[right]:
        return False
    return is_palindrome(s, left + 1, right - 1)
```

## Complexity
- **Time:** `O(N/2)` = `O(N)`
- **Space:** `O(N/2)` = `O(N)`