# First Unique Character in a String — UMPIR Revision Notes

## Problem

Given a string `s`, return the **index of the first non-repeating character**.

If every character repeats, return `-1`.

### Example 1

```text
Input:  s = "leetcode"
Output: 0
```

`l` appears only once and is the first unique character.

### Example 2

```text
Input:  s = "loveleetcode"
Output: 2
```

### Example 3

```text
Input:  s = "aabb"
Output: -1
```

---

# UMPIR Method

## U — Understand

We need to:

- Find the **first character** that appears exactly once.
- Return its **index**.
- Return `-1` if no unique character exists.

Important detail:

> We care about both **frequency** and **original order/index**.

---

## M — Match

This is a:

- Hash map / frequency counting problem
- `Counter` is a good fit because it gives the frequency of every character.

Pattern:

```text
character -> frequency
```

For example:

```python
s = "leetcode"
```

The frequencies are approximately:

```python
{
    'l': 1,
    'e': 3,
    't': 1,
    'c': 1,
    'o': 1,
    'd': 1
}
```

But there is one important thing to remember:

> `Counter` stores frequency, not the original index.

So after counting frequencies, we still need to iterate over the original string.

---

## P — Plan

1. Count the frequency of every character using `Counter`.
2. Iterate through the original string using `enumerate()`.
3. For each character:
   - Check whether its frequency is `1`.
4. Return the first index where the frequency is `1`.
5. If the loop finishes without finding one, return `-1`.

Pseudocode:

```text
count frequencies

for each index and character in original string:
    if frequency of character == 1:
        return index

return -1
```

---

## I — Implement

```python
from collections import Counter

class Solution:
    def firstUniqChar(self, s: str) -> int:

        count = Counter(s)

        for index, character in enumerate(s):
            if count[character] == 1:
                return index

        return -1
```

---

# Dry Run

Consider:

```python
s = "leetcode"
```

First:

```python
count = Counter(s)
```

We get:

```text
l -> 1
e -> 3
t -> 1
c -> 1
o -> 1
d -> 1
```

Now:

```python
for index, character in enumerate(s):
```

### First iteration

```text
index = 0
character = 'l'
```

Check:

```python
count['l'] == 1
```

This is `True`.

So:

```python
return 0
```

Answer:

```text
0
```

---

# My Original Mistake

I originally tried something like:

```python
count = Counter(s)

for key, value in count.items():

    if value == 1:
        return count[value]
    else:
        return -1
```

There are two major problems here.

---

## Mistake 1 — `count[value]` Does Not Give the Index

Inside:

```python
for key, value in count.items():
```

For example:

```text
key   = 'l'
value = 1
```

Then:

```python
count[value]
```

becomes:

```python
count[1]
```

But the keys inside the Counter are characters:

```python
count['l']
count['e']
count['t']
```

The Counter structure is:

```text
character -> frequency
```

It is NOT:

```text
index -> character
```

or:

```text
frequency -> index
```

### Key lesson

> `Counter` knows how many times something appears, but it does not know where it originally appeared.

If I need the index, I should iterate through the original string with:

```python
enumerate(s)
```

---

## Mistake 2 — Returning `-1` Too Early

Suppose:

```python
s = "aab"
```

Frequency:

```text
a -> 2
b -> 1
```

If I write:

```python
for key, value in count.items():
    if value == 1:
        return ...
    else:
        return -1
```

The first character might be:

```text
a -> 2
```

Since `2 != 1`, the code immediately returns:

```python
-1
```

But `b` is still unique!

The loop stopped before checking everything.

### Correct structure

```python
for index, character in enumerate(s):
    if count[character] == 1:
        return index

return -1
```

The `return -1` belongs **after the loop**.

---

# Important Interview Pattern

When searching for something:

```python
for item in collection:
    if answer_found:
        return answer

return failure_value
```

Meaning:

```text
Search all possible candidates.

If you find the answer:
    return immediately.

If the entire search finishes:
    return failure value.
```

For this problem:

```python
for index, character in enumerate(s):
    if count[character] == 1:
        return index

return -1
```

---

# Why Use `enumerate()`?

Instead of:

```python
for character in s:
```

we use:

```python
for index, character in enumerate(s):
```

because the problem asks us to return the **index**.

Example:

```python
s = "abc"
```

`enumerate(s)` produces:

```text
0, 'a'
1, 'b'
2, 'c'
```

So we get both:

```text
index + character
```

at the same time.

---

# Complexity

## Time Complexity

Creating the Counter:

```python
Counter(s)
```

takes:

```text
O(n)
```

Iterating through the string:

```python
for index, character in enumerate(s):
```

takes:

```text
O(n)
```

Total:

```text
O(n) + O(n) = O(n)
```

So:

**Time Complexity: `O(n)`**

---

## Space Complexity

The Counter stores character frequencies.

In the general case:

**Space Complexity: `O(n)`**

If the character set is fixed, such as lowercase English letters only, it could also be described as `O(1)` because there are at most 26 possible keys.

---

# Pattern to Memorize

A very useful interview pattern:

```python
frequency = Counter(data)

for index, item in enumerate(data):
    if frequency[item] == desired_frequency:
        return index

return failure_value
```

For this problem:

```python
count = Counter(s)

for index, character in enumerate(s):
    if count[character] == 1:
        return index

return -1
```

---

# R — Review

## What I Should Remember

1. `Counter` gives me:

```text
item -> frequency
```

2. `Counter` does **not** give me the original index.

3. If I need both the value and its position, use:

```python
enumerate()
```

4. Do not return failure from inside the loop unless I am certain no later element can be the answer.

Wrong:

```python
for item in items:
    if condition:
        return answer
    else:
        return -1
```

Better:

```python
for item in items:
    if condition:
        return answer

return -1
```

5. This problem uses two passes:

```text
Pass 1 -> calculate frequency
Pass 2 -> find the first character with frequency 1
```

Both passes are linear, so the total is still:

```text
O(n)
```

---

# Interview Trigger

When I see words like:

- first unique
- first non-repeating
- frequency
- appears once
- duplicate count
- occurrence count

I should think:

```text
Hash Map / Counter
```

And if the problem asks for the **first** one or an **index**, I should think:

```text
Count frequencies first
+
iterate through original input in order
```

---

# Final Solution

```python
from collections import Counter

class Solution:
    def firstUniqChar(self, s: str) -> int:

        count = Counter(s)

        for index, character in enumerate(s):
            if count[character] == 1:
                return index

        return -1
```

---

## One-Line Memory Trick

> **Counter tells me HOW MANY; `enumerate()` tells me WHERE.**
