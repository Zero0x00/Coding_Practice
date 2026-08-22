# Python `deque` and `heapq` — Interview Revision Notes

A fast revision guide focused on **intuition, complexity, and practical usage**.

---

# 1. `deque` — Double-Ended Queue

## Why `list.pop(0)` is expensive

Python lists are stored like a continuous row of elements:

```text
[A][B][C][D][E]
 0  1  2  3  4
```

If we do:

```python
nums.pop(0)
```

we remove `A`, but then every remaining element must shift left:

```text
Before:
[A][B][C][D][E]

After removing A:
   [B][C][D][E]

Shift:
B -> index 0
C -> index 1
D -> index 2
E -> index 3
```

So:

```python
list.pop()      # O(1)
list.pop(0)     # O(n)
```

### Mental model

Think of a Python list like people sitting in a movie-theater row.

Removing the person at the end is easy.

Removing the person at the front forces everyone else to move.

---

# 2. Why `deque` exists

`deque` means:

> **Double-Ended Queue**

It is designed to efficiently add and remove elements from **both ends**.

```python
from collections import deque

q = deque()
```

## Core operations

```python
q.append(x)       # add to right
q.pop()           # remove from right

q.appendleft(x)   # add to left
q.popleft()       # remove from left
```

All of these are:

```text
O(1)
```

## Easy way to remember the method names

There are only two main verbs:

```text
append = ADD
pop    = REMOVE
```

Then add the direction when needed:

```text
appendleft
popleft
```

---

# 3. Queue / FIFO Pattern

For a normal queue:

```text
FIRST IN -> FIRST OUT
```

Example:

```text
process <- [A][B][C] <- new item
```

Use:

```python
q.append(x)
q.popleft()
```

Example:

```python
from collections import deque

q = deque(["Arif", "Bob"])
q.append("Sarah")

print(q.popleft())
```

Output:

```text
Arif
```

## Common interview signal

If the problem says:

- FIFO
- process in arrival order
- BFS
- queue
- remove from front repeatedly

Think:

```text
deque
```

---

# 4. Min-Heap — Core Intuition

A heap is **not fully sorted**.

It maintains only enough order to make one important element easy to access.

For a **min-heap**:

```text
        1
      /   \
     4     2
    / \   /
   9   7  6
```

Heap rule:

```text
parent <= children
```

Because every parent is smaller than its children, the root is guaranteed to be the minimum.

So:

```python
heap[0]
```

is always the minimum.

---

# 5. Why a Heap is "Partially Sorted"

A heap does **not** guarantee:

```text
left side < right side
```

or:

```text
entire list is sorted
```

It only guarantees:

```text
parent <= children
```

For example:

```text
        1
      /   \
     4     2
```

`4 > 2`, and that is completely valid.

The heap does not care about ordering between different branches.

---

# 6. Heap Complexity

## Peek at minimum

```python
heap[0]
```

Complexity:

```text
O(1)
```

## Insert

```python
heapq.heappush(heap, x)
```

Complexity:

```text
O(log n)
```

The new element may need to move upward through the tree.

This is called:

```text
sift up
```

## Remove minimum

```python
heapq.heappop(heap)
```

Complexity:

```text
O(log n)
```

After removing the root, another element moves to the top and may move downward.

This is called:

```text
sift down
```

---

# 7. Why Heap Operations are `O(log n)`

A heap is a balanced binary tree.

Approximate height:

```text
log2(n)
```

Examples:

```text
1 element       -> ~0 levels
8 elements      -> ~3 levels
1,024 elements  -> ~10 levels
1,000,000       -> ~20 levels
```

So even with one million elements, an insertion/removal only needs to move through roughly 20 levels.

---

# 8. Python `heapq`

```python
import heapq
```

## Push

```python
heapq.heappush(heap, x)
```

## Pop minimum

```python
smallest = heapq.heappop(heap)
```

## Peek minimum

```python
smallest = heap[0]
```

---

# 9. Already Have a List?

Suppose:

```python
nums = [8, 3, 10, 1, 5]
```

If you only need the minimum or maximum **once**:

```python
min(nums)
max(nums)
```

Use those directly.

Do not create a heap unnecessarily.

---

# 10. Convert an Existing List to a Heap

```python
import heapq

nums = [8, 3, 10, 1, 5]

heapq.heapify(nums)
```

Important:

```python
heapq.heapify(nums)
```

rearranges the existing list into a valid heap.

It does **not** fully sort the list.

Complexity:

```text
O(n)
```

That is better than pushing every item one by one:

```text
O(n log n)
```

---

# 11. Min-Heap vs Maximum Value

For a min-heap:

```python
heap[0]
```

gives the minimum in:

```text
O(1)
```

But the maximum could be anywhere near the leaves.

So finding the max of a min-heap is generally:

```text
O(n)
```

---

# 12. Classic Max-Heap Trick

Python's classic `heapq` API behaves as a min-heap.

To simulate a max-heap, negate the values.

Example:

```python
import heapq

nums = [3, 8, 2, 10, 5]

heap = [-x for x in nums]
heapq.heapify(heap)

largest = -heapq.heappop(heap)

print(largest)
```

Output:

```text
10
```

## Why it works

Original:

```text
10, 7, 4
```

Negated:

```text
-10, -7, -4
```

The smallest negated value:

