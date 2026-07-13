# Recursion Code Examples

Clean, interview-ready Python implementations. No fluff.

## 1. Print Name N Times
```python
def print_name(i, n, name):
    if i > n:
        return
    print(name)
    print_name(i + 1, n, name)
```

## 2. Sum of First N (Functional)
```python
def sum_n(n):
    if n == 0:
        return 0
    return n + sum_n(n - 1)
```

## 3. Factorial
```python
def factorial(n):
    if n == 0 or n == 1:
        return 1
    return n * factorial(n - 1)
```

## 4. Reverse Array (Two Pointers)
```python
def reverse_array(arr, left, right):
    if left >= right:
        return arr
    arr[left], arr[right] = arr[right], arr[left]
    return reverse_array(arr, left + 1, right - 1)
```

## 5. Check Palindrome
```python
def is_palindrome(s, left, right):
    if left >= right:
        return True
    if s[left] != s[right]:
        return False
    return is_palindrome(s, left + 1, right - 1)
```

## 6. Fibonacci (Multiple Recursion)
```python
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)
```

## 7. Power(x, n)
```python
def power(x, n):
    if n == 0:
        return 1
    if n % 2 == 0:
        half = power(x, n // 2)
        return half * half
    else:
        return x * power(x, n - 1)
```

## 8. Binary Search Recursive
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