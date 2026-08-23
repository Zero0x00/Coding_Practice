# Majority Element — LeetCode 169

## Problem

Given an integer array `nums`, return the **majority element**.

The majority element is the element that appears **more than** `n / 2` times, where `n = len(nums)`.

The problem guarantees that a majority element exists.

---

## UMPIR Method

### U — Understand

- Input: an integer array `nums`
- Output: the element occurring more than `n / 2` times
- A majority element is guaranteed to exist
- We need to return the element itself, not its index or frequency

### M — Match

The problem can be recognized as:

1. A **frequency-counting** problem, suggesting a dictionary or `Counter`
2. A **majority cancellation** problem, suggesting Boyer–Moore when `O(1)` extra space is required

### P — Plan

#### Counter plan

1. Count the frequency of every number.
2. Iterate through the number-frequency pairs.
3. Return the number whose frequency is greater than `n / 2`.

#### Boyer–Moore plan

1. Maintain a `candidate` and a `count`.
2. When `count` becomes zero, select the current number as the candidate.
3. Increase `count` when the current number matches the candidate.
4. Decrease `count` when it does not match—the two different elements cancel each other.
5. Return the surviving candidate.

---

## Method 1: Counter

```python
from collections import Counter
from typing import List


class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        for number, frequency in Counter(nums).items():
            if frequency > len(nums) // 2:
                return number
```

### Understanding the loop

```python
for number, frequency in Counter(nums).items():
```

`Counter(nums)` behaves like a dictionary:

```text
number → frequency
```

For example:

```python
nums = [2, 2, 1, 2]
Counter(nums)  # {2: 3, 1: 1}
```

Therefore:

- `number` is the dictionary key
- `frequency` is the dictionary value
- `.items()` produces `(key, value)` pairs

Since `frequency` already contains the count, either condition works:

```python
if frequency > len(nums) // 2:
```

```python
if counts[number] > len(nums) // 2:
```

The first form is cleaner when the frequency has already been unpacked.

### Complexity

- Time: **O(n)**
- Extra space: **O(n)** in the worst case

Writing `Counter(nums)` directly in the loop does not make the space `O(1)`. The complete frequency dictionary still exists in memory.

---

## Method 2: Boyer–Moore Voting Algorithm

```python
from typing import List


class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        candidate = None
        count = 0

        for number in nums:
            if count == 0:
                candidate = number

            if number == candidate:
                count += 1
            else:
                count -= 1

        return candidate
```

### Core intuition: cancellation

When the current number differs from the candidate, they cancel each other:

```text
one candidate + one different element → canceled pair
```

The majority element occurs more than all other elements combined. Therefore, even after canceling it against different elements, it cannot be completely eliminated.

### Trace

For:

```text
[2, 2, 1, 1, 1, 2, 2]
```

| Number | Action | Candidate | Count |
|---:|---|---:|---:|
| 2 | Count is zero; select 2, then match | 2 | 1 |
| 2 | Match | 2 | 2 |
| 1 | Different; cancel | 2 | 1 |
| 1 | Different; cancel | 2 | 0 |
| 1 | Count is zero; select 1, then match | 1 | 1 |
| 2 | Different; cancel | 1 | 0 |
| 2 | Count is zero; select 2, then match | 2 | 1 |

The surviving candidate is `2`.

### Complexity

- Time: **O(n)**
- Extra space: **O(1)**

Only `candidate`, `count`, and the current loop element are stored. The extra memory does not grow with the input.

---

## When Is Verification Required?

For LeetCode 169, verification is unnecessary because the problem guarantees that a majority element exists.

If that guarantee did not exist, Boyer–Moore would only produce a **possible candidate**. Verify it once after the first pass:

```python
from typing import List, Optional


class Solution:
    def majorityElement(self, nums: List[int]) -> Optional[int]:
        candidate = None
        count = 0

        for number in nums:
            if count == 0:
                candidate = number

            if number == candidate:
                count += 1
            else:
                count -= 1

        if nums.count(candidate) > len(nums) // 2:
            return candidate

        return None
```

The verification still gives:

- Time: **O(n) + O(n) = O(n)**
- Extra space: **O(1)**

Do not place `nums.count(candidate)` inside the loop. Since `.count()` is `O(n)`, calling it up to `n` times would make the algorithm `O(n²)`.

---

## Counter vs. Boyer–Moore

| Method | Time | Extra space | Main idea |
|---|---:|---:|---|
| `Counter` | O(n) | O(n) | Store every element's frequency |
| Boyer–Moore | O(n) | O(1) | Cancel different elements |

Use `Counter` when clarity is the priority and extra memory is allowed. Recognize Boyer–Moore when the problem says:

- Find an element occurring more than half the time
- Linear time is required
- Constant extra space is required

---

## Important Python Reminders

### `enumerate()`

```python
for index, element in enumerate(nums):
```

Produces:

```text
(index, element)
```

### Dictionary or Counter `.items()`

```python
for key, value in dictionary.items():
```

Produces:

```text
(key, stored value)
```

For a Counter:

```text
(element, frequency)
```

Memory rule:

> Need the position? Use `enumerate()`. Need key-value pairs? Use `.items()`.

---

## Interview Explanation

### Counter

> I will count the frequency of every number and return the one whose count is greater than half the array length. This takes O(n) time and O(n) extra space.

### Boyer–Moore

> I will cancel each occurrence of the candidate against a different element. Because the majority element appears more than half the time, it cannot be completely canceled and must be the final candidate. This takes O(n) time and O(1) extra space.

## Final Takeaway

```text
Counter      → count everything → O(n) space
Boyer–Moore  → cancel pairs     → O(1) space
```
