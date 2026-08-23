# Boyer–Moore Voting Algorithm

## Goal

Find the element that appears **more than half** the time using:

- **Time:** `O(n)`
- **Extra space:** `O(1)`

Unlike `Counter`, Boyer–Moore does not store the frequency of every value. It finds the majority through **pairwise cancellation**.

---

## 1. The Big-Picture Analogy: A Battlefield

Imagine every number is a soldier representing its own army.

```text
[2, 2, 1, 1, 1, 2, 2]
```

There are:

- Four soldiers from army `2`
- Three soldiers from army `1`

Apply one rule:

> Whenever two soldiers from different armies meet, both are removed.

```text
2 vs 1 → both removed
2 vs 1 → both removed
2 vs 1 → both removed
```

After all three battles, one `2` remains.

The majority army must survive because it has more soldiers than **all other armies combined**. There are not enough enemies to eliminate it completely.

> **Core intuition:** A true majority element cannot be completely canceled by all the other elements.

---

## 2. The Mathematical Insight

Suppose:

- The array has `n` elements.
- The majority appears `m` times.
- All other values combined appear `n - m` times.

Because the element is a majority:

```text
m > n / 2
```

Therefore:

```text
m > n - m
```

Each cancellation removes:

- One majority occurrence
- One non-majority occurrence

After every possible cancellation, the remaining majority occurrences equal:

```text
m - (n - m)
```

Since `m > n - m`, this value is positive. At least one majority occurrence must survive.

### Fundamental truth

> If an element occupies more than half the array, removing pairs of different elements cannot remove every occurrence of that element.

Example:

```text
Original:       [A, A, A, A, B, B, C]
Cancel A and B: [A, A, A, B, C]
Cancel A and B: [A, A, C]
Cancel A and C: [A]
```

`A` survives.

---

## 3. The Two Variables

The algorithm uses only:

```python
candidate
count
```

- `candidate`: the army currently controlling the battlefield
- `count`: that candidate's current **unmatched advantage**

### Rules

1. If `count == 0`, make the current number the new candidate.
2. If the current number matches the candidate, increment `count`.
3. If it does not match, decrement `count`.

### Important

`count` is **not** the candidate's total frequency.

It represents the candidate's unmatched advantage after cancellations in the portion of the array processed so far.

When `count` becomes zero, the current candidate's support has been perfectly canceled. It does not mean that candidate never appeared.

---

## 4. Step-by-Step Trace

```text
nums = [2, 2, 1, 1, 1, 2, 2]
```

| Current number | Candidate | Count | Explanation |
|---:|---:|---:|---|
| `2` | `2` | `1` | Count was zero, so `2` becomes candidate; it then receives one vote |
| `2` | `2` | `2` | The number matches the candidate |
| `1` | `2` | `1` | A different number cancels one unmatched `2` |
| `1` | `2` | `0` | Another different number cancels the remaining advantage |
| `1` | `1` | `1` | Count was zero, so `1` becomes the new candidate |
| `2` | `1` | `0` | The `2` cancels the unmatched `1` |
| `2` | `2` | `1` | Count was zero, so `2` becomes the new candidate |

Final candidate: `2`

---

## 5. Deriving the Code

Translate the rules directly:

```text
No surviving army?       Choose the current army.
Same army as candidate?  Add one.
Different army?          Cancel one.
```

```python
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

### Complexity

- **Time:** `O(n)` — one pass through the array
- **Extra space:** `O(1)` — only `candidate` and `count`

---

## 6. The Gotcha: The Second Pass

Boyer–Moore finds the only element that **could** be the majority. If the problem does not guarantee that a majority exists, the result must be verified.

Consider:

```text
[1, 2, 3]
```

| Number | Candidate | Count |
|---:|---:|---:|
| `1` | `1` | `1` |
| `2` | `1` | `0` |
| `3` | `3` | `1` |

The algorithm produces `3`, but `3` occurs only once. It is not a majority.

This happens because some candidate may remain after cancellation even when no true majority exists.

### General version with verification

```python
def majority_element(nums):
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

The second pass does not change the Big-O complexity:

```text
O(n) + O(n) = O(2n) = O(n)
```

### When can verification be skipped?

Skip it when the problem explicitly guarantees:

> A majority element always exists.

Use verification when the problem says:

> Return the majority element if one exists; otherwise return `-1` or `None`.

---

## 7. Interview Recognition Pattern

Boyer–Moore should come to mind when you see all or most of these clues:

- Find an element appearing **more than `n / 2` times**.
- The required time complexity is `O(n)`.
- The required extra-space complexity is `O(1)`.
- A dictionary, `Counter`, or sorting would use too much space or time.

The phrase **“more than half”** is special. Only one value can satisfy it, and that value occurs more often than every other value combined.

### Interview derivation sentence

If you freeze, say:

> Because the majority occurs more often than all other values combined, I can cancel each occurrence against a different value, and the majority must survive. I only need to track the current survivor and its unmatched balance.

That sentence produces the variables naturally:

```text
current survivor  → candidate
unmatched balance → count
```

Then the update rules follow:

```text
count is zero → choose a candidate
same value    → count + 1
different     → count - 1
```

---

## Final Mental Model

Do not memorize Boyer–Moore as mysterious code.

Remember this:

> **Different values cancel each other. A true majority has more members than all opponents combined, so it must survive.**

