# Contains Duplicate — UMPIRE Method

## Problem

Given an integer array `nums`, return `true` if any value appears at least twice in the array, and return `false` if every element is distinct.

**Constraints**
- `1 <= nums.length <= 10^5`
- `-10^9 <= nums[i] <= 10^9`

---

## U — Understand

### What is the problem asking?

I need to check whether **any number appears more than once**.

- If a duplicate exists → return `True`
- If every number is unique → return `False`
- There is only one output: a boolean

### Examples

```text
nums = [1, 2, 3, 1]
→ True
```

Because `1` appears twice.

```text
nums = [1, 2, 3, 4]
→ False
```

Because every element is distinct.

### Key question

> Have I seen the same value more than once?

---

## M — Match

This looks like a **lookup / hashing** problem.

Words that should make me think about hashing:

- duplicate
- repeated
- seen before
- unique
- distinct

Because I only care whether a value already exists, a **set** is a good data structure.

### Pattern

```text
"Have I seen this VALUE before?"
            ↓
           SET
```

### Brute Force

I could compare every element with every other element.

```python
for i in range(len(nums)):
    for j in range(i + 1, len(nums)):
        if nums[i] == nums[j]:
            return True
```

That would take **O(n²)** time.

Since `nums.length` can be `10^5`, I should look for a faster solution.

---

## P — Plan

A Python `set` only keeps unique values.

For example:

```python
nums = [1, 1, 2]

set(nums)
# {1, 2}
```

So I can:

1. Convert `nums` into a set.
2. Compare the length of the original list with the length of the set.
3. If the lengths are different, a duplicate was removed → return `True`.
4. If the lengths are equal, every value was unique → return `False`.

### Pseudocode

```text
unique_numbers = set(nums)

if length(nums) != length(unique_numbers):
    return True

return False
```

---

## I — Implement

### My Solution

```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        final = set(nums)

        if len(nums) != len(final):
            return True
        else:
            return False
```

### Shorter Version

```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        return len(nums) != len(set(nums))
```

---

## R — Review

### Dry Run 1

```text
nums = [1, 1, 2]

set(nums) = {1, 2}

len(nums) = 3
len(set(nums)) = 2

3 != 2 → True
```

Correct: there is a duplicate.

### Dry Run 2

```text
nums = [1, 2, 3, 4]

set(nums) = {1, 2, 3, 4}

len(nums) = 4
len(set(nums)) = 4

4 != 4 → False
```

Correct: every element is distinct.

### Edge Case

```text
nums = [1]

set(nums) = {1}

1 == 1 → False
```

Correct: one element cannot contain a duplicate.

---

## E — Evaluate

### Time Complexity

**O(n)** average time.

Creating `set(nums)` requires processing the elements in `nums`.

### Space Complexity

**O(n)**.

In the worst case, every value is unique and the set contains all `n` elements.

```text
Time:  O(n)
Space: O(n)
```

---

## Mistakes I Made

### 1. I confused `tuple` with `set`

My original idea used:

```python
tuple(nums)
```

I thought a tuple would remove duplicates.

It does **not**.

```python
tuple([1, 1, 2])
# (1, 1, 2)
```

A tuple keeps duplicates.

A **set** removes duplicates:

```python
set([1, 1, 2])
# {1, 2}
```

### 2. I initially reversed the return logic

If:

```python
len(nums) != len(set(nums))
```

the length changed because duplicate values were removed.

Therefore the answer must be:

```python
True
```

### 3. I initially thought the runtime was O(1)

`set(nums)` must process the input, so the solution is:

```text
O(n) time
O(n) space
```

---

## Interview Pressure Rule

When I see:

```text
duplicate
repeated
seen before
unique
distinct
```

I should immediately ask:

> Can I use a hash set?

### Contains Duplicate vs Two Sum

```text
Contains Duplicate:
"Have I seen THIS VALUE before?"
→ Set

Two Sum:
"Have I seen the COMPLEMENT, and what is its index?"
→ Dictionary
```

---

## Interview Explanation

> Brute force would compare pairs of elements and take O(n²) time. Since I only need to know whether a duplicate exists, I can use a hash set. A set stores unique values, so if the set has fewer elements than the original array, a duplicate must have existed. Building the set takes O(n) average time and O(n) space.

---

## Final Takeaway

```text
Duplicate / repeated value
          ↓
"Have I seen this before?"
          ↓
        Hashing
          ↓
          Set
          ↓
 O(n) time / O(n) space
```

**Pattern to remember:** if the question is asking whether a value has been **seen before**, think **hash set**.
