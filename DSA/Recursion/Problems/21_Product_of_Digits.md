# 21. Product of Digits

## Intuition
Same as sum, but multiply. Be careful with `0` handling if the entire number is `0` (base case should probably return `1` to not kill the product).

## Code
```python
def product_digits(n):
    if n == 0:
        return 1
    return (n % 10) * product_digits(n // 10)
```