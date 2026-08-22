# Top K Frequent Elements — UMPIR(E) Revision Notes

## Problem

Given an integer array `nums` and an integer `k`, return the `k` most frequent elements. The answer may be returned in any order.

### Example

```text
Input:  nums = [1, 1, 1, 2, 2, 3], k = 2
Output: [1, 2]
```

---

## U — Understand

We need to:

1. Count how many times every number occurs.
2. Find the `k` numbers with the highest frequencies.
3. Return the numbers, not their frequencies.

Important observations:

- Duplicate values make this a **frequency-counting problem**.
- We repeatedly need the largest frequency, which suggests a **heap**.
- Python's `heapq` is a **min-heap**.
- To simulate a max-heap, store each frequency as a negative number.
- We negate the **frequency**, not the original number, because frequency determines priority.

---

## M — Match

This problem combines two familiar patterns:

- `Counter` or dictionary → count frequencies
- Heap → retrieve the highest-priority items

We store tuples in this form:

```python
(-frequency, number)
```

The first tuple value controls heap priority.

For example:

```python
(-3, 1)
```

means that number `1` occurs `3` times.

---

## P — Plan

1. Build a frequency map using `Counter(nums)`.
2. Create a list called `heap`.
3. For every `(number, frequency)` pair, add `(-frequency, number)`.
4. Convert the list into a heap with `heapq.heapify()`.
5. Pop from the heap exactly `k` times.
6. Add each popped number to `result`.
7. Return `result`.

---

## I — Implement

```python
from collections import Counter
import heapq
from typing import List


class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        frequency = Counter(nums)

        heap = []

        for number, count in frequency.items():
            heap.append((-count, number))

        heapq.heapify(heap)

        result = []

        for _ in range(k):
            negative_count, number = heapq.heappop(heap)
            result.append(number)

        return result
```

Because `negative_count` is not used after popping, we can write the shorter version:

```python
for _ in range(k):
    _, number = heapq.heappop(heap)
    result.append(number)
```

---

## Tuple Unpacking

Suppose `heappop()` returns:

```python
(-3, 1)
```

This:

```python
negative_count, number = heapq.heappop(heap)
```

is equivalent to:

```python
item = heapq.heappop(heap)
negative_count = item[0]
number = item[1]
```

Python assigns tuple values by **position**, not by variable name:

```text
first variable  ← first value
second variable ← second value
```

The names are our choice. These all work:

```python
count, number = (-3, 1)
x, y = (-3, 1)
banana, apple = (-3, 1)
```

Good variable names are used because they explain what each value represents.

The number of variables must match the number of values:

```python
a, b = (-3, 1)       # Works
a = (-3, 1)          # Works: a stores the entire tuple
a, b, c = (-3, 1)    # Error: not enough values
```

### Meaning of `_`

This loop:

```python
for _ in range(k):
```

means: repeat the operation `k` times, but we do not need the loop number.

It behaves like:

```python
for i in range(k):
```

The `_` is an ordinary valid variable name, but Python programmers conventionally use it to mean:

> This value exists, but I do not need it.

The same idea appears here:

```python
_, number = heapq.heappop(heap)
```

We ignore the frequency and keep the number.

---

## R — Review With a Dry Run

```python
nums = [1, 1, 1, 2, 2, 3]
k = 2
```

### Step 1: Count frequencies

```python
frequency = {
    1: 3,
    2: 2,
    3: 1
}
```

### Step 2: Build the heap list

```python
[
    (-3, 1),
    (-2, 2),
    (-1, 3)
]
```

### Step 3: Pop `k = 2` times

First pop:

```python
(-3, 1)
result = [1]
```

Second pop:

```python
(-2, 2)
result = [1, 2]
```

Final answer:

```python
[1, 2]
```

---

## E — Evaluate Complexity

Let:

- `n` = total number of elements
- `m` = number of unique elements

### Time

- Build the `Counter`: `O(n)`
- Build the heap list: `O(m)`
- Heapify: `O(m)`
- Pop `k` times: `O(k log m)`

Total:

```text
O(n + k log m)
```

### Space

The counter and heap each store at most `m` unique elements:

```text
O(m)
```

---

## Mistakes From the First Attempt

### 1. Negating the original numbers

Incorrect:

```python
nums = [-x for x in nums]
```

This prioritizes numbers by numerical value. We need to prioritize them by frequency.

Correct:

```python
heap.append((-count, number))
```

### 2. Heapifying a `Counter`

Incorrect:

```python
heapq.heapify(frequency)
```

`Counter` is dictionary-like, while `heapify()` requires a list.

Correct:

```python
heap = [(-count, number) for number, count in frequency.items()]
heapq.heapify(heap)
```

### 3. Using `len(k)`

Incorrect:

```python
for i in range(len(k)):
```

`k` is an integer and has no length.

Correct:

```python
for _ in range(k):
```

### 4. Using separate `first` and `second` variables

The value of `k` is not always two. Use a result list that works for any valid `k`:

```python
result = []

for _ in range(k):
    _, number = heapq.heappop(heap)
    result.append(number)
```

---

## Interview Pressure Rule

When you see:

> Return the `k` most frequent elements

Think:

```text
Frequency → Counter
Top K → Heap
Max priority in Python → Negative frequency
Heap item → (-frequency, number)
Pop exactly k times
```

The most important principle is:

> In a heap tuple, the first value is the priority. Put the property you want to rank by first.

