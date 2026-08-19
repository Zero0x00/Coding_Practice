# Two Sum — UMPIR Practice

## U — Understand

Given an integer array `nums` and an integer `target`, return the **indices of two different elements** whose values add up to `target`.

### Constraints

- `2 <= nums.length <= 10^4`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= target <= 10^9`
- Exactly one valid answer exists.
- You cannot use the same array element twice.

### Example

```text
nums = [2, 3, 4, 1]
target = 5

Output: [0, 1]
```

---

## M — Match

This is a **Hash Map / Dictionary Lookup** problem.

The key observation is:

```text
nums[i] + second_num = target

therefore:

second_num = target - nums[i]
```

### Brute Force

A brute-force solution compares every possible pair:

```text
Time: O(n²)
Space: O(1)
```

### Hash Map

With a dictionary, for each number we ask:

> "Have I already seen the number I need to reach the target?"

Dictionary lookup is **average O(1)**, so one pass gives us an **O(n)** solution.

---

## P — Plan

For each `nums[i]`:

1. Calculate the complement:

```python
second_num = target - nums[i]
```

2. Check whether `second_num` already exists in the dictionary.
3. If it exists, return the stored index and the current index.
4. Otherwise, store the current number and its index.

### Important Detail

We **check before inserting** the current number.

This prevents accidentally using the same element twice.

The dictionary stores:

```text
number → index
```

---

## I — Implement

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        dic = {}

        for i, num in enumerate(nums):
            second_num = target - num

            if second_num in dic:
                return [dic[second_num], i]

            dic[num] = i
```

### Original Bugs

The initial version was very close. The two main issues were:

#### 1. `print` instead of `return`

Incorrect:

```python
print([i, dic[second_num]])
```

Correct:

```python
return [dic[second_num], i]
```

LeetCode expects the function to **return** the indices.

#### 2. Incorrect variable name

Incorrect:

```python
dic[num[i]] = i
```

Correct:

```python
dic[nums[i]] = i
```

Or, with `enumerate()`:

```python
dic[num] = i
```

---

## R — Review with an Example

Consider:

```python
nums = [2, 3, 4, 1]
target = 5
```

### Step 1

```text
dic = {}
```

### i = 0

```text
number = 2

second_num = 5 - 2
second_num = 3
```

`3` is not in the dictionary.

Store:

```text
dic = {2: 0}
```

### i = 1

```text
number = 3

second_num = 5 - 3
second_num = 2
```

`2` **is** in the dictionary.

We previously stored:

```text
dic[2] = 0
```

The current index is:

```text
i = 1
```

Therefore:

```python
return [0, 1]
```

Verification:

```text
nums[0] + nums[1]
= 2 + 3
= 5
```

So the answer is correct. ✅

---

## E — Evaluate

### Time Complexity

```text
O(n)
```

We traverse the array once, and dictionary lookup is **average O(1)**.

### Space Complexity

```text
O(n)
```

In the worst case, we may store up to `n` numbers in the dictionary.

---

## Key Pattern to Remember

### Hash Map / Complement Lookup

When looking for two values that satisfy an equation such as:

```text
a + b = target
```

we can:

1. Iterate through the array.
2. Calculate the value needed to complete the equation.
3. Check whether that value has already been seen.
4. Store the current value and its index if no match is found.

This pattern is useful for many problems involving:

- Two Sum
- Pair matching
- Target sums
- Complement lookups
- Frequency/counting problems
- Duplicate detection

### Mental Model

```text
Current number
      ↓
Calculate complement
      ↓
Have I seen it before?
   ↙          ↘
 YES           NO
  ↓             ↓
Return      Store current
indices       number:index
```
