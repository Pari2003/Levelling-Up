# Pattern 3: Recursive Thinking & The Faith Principle

Recursion is hard because humans want to trace every single execution step. For `f(10)`, you try to trace `f(9)`, `f(8)`, down to `f(0)`. Your brain runs out of stack space!

## The Faith / Trust Principle
Stop tracing. Instead:
1. Assume (have faith) that your recursive function **already works** for smaller inputs.
2. Build the current step using that assumption.

**Example: Factorial**
1. Assume `factorial(n-1)` works perfectly and returns `(n-1)!`.
2. What is my job for `factorial(n)`?
3. My job is just `n * factorial(n-1)`.

If you establish the correct base case and correctly connect step `N` to step `N-1`, mathematics guarantees it will work for all cases.

## The Call Stack
- **Growing Phase:** As you make calls, stack frames are created. Memory usage grows to `O(Depth)`.
- **Shrinking Phase:** Once a base case is hit, functions return and frames are popped.
- **Why Stack and not Queue?** Because recursion resolves in LIFO (Last In First Out) order. The last function called is the first one to finish.