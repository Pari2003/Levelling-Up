# Pattern 8: Recursion Trees

A recursion tree is a visual representation of how a recursive function executes.

## Drawing the Tree
- Each node represents a function call.
- The children of a node represent the recursive calls made by that function.
- The leaves are the base cases.

## Calculating Complexity
**Time Complexity:** Look at the number of nodes. 
Roughly: `O(branches ^ depth)`
- For Fibonacci: 2 branches, depth N -> `O(2^N)`
- For Subsets: 2 branches, depth N -> `O(2^N)`
- For Permutations: N branches (decreasing), depth N -> `O(N!)`

**Space Complexity:** Look at the maximum depth of the tree (height).
The maximum number of active frames on the call stack at any one time is the distance from the root to the deepest leaf.
Roughly: `O(Depth)`