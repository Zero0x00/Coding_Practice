# Intersection of Two Arrays — UMPIRE Revision Notes

## Problem

Given two integer arrays, return the **unique values that appear in both arrays**. The answer may be returned in any order.

### Example

```text
Input:  nums1 = [1, 2, 2, 1], nums2 = [2, 2]
Output: [2]
```

Although `2` appears more than once, it appears only once in the result.

---

## U — Understand

We need to find the **intersection** of the two arrays:

> Which unique numbers exist in both arrays?

This is a **lookup/membership problem**, but it is **not a frequency problem**.

- Membership question: “Does this number exist?” → use a `set`
- Frequency question: “How many times does this number occur?” → use a `dict` or `Counter`

---

## M — Match

A set is the best data structure because it:

- Removes duplicates automatically.
- Provides average `O(1)` membership lookup.
- Can store the unique matching values.

A `Counter` would work, but it stores frequencies that this problem does not require.

---

## P — Plan

1. Convert one array into a set named `lookup`.
2. Create an empty set named `result`.
3. Loop through the other array.
4. If the current number exists in `lookup`, add it to `result`.
5. Convert `result` to a list and return it.

---

## I — Implement

```python
class Solution:
    def intersection(
        self, nums1: List[int], nums2: List[int]
    ) -> List[int]:

        lookup = set(nums1)
        result = set()

        for number in nums2:
            if number in lookup:
                result.add(number)

        return list(result)
```

### Short Python version

Python sets support intersection directly with `&`:

```python
class Solution:
    def intersection(
        self, nums1: List[int], nums2: List[int]
    ) -> List[int]:
        return list(set(nums1) & set(nums2))
```

---

## R — Review the Three Planned Approaches

### Approach 1: Sets

Convert the arrays to sets and find the common values.

```python
return list(set(nums1) & set(nums2))
```

- Correct: Yes
- Average time: `O(n + m)`
- Space: `O(n + m)`
- Best feature: Simple and automatically removes duplicates

### Approach 2: Counter

```python
count1 = Counter(nums1)
count2 = Counter(nums2)
```

- Correct: Yes
- Average time: `O(n + m)`
- Space: `O(n + m)`
- Disadvantage: Stores frequencies even though they are unnecessary

Use `Counter` when duplicate counts matter, such as **Intersection of Two Arrays II**.

### Approach 3: Loop with List Membership

```python
result = []

for number in nums1:
    if number in nums2:
        result.append(number)
```

This approach has two problems:

1. It may add duplicate answers.
2. `number in nums2` takes `O(m)` because `nums2` is a list.

Repeating that lookup for `n` numbers produces `O(n × m)` time—not `O(n)`.

To fix it, convert the lookup list to a set and use a set for the result.

---

## E — Evaluate Complexity

For the implemented set-based solution:

- Creating `set(nums1)`: `O(n)` average
- Looping through `nums2`: `O(m)`
- Each set lookup: `O(1)` average
- Overall time: **`O(n + m)`** average
- Auxiliary space: **`O(n + r)`**, where `r` is the number of matching values

---

## Python Set Lessons

### Convert a list to a set

```python
numbers = [1, 2, 2, 3]
unique_numbers = set(numbers)

# {1, 2, 3}
```

### Convert a set to a list

```python
answer = list(unique_numbers)

# [1, 2, 3] — order may vary
```

We return a list because the method promises `List[int]`.

### Lists use `append`; sets use `add`

```python
my_list = []
my_list.append(5)
```

```python
my_set = set()
my_set.add(5)
```

- A list has an end, so an item is **appended** to it.
- A set has no positional end, so an item is simply **added**.
- Adding the same value repeatedly to a set still stores it only once.

```python
result = set()
result.add(2)
result.add(2)
result.add(2)

# {2}
```

### `i` versus `number`

Both are legal variable names and behave identically:

```python
for i in nums2:
    pass
```

```python
for number in nums2:
    pass
```

`number` is clearer because the variable contains a number from the array. By convention, `i` is often used when the variable represents an index.

---

## Interview Recognition Tip

When the problem asks:

> “Does this value exist in both collections?”

Think **set membership**.

When it asks:

> “How many times does this value occur?”

Think **dictionary or Counter**.

## Final Takeaway

> Intersection of Two Arrays is a lookup problem, not a frequency problem. Use a set for fast membership checking and automatic duplicate removal.
