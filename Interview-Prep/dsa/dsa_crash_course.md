# DSA Crash Course — 7 Topics to Cover 80% of Fresher Interviews

> You already know: **Arrays, Sorting, Recursion**. This guide adds the 7 remaining topics you need. Each topic has: concept → pattern → 3-5 practice problems with solutions.

---

## Topic 1: Strings

### Core Operations
```python
s = "hello world"
s.upper()          # "HELLO WORLD"
s.lower()          # "hello world"
s.split(" ")         # ["hello", "world"]
s.replace("hello", "hi")  # "hi world"
s[::-1]           # "dlrow olleh" (reverse)
s.find("world")       # 6 (index, -1 if not found)
s.count("l")         # 3
"".join(["a","b","c"])    # "abc"
ord("a")           # 97 (ASCII value)
chr(97)           # "a"

# Strings are IMMUTABLE in Python — you can't change them in-place
# Convert to list, modify, join back:
chars = list(s)
chars[0] = "H"
s = "".join(chars)      # "Hello world"
```

### Pattern: Frequency Counting (Very Common)
```python
from collections import Counter

def is_anagram(s1, s2):
  return Counter(s1) == Counter(s2)

print(is_anagram("listen", "silent")) # True
```

### Problems

**P1: Reverse Words in a String**
```
Input: " hello  world "
Output: "world hello"
```
```python
def reverse_words(s):
  return " ".join(s.split()[::-1])
```

**P2: Valid Palindrome (ignore non-alphanumeric)**
```
Input: "A man, a plan, a canal: Panama"
Output: True
```
```python
def is_palindrome(s):
  cleaned = "".join(c.lower() for c in s if c.isalnum())
  return cleaned == cleaned[::-1]
```

**P3: First Non-Repeating Character**
```
Input: "leetcode"
Output: 0 (index of 'l')
```
```python
def first_unique(s):
  freq = Counter(s)
  for i, c in enumerate(s):
    if freq[c] == 1:
      return i
  return -1
```

**P4: Longest Common Prefix**
```
Input: ["flower", "flow", "flight"]
Output: "fl"
```
```python
def longest_common_prefix(strs):
  if not strs:
    return ""
  prefix = strs[0]
  for s in strs[1:]:
    while not s.startswith(prefix):
      prefix = prefix[:-1]
      if not prefix:
        return ""
  return prefix
```

---

## Topic 2: HashMaps & Sets

### When to Use
- **HashMap (dict):** When you need key→value lookup in O(1). Frequency counting, two-sum, grouping.
- **Set:** When you need fast membership check (is X in the collection?) or removing duplicates.

### Core Operations
```python
# Dict
d = {}
d["key"] = "value"      # Insert/update O(1)
val = d.get("key", "default")# Safe get O(1)
"key" in d          # Check exists O(1)
del d["key"]         # Delete O(1)
d.keys(), d.values(), d.items()

# Set
s = set()
s.add(5)           # Add O(1)
s.remove(5)         # Remove (raises error if missing)
s.discard(5)         # Remove (no error if missing)
5 in s            # Check membership O(1)
s1 & s2           # Intersection
s1 | s2           # Union
s1 - s2           # Difference
```

### Problems

**P1: Two Sum** (Most asked problem in interviews)
```
Input: nums = [2, 7, 11, 15], target = 9
Output: [0, 1] (because nums[0] + nums[1] = 9)
```
```python
def two_sum(nums, target):
  seen = {} # value → index
  for i, num in enumerate(nums):
    complement = target - num
    if complement in seen:
      return [seen[complement], i]
    seen[num] = i
  return []
```
**Logic:** For each number, check if `target - number` already exists in our map. If yes, we found the pair.

**P2: Group Anagrams**
```
Input: ["eat", "tea", "tan", "ate", "nat", "bat"]
Output: [["eat","tea","ate"], ["tan","nat"], ["bat"]]
```
```python
from collections import defaultdict

def group_anagrams(strs):
  groups = defaultdict(list)
  for s in strs:
    key = "".join(sorted(s)) # sorted anagram = same key
    groups[key].append(s)
  return list(groups.values())
```

**P3: Contains Duplicate**
```python
def contains_duplicate(nums):
  return len(nums) != len(set(nums))
```

**P4: Intersection of Two Arrays**
```python
def intersection(nums1, nums2):
  return list(set(nums1) & set(nums2))
```

---

## Topic 3: Linked Lists

### What It Is
A chain of nodes where each node has a `value` and a pointer to the `next` node. Unlike arrays, no random access — must traverse from head.

```python
class ListNode:
  def __init__(self, val=0, next=None):
    self.val = val
    self.next = next
```

### Key Patterns
```python
# Traverse
def traverse(head):
  current = head
  while current:
    print(current.val)
    current = current.next

# Reverse (MOST ASKED linked list question)
def reverse(head):
  prev = None
  current = head
  while current:
    next_node = current.next  # Save next
    current.next = prev    # Reverse pointer
    prev = current       # Move prev forward
    current = next_node    # Move current forward
  return prev # New head
```

