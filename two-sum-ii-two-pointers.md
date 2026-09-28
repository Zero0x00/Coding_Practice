# Two Sum II — Input Array Is Sorted

LeetCode 167 is a classic **two-pointer** problem.

The array is already sorted in non-decreasing order, and we must find two numbers whose sum equals the target.

## Problem

Given:

```python
numbers = [2, 7, 11, 15]
target = 9
```

Return the **1-indexed** positions of the two numbers:

```python
[1, 2]
```

The solution must use **constant extra space**.

## The Umpire Analogy

Imagine an umpire standing between two players in a line of people:

- The left pointer starts at the person on the far left.
- The right pointer starts at the person on the far right.
- The numbers are sorted from smallest to largest.

The umpire adds the two numbers currently pointed to.

### If the sum is too small

The umpire needs a larger sum. Since the array is sorted, moving the `left` pointer to the right gives us a larger number.

```python
left += 1
```

### If the sum is too large

The umpire needs a smaller sum. Moving the `right` pointer to the left gives us a smaller number.

```python
right -= 1
```

### If the sum equals the target

The correct pair has been found.

This is why we do not need to try every possible pair. The sorted order tells us which pointer to move.

## Correct Solution

```python
class Solution:
    def twoSum(self, numbers: List[int], target: int) -> List[int]:
        left = 0
        right = len(numbers) - 1

        while left < right:
            current_sum = numbers[left] + numbers[right]

            if current_sum == target:
                # LeetCode requires 1-indexed positions
                return [left + 1, right + 1]

            elif current_sum < target:
                # The sum is too small, so increase it
                left += 1

            else:
                # The sum is too large, so decrease it
                right -= 1
```

## Step-by-Step Trace

Input:

```python
numbers = [2, 7, 11, 15]
target = 9
```

| `left` value | `right` value | Sum | Decision |
|---:|---:|---:|---|
| 2 | 15 | 17 | Too large → move `right` left |
| 2 | 11 | 13 | Too large → move `right` left |
| 2 | 7 | 9 | Found the answer |

The internal indexes are `0` and `1`, but the problem uses 1-based indexes. Therefore, return:

```python
[left + 1, right + 1]
```

## Why My Original Conditions Were Incorrect

This logic is not sufficient:

```python
elif target > numbers[left]:
elif target < numbers[right]:
```

These conditions compare the target with only one number. We need to compare the target with the **sum of both numbers**:

```python
current_sum = numbers[left] + numbers[right]
```

Then:

```python
if current_sum < target:
    left += 1
elif current_sum > target:
    right -= 1
```

## Can We Use a Hash Map?

Yes. This is similar to Two Sum I:

```python
class Solution:
    def twoSum(self, numbers: List[int], target: int) -> List[int]:
        seen = {}

        for index, number in enumerate(numbers):
            complement = target - number

            if complement in seen:
                return [seen[complement] + 1, index + 1]

            seen[number] = index
```

However, the hash-map solution uses `O(n)` extra space because the dictionary stores values.

The two-pointer solution uses only two variables, so it uses `O(1)` extra space.

| Approach | Time | Extra Space | Uses sorted order? |
|---|---:|---:|---|
| Hash map | `O(n)` | `O(n)` | No |
| Two pointers | `O(n)` | `O(1)` | Yes |

## Why Use Two Pointers Here?

The problem gives us two clues:

1. The array is already sorted.
2. The solution must use constant extra space.

Those clues strongly suggest two pointers.

The hash map is valid in general, but the two-pointer approach is better for this specific problem because it satisfies the space requirement and takes advantage of the sorted array.

## Important Pattern

When you see:

> A sorted array and a pair of values that must reach a target sum

Think:

```text
left = beginning
right = end

sum too small  → move left right
sum too large  → move right left
sum correct    → return the pair
```

## Common Mistakes

### 1. Returning zero-based indexes

The array uses normal Python indexes, but the problem asks for 1-based indexes.

```python
return [left + 1, right + 1]
```

### 2. Comparing only one number with the target

Always calculate:

```python
current_sum = numbers[left] + numbers[right]
```

### 3. Moving the wrong pointer

```python
current_sum < target  # Need a larger sum
left += 1

current_sum > target  # Need a smaller sum
right -= 1
```

### 4. Allowing the pointers to overlap

Use:

```python
while left < right:
```

This ensures that the same element is never used twice.

## Final Mental Model

The umpire does not randomly move either pointer.

- Sum too small? Move the left pointer toward larger values.
- Sum too large? Move the right pointer toward smaller values.
- Sum correct? Return the 1-based positions.

The sorted array makes every pointer movement meaningful.

