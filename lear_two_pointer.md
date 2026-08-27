# Two-Pointer Pattern for Sorted Arrays

## 1. Core Idea

The two-pointer pattern uses two positions that move through an array.

For pair-sum problems on a **sorted array**:

- `left` begins at the smallest value.
- `right` begins at the largest value.
- We compare their sum with the target.
- The comparison tells us which pointer to move.

The goal is not to memorize pointer movements. The goal is to understand why each movement safely eliminates an impossible choice.

---

## 2. Mental Model: A Sorted Bookshelf

Imagine books arranged from shortest to tallest.

```text
shortest                              tallest
   left  →                        ←  right
```

If the two selected heights add to less than the target, we need a larger total. Moving `right` leftward would select a shorter book and make the total even smaller. Therefore, we move `left` rightward.

If the total is greater than the target, we need a smaller total. Moving `left` rightward would select a taller book and make the total even larger. Therefore, we move `right` leftward.

The sorted order gives pointer movement a predictable effect.

---

## 3. Movement Rules

```text
current sum < target  → move left rightward
current sum > target  → move right leftward
current sum = target  → pair found
```

In Python:

```python
if current_sum < target:
    left += 1
elif current_sum > target:
    right -= 1
else:
    return left, right
```

### Why these rules work

- When the sum is too small, we must replace the smaller value with a larger value.
- When the sum is too large, we must replace the larger value with a smaller value.
- Every move discards one position that cannot produce the target with the remaining candidates.

---

## 4. Step-by-Step Trace

```text
numbers = [2, 5, 8, 12]
target = 13
```

### Step 1

```text
2 + 12 = 14
```

The sum is too large, so move `right` leftward.

### Step 2

```text
2 + 8 = 10
```

The sum is too small, so move `left` rightward.

### Step 3

```text
5 + 8 = 13
```

The target is found.

---

## 5. Complete Python Template

```python
def find_pair(numbers, target):
    left = 0
    right = len(numbers) - 1

    while left < right:
        current_sum = numbers[left] + numbers[right]

        if current_sum < target:
            left += 1
        elif current_sum > target:
            right -= 1
        else:
            return left, right

    return None
```

This function returns the indexes of the matching values. It returns `None` when no pair exists.

---

## 6. Why `left < right`?

We need two different array positions.

```python
while left < right:
```

If `left == right`, both pointers refer to the same element. That would reuse one position twice.

This prevents reuse of the **same index**, not the use of equal values. For example, the two `2`s below occupy different positions and may form a valid pair:

```text
[2, 2, 5]
```

---

## 7. Why the Array Must Be Sorted

For this opposite-end pattern, sorting creates direction:

- Moving `left` rightward guarantees that its value will stay the same or increase.
- Moving `right` leftward guarantees that its value will stay the same or decrease.

In an unsorted array, moving a pointer may produce either a larger or smaller value. Therefore, the comparison does not tell us which choice can be safely discarded.

Important: not every two-pointer problem requires sorting. Sorting is essential for this specific **compare-and-move from opposite ends** strategy.

---

## 8. Complexity

### Time: \(O(n)\)

There are two pointers, but there are not two nested loops.

- `left` only moves right.
- `right` only moves left.
- Neither pointer moves backward.
- Together, they can move across the array only a linear number of times.

### Extra space: \(O(1)\)

Only a few variables are stored: `left`, `right`, and `current_sum`.

If an unsorted array must be sorted first, sorting usually changes the total time to \(O(n \log n)\).

---

## 9. Common Mistakes

### Mistake 1: Moving the wrong pointer

```text
sum too small → move left
sum too large → move right
```

Ask: **Do I need the sum to become larger or smaller?**

### Mistake 2: Using `left <= right`

That allows both pointers to select the same position. Use `left < right` when two distinct elements are required.

### Mistake 3: Applying the movement rules to an unsorted array

Without sorted order, a pointer move has no guaranteed effect.

### Mistake 4: Confusing indexes with values

```python
left = 0                       # index
numbers[left]                  # value at that index
```

### Mistake 5: Forgetting the failure return

If the pointers meet or cross without finding the target, return a failure value such as `None`, `False`, or `[-1, -1]`, depending on the problem.

---

## 10. Recognition Checklist

Consider this pattern when:

1. The input is sorted, or sorting is allowed.
2. The problem compares a pair of values or positions.
3. One pointer move predictably increases something.
4. The other pointer move predictably decreases something.
5. Each comparison lets you eliminate an impossible candidate.

Common examples include:

- finding two values with a target sum;
- checking whether a sorted array contains a valid pair;
- finding a pair closest to a target;
- comparing values from opposite ends;
- shrinking a search interval using ordered information.

---

## 11. The Invariant to Remember

An **invariant** is a fact that remains true throughout the loop.

Here, the possible answer—if one still exists—must be somewhere between `left` and `right`.

Each comparison proves that one boundary cannot participate in a valid answer, so we move that boundary inward without losing a possible solution.

> Two pointers are powerful not because there are two variables, but because each comparison lets us safely eliminate part of the search space.

---

## 12. Quick Self-Test

Given:

```text
numbers = [1, 3, 4, 7, 9]
target = 11
```

Trace the pointers:

```text
1 + 9 = 10  → ____________________
3 + 9 = 12  → ____________________
3 + 7 = 10  → ____________________
4 + 7 = 11  → ____________________
```

Then answer:

1. Why does the smaller sum cause `left` to move?
2. Why does the larger sum cause `right` to move?
3. Why is the loop linear rather than quadratic?
4. Why do we stop when `left` is no longer less than `right`?

---

## Final Summary

```text
Start at both ends of a sorted array.

Too small → move left inward.
Too large → move right inward.
Equal     → found.

Stop when the pointers meet or cross.
```

Do not memorize only the code. Remember the reasoning:

> Sorted order tells us how a pointer move changes the comparison, allowing us to discard impossible choices safely.
