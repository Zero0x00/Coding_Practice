# Last Stone Weight — UMPIRE Method

## Problem Summary

You are given an array of stone weights.

On each turn:

- Choose the **two heaviest stones**.
- If they are equal, both are destroyed.
- If they are different, the lighter stone is destroyed and the heavier stone becomes the difference.
- Continue until at most one stone remains.
- Return the remaining stone's weight, or `0` if no stones remain.

---

# U — Understand

The key phrase is:

> **Repeatedly take the two heaviest elements.**

That strongly suggests using a **heap / priority queue**.

The process repeats until fewer than two stones remain.

---

# M — Match

Python's `heapq` is a **min-heap**.

That means:

```python
heapq.heappop(heap)
```

returns the **smallest** value.

But this problem needs the **largest** values.

So we negate every stone:

```python
stones = [-x for x in stones]
```

Example:

```text
Original: [2, 7, 4, 8]

Negated:  [-2, -7, -4, -8]
```

Now the most negative number represents the largest original stone.

### Pattern to remember

```text
Repeatedly need smallest
→ normal heapq

Repeatedly need largest
→ negate values + heapq
```

---

# P — Plan

1. Convert every stone weight to negative.
2. Turn the list into a heap using `heapq.heapify()`.
3. While there are at least two stones:
   - Pop the heaviest stone.
   - Pop the second-heaviest stone.
   - Convert both back to positive.
4. If they are different:
   - Compute their difference.
   - Negate the difference.
   - Push it back into the heap.
5. When the loop ends:
   - If the heap is empty, return `0`.
   - Otherwise return the remaining stone as a positive number.

---

# I — Implement

```python
class Solution:
    def lastStoneWeight(self, stones: List[int]) -> int:
        stones = [-x for x in stones]
        heapq.heapify(stones)

        while len(stones) > 1:
            first = -heapq.heappop(stones)
            second = -heapq.heappop(stones)

            if first != second:
                new = first - second
                heapq.heappush(stones, -new)

        if len(stones) == 0:
            return 0

        return -stones[0]
```

---

# R — Review

Suppose:

```text
stones = [2, 7, 4, 1, 8, 1]
```

The two heaviest stones are:

```text
8 and 7
```

After popping:

```text
8 - 7 = 1
```

Push `1` back.

Next:

```text
4 and 2
→ 4 - 2 = 2
```

Push `2` back.

Continue until fewer than two stones remain.

---

## Important `heappop()` Concept

This:

```python
first = -heapq.heappop(stones)
```

does **two things**:

```text
REMOVE + RETURN
```

So when we write:

```python
first = -heapq.heappop(stones)
second = -heapq.heappop(stones)
```

both stones are already removed.

If:

```python
first == second
```

we do nothing because both stones are already destroyed.

If:

```python
first != second
```

we push the difference back:

```python
heapq.heappush(stones, -(first - second))
```

---

## Important `heapify()` Concept

This is correct:

```python
heapq.heapify(stones)
```

`heapify()` modifies the list **in place**.

This is wrong:

```python
heapstone = heapq.heapify(stones)
```

because `heapify()` returns:

```python
None
```

Mental model:

```text
heapq.heapify(stones)

means:

"Turn this existing list into a heap."
```

---

## Important Heap Index Rule

Do **not** assume:

```python
stones[1]
```

is the second-smallest or second-largest element.

A heap only guarantees:

```text
heap[0] = minimum element
```

For the next priority element, use another:

```python
heapq.heappop()
```

So for the two largest stones:

```python
first = -heapq.heappop(stones)
second = -heapq.heappop(stones)
```

---

## Important While Loop Concept

The loop is:

```python
while len(stones) > 1:
```

Why?

Because we can only smash stones when at least **two stones remain**.

```text
5 stones → continue
4 stones → continue
3 stones → continue
2 stones → continue
1 stone  → stop
0 stones → stop
```

---

# E — Evaluate

## Heap Construction

```python
heapq.heapify(stones)
```

takes:

```text
O(n)
```

---

## While Loop

The loop can run up to roughly:

```text
O(n)
```

times.

But the work inside each iteration is not `O(1)`.

We perform heap operations:

```python
heapq.heappop(stones)   # O(log n)
heapq.heappop(stones)   # O(log n)
heapq.heappush(...)     # O(log n), sometimes
```

So one iteration is:

```text
O(log n)
```

The loop runs up to:

```text
O(n)
```

times.

Therefore:

```text
O(n) iterations × O(log n) work
= O(n log n)
```

Including heapify:

```text
O(n) + O(n log n)
= O(n log n)
```

### Final Complexity

```text
Time:  O(n log n)
Space: O(n)
```

---

# Interview Takeaway

When you see:

> **Repeatedly choose the largest or smallest element**

think:

```text
Heap / Priority Queue
```

When you see:

> **Repeatedly choose the two largest elements**

think:

```text
pop
pop
process
maybe push result back
```

For Python:

```text
Need smallest → normal heapq

Need largest → negate values
```

---

# Quick Memory Pattern

```text
MAX HEAP WITH PYTHON

1. Negate
2. Heapify
3. Pop
4. Pop
5. Process
6. Push result if needed
7. Repeat
```

For this problem:

```text
two heaviest
→ pop two
→ equal: do nothing
→ unequal: push difference
→ stop when heap size <= 1
```