### Problems

**P1: Reverse a Linked List** 
```python
def reverse_list(head):
  prev, curr = None, head
  while curr:
    curr.next, prev, curr = prev, curr, curr.next
  return prev
```

**P2: Detect Cycle (Floyd's Tortoise & Hare)**
```python
def has_cycle(head):
  slow = fast = head
  while fast and fast.next:
    slow = slow.next
    fast = fast.next.next
    if slow == fast:
      return True
  return False
```
**Logic:** Slow moves 1 step, fast moves 2 steps. If there's a cycle, they'll meet.

**P3: Merge Two Sorted Lists**
```python
def merge(l1, l2):
  dummy = ListNode(0)
  curr = dummy
  while l1 and l2:
    if l1.val <= l2.val:
      curr.next = l1
      l1 = l1.next
    else:
      curr.next = l2
      l2 = l2.next
    curr = curr.next
  curr.next = l1 or l2
  return dummy.next
```

---

## Topic 4: Stacks & Queues

### Stack = LIFO (Last In, First Out)
```python
stack = []
stack.append(1)  # Push
stack.append(2)
stack.pop()    # Returns 2 (last in, first out)
stack[-1]     # Peek (see top without removing)
```

### Queue = FIFO (First In, First Out)
```python
from collections import deque
queue = deque()
queue.append(1)    # Enqueue (add to right)
queue.append(2)
queue.popleft()    # Dequeue (remove from left) → 1
```

### Problems

**P1: Valid Parentheses** (Very commonly asked)
```
Input: "({[]})"
Output: True
```
```python
def is_valid(s):
  stack = []
  mapping = {")": "(", "}": "{", "]": "["}
  for char in s:
    if char in mapping: # Closing bracket
      if not stack or stack[-1] != mapping[char]:
        return False
      stack.pop()
    else: # Opening bracket
      stack.append(char)
  return len(stack) == 0
```
**Logic:** Push opening brackets. When you see a closing bracket, check if the top of stack matches. If stack is empty at the end, all matched.

**P2: Min Stack (Stack that tracks minimum in O(1))**
```python
class MinStack:
  def __init__(self):
    self.stack = []
    self.min_stack = []
  
  def push(self, val):
    self.stack.append(val)
    if not self.min_stack or val <= self.min_stack[-1]:
      self.min_stack.append(val)
  
  def pop(self):
    val = self.stack.pop()
    if val == self.min_stack[-1]:
      self.min_stack.pop()
    return val
  
  def get_min(self):
    return self.min_stack[-1]
```

**P3: Implement Queue using Two Stacks**
```python
class MyQueue:
  def __init__(self):
    self.in_stack = []
    self.out_stack = []
  
  def push(self, x):
    self.in_stack.append(x)
  
  def pop(self):
    if not self.out_stack:
      while self.in_stack:
        self.out_stack.append(self.in_stack.pop())
    return self.out_stack.pop()
```

---

## Topic 5: Binary Search

### The Pattern
Only works on **sorted** data. Repeatedly halve the search space. O(log n).

```python
def binary_search(nums, target):
  left, right = 0, len(nums) - 1
  while left <= right:
    mid = (left + right) // 2
    if nums[mid] == target:
      return mid
    elif nums[mid] < target:
      left = mid + 1
    else:
      right = mid - 1
  return -1
```

### Problems

**P1: Search Insert Position**
```
Input: nums = [1,3,5,6], target = 5 → Output: 2
Input: nums = [1,3,5,6], target = 2 → Output: 1 (where it would be inserted)
```
```python
def search_insert(nums, target):
  left, right = 0, len(nums) - 1
  while left <= right:
    mid = (left + right) // 2
    if nums[mid] == target:
      return mid
    elif nums[mid] < target:
      left = mid + 1
    else:
      right = mid - 1
  return left # Insert position
```

**P2: First and Last Position of Target**
```python
def search_range(nums, target):
  def find(is_left):
    left, right, result = 0, len(nums) - 1, -1
    while left <= right:
      mid = (left + right) // 2
      if nums[mid] == target:
        result = mid
        if is_left:
          right = mid - 1 # Keep searching left
        else:
          left = mid + 1  # Keep searching right
      elif nums[mid] < target:
        left = mid + 1
      else:
        right = mid - 1
    return result
  return [find(True), find(False)]
```

**P3: Square Root (without using sqrt)**
```python
def sqrt(x):
  left, right = 0, x
  while left <= right:
    mid = (left + right) // 2
    if mid * mid == x:
      return mid
    elif mid * mid < x:
      left = mid + 1
      result = mid # Floor value
    else:
      right = mid - 1
  return result
```

---

## Topic 6: Basic Trees

### Binary Tree Node
```python
class TreeNode:
  def __init__(self, val=0, left=None, right=None):
    self.val = val
    self.left = left
    self.right = right
```

### Three Traversals (Memorize These)
```python
# Inorder: Left → Root → Right (gives sorted order for BST)
def inorder(root):
  if not root:
    return []
  return inorder(root.left) + [root.val] + inorder(root.right)

# Preorder: Root → Left → Right
def preorder(root):
  if not root:
    return []
  return [root.val] + preorder(root.left) + preorder(root.right)

# Postorder: Left → Right → Root
def postorder(root):
  if not root:
    return []
  return postorder(root.left) + postorder(root.right) + [root.val]

# Level Order (BFS) — uses queue
from collections import deque
def level_order(root):
  if not root:
    return []
  result, queue = [], deque([root])
  while queue:
    level = []
    for _ in range(len(queue)):
      node = queue.popleft()
      level.append(node.val)
      if node.left: queue.append(node.left)
      if node.right: queue.append(node.right)
    result.append(level)
  return result
```

### Problems

**P1: Maximum Depth of Binary Tree** 
```python
def max_depth(root):
  if not root:
    return 0
  return 1 + max(max_depth(root.left), max_depth(root.right))
```

**P2: Invert Binary Tree** (famously asked at Google)
```python
def invert_tree(root):
  if not root:
    return None
  root.left, root.right = invert_tree(root.right), invert_tree(root.left)
  return root
```

**P3: Check if Tree is Symmetric**
```python
def is_symmetric(root):
  def is_mirror(t1, t2):
    if not t1 and not t2: return True
    if not t1 or not t2: return False
    return (t1.val == t2.val and 
        is_mirror(t1.left, t2.right) and 
        is_mirror(t1.right, t2.left))
  return is_mirror(root, root)
```

**P4: Validate BST**
```python
def is_valid_bst(root, min_val=float('-inf'), max_val=float('inf')):
  if not root:
    return True
  if root.val <= min_val or root.val >= max_val:
    return False
  return (is_valid_bst(root.left, min_val, root.val) and
      is_valid_bst(root.right, root.val, max_val))
```

---

## Topic 7: Two Pointers & Sliding Window

### Two Pointers Pattern
Two pointers moving towards each other (or in the same direction) to solve problems in O(n).

**P1: Two Sum II (Sorted Array)** — Classic two-pointer
```python
def two_sum_sorted(numbers, target):
  left, right = 0, len(numbers) - 1
  while left < right:
    total = numbers[left] + numbers[right]
    if total == target:
      return [left + 1, right + 1]
    elif total < target:
      left += 1
    else:
      right -= 1
```

**P2: Remove Duplicates from Sorted Array (in-place)**
```python
def remove_duplicates(nums):
  if not nums:
    return 0
  slow = 0
  for fast in range(1, len(nums)):
    if nums[fast] != nums[slow]:
      slow += 1
      nums[slow] = nums[fast]
  return slow + 1
```

**P3: Container With Most Water**
```python
def max_area(height):
  left, right = 0, len(height) - 1
  max_water = 0
  while left < right:
    width = right - left
    h = min(height[left], height[right])
    max_water = max(max_water, width * h)
    if height[left] < height[right]:
      left += 1
    else:
      right -= 1
  return max_water
```

### Sliding Window Pattern
Fixed or variable size window that slides across an array. Avoids recomputing from scratch.

**P4: Maximum Sum Subarray of Size K (Fixed Window)**
```python
def max_sum_subarray(nums, k):
  window_sum = sum(nums[:k])
  max_sum = window_sum
  for i in range(k, len(nums)):
    window_sum += nums[i] - nums[i - k] # Slide: add right, remove left
    max_sum = max(max_sum, window_sum)
  return max_sum
```

**P5: Longest Substring Without Repeating Characters** (Variable Window)
```python
def length_of_longest_substring(s):
  char_set = set()
  left = 0
  max_len = 0
  for right in range(len(s)):
    while s[right] in char_set:
      char_set.remove(s[left])
      left += 1
    char_set.add(s[right])
    max_len = max(max_len, right - left + 1)
  return max_len
```

---

## Quick Reference — When to Use What

| Problem Pattern | Data Structure/Technique |
|:---------------|:------------------------|
| "Find pair that sums to X" | HashMap (unsorted) or Two Pointers (sorted) |
| "Find duplicates" | Set or HashMap |
| "Group similar items" | HashMap with sorted key |
| "Check matching brackets" | Stack |
| "Find in sorted array" | Binary Search |
| "Tree traversal" | Recursion (DFS) or Queue (BFS) |
| "Subarray/substring of size K" | Sliding Window |
| "Reverse a linked list" | Three pointers (prev, curr, next) |
| "Detect cycle" | Fast/slow pointers |
| "Find min/max depth" | Recursion |