```text
-10
```

corresponds to the largest original value:

```text
10
```

---

# 13. Choosing Between `list`, `deque`, and `heap`

## Use a `list` when:

You need:

- indexing
- `arr[i]`
- iteration
- append/remove mostly at the end

Mental model:

```text
numbered shelf
```

---

## Use a `deque` when:

You need:

- queue behavior
- FIFO
- BFS
- frequent removal from the front
- efficient operations on both ends

Mental model:

```text
tunnel with two doors
```

---

## Use a `heap` when:

You repeatedly need:

- smallest element
- largest element
- priority processing
- top K elements
- scheduling based on priority

Mental model:

```text
priority podium
```

---

# 14. Important Interview Pattern: Top K

Suppose numbers arrive continuously:

```text
4, 10, 3, 8, 15, 2, ...
```

You want to keep only the:

```text
3 largest numbers
```

The best structure is:

```text
MIN-HEAP of size 3
```

This initially feels backwards.

Why use a **min-heap** when we want the **largest** numbers?

Because the heap root represents:

> the weakest member of our current winning group.

---

# 15. K Largest -> Min-Heap

Suppose current top 3 are:

```text
3, 4, 10
```

The minimum is:

```text
3
```

That is the weakest winner.

Now `8` arrives.

Compare:

```text
8 > 3
```

So remove `3` and insert `8`.

Now:

```text
4, 8, 10
```

Then `15` arrives:

```text
15 > 4
```

Remove `4`.

Now:

```text
8, 10, 15
```

Then `2` arrives:

```text
2 < 8
```

Ignore it.

Final top 3:

```text
8, 10, 15
```

---

# 16. The Key Top-K Rule

## K Largest

```text
K LARGEST
    |
    v
MIN-HEAP of size K
```

Why?

Because:

```text
heap[0] = smallest of the winners
```

That is the easiest winner to replace.

---

## K Smallest

```text
K SMALLEST
    |
    v
MAX-HEAP of size K
```

Why?

Because:

```text
root = largest of the winners
```

That is the easiest winner to replace.

---

# 17. Deep Mental Model

Do not think:

> "I want the largest numbers, so I need a max-heap."

For Top-K problems, instead think:

> "Which current winner should be easiest to eliminate?"

For `K largest`:

```text
eliminate the smallest winner
```

Therefore:

```text
min-heap
```

For `K smallest`:

```text
eliminate the largest winner
```

Therefore:

```text
max-heap
```

---

# 18. Sorting vs Heap

Suppose:

```python
nums = [4, 10, 3, 8, 15, 2]
```

If you need the top 3 **one time**, sorting is perfectly reasonable:

```python
nums.sort()

print(nums[-3:])
```

Output:

```text
[8, 10, 15]
```

Complexity:

```text
O(n log n)
```

---

If numbers are streaming or you only want to maintain `K` elements:

```text
min-heap of size K
```

Complexity:

```text
O(n log k)
```

This is often much better when:

```text
k << n
```

---

# 19. Python Slicing Reminder

To get the last 3 elements:

```python
nums[-3:]
```

To get the last 3 in reverse order:

```python
nums[-1:-4:-1]
```

Example:

```python
nums = [1, 2, 3, 4, 5]

print(nums[-3:])
```

Output:

```text
[3, 4, 5]
```

---

# 20. Quick Complexity Table

| Operation | Structure | Complexity |
|---|---|---:|
| `list.append(x)` | List | O(1) |
| `list.pop()` | List | O(1) |
| `list.pop(0)` | List | O(n) |
| `deque.append(x)` | Deque | O(1) |
| `deque.popleft()` | Deque | O(1) |
| `heap[0]` | Min-Heap | O(1) |
| `heappush()` | Heap | O(log n) |
| `heappop()` | Heap | O(log n) |
| `heapify()` | Heap | O(n) |
| `min(nums)` / `max(nums)` | List | O(n) |
| Sorting | List | O(n log n) |

---

# 21. Interview Recognition Cheat Sheet

When you see:

```text
FIFO
BFS
process in arrival order
remove from front
```

Think:

```text
deque
```

---

When you see:

```text
smallest repeatedly
largest repeatedly
priority
next most important
top K
K largest
K smallest
```

Think:

```text
heap
```

---

When you see:

```text
index
arr[i]
simple iteration
need min/max only once
```

Think:

```text
list
```

---

# 22. Final Mental Models

## List

```text
[A][B][C][D]
 0  1  2  3
```

> Numbered shelf.

---

## Deque

```text
LEFT <- [A][B][C][D] -> RIGHT
```

> Tunnel with two doors.

---

## Min-Heap

```text
        smallest
        /      \
     bigger    bigger
```

> A hierarchy that protects the smallest element.

---

# 23. Final Rules to Remember

```text
Queue / BFS
    ->
deque
```

```text
Repeated minimum
    ->
min-heap
```

```text
Repeated maximum
    ->
max-heap
```

```text
K largest
    ->
min-heap of size K
```

```text
K smallest
    ->
max-heap of size K
```

```text
Need min/max once
    ->
min() / max()
```

```text
Already have all values and need heap
    ->
heapify()
```

---

# One Sentence Summary

> A `deque` avoids expensive front-shifting, while a heap avoids the unnecessary cost of fully sorting data when you only care about priority.

