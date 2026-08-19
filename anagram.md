# Valid Anagram — UMPIRE Method

## Problem

Given two strings `s` and `t`, return `True` if `t` is an anagram of `s`, otherwise return `False`.

An **anagram** means both strings contain the same characters with the same number of occurrences, but the order can be different.

### Example

```text
s = "anagram"
t = "nagaram"

Output: True
```

---

# U — Understand

Ask:

> What exactly must be the same between the two strings?

The order does **not** matter.

The frequency of each character **does** matter.

Example:

```text
"aab" and "aba"  -> True
"aab" and "abb"  -> False
```

A `set` is not enough because a set removes duplicates:

```python
set("aab") == set("abb")
# True
```

But the strings are not anagrams because the character counts are different.

### First important check

Two anagrams must have the same length.

```python
if len(s) != len(t):
    return False
```

---

# M — Match

This is a **frequency counting / hashmap** problem.

Important clues:

```text
same characters
same number of occurrences
frequency
count
anagram
```

These should make you think:

```text
character -> frequency
```

For example:

```text
"aab"

a -> 2
b -> 1
```

### Pattern Recognition

```text
Need to know if something exists?
-> Set

Need to store information about something?
-> Dictionary / HashMap

Need to know how many times something occurs?
-> Frequency Dictionary / Counter
```

---

# P — Plan

We can solve the problem using one frequency dictionary.

### Idea

For every character in `s`:

```text
add 1
```

For every corresponding character in `t`:

```text
subtract 1
```

If the strings are anagrams, every frequency should finish at:

```text
0
```

Think of the dictionary as a **balance sheet**.

```text
s deposits      +1
t withdraws     -1
```

If everything balances back to zero, the strings are anagrams.

### Example

```text
s = "cat"
t = "tac"
```

Processing:

```text
c: +1
t: -1

a: +1
a: -1

t: +1
c: -1
```

Final result:

```text
c -> 0
a -> 0
t -> 0
```

Everything balances.

---

# I — Implement

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        count = {}

        for i in range(len(s)):
            count[s[i]] = count.get(s[i], 0) + 1
            count[t[i]] = count.get(t[i], 0) - 1

        for value in count.values():
            if value != 0:
                return False

        return True
```

---

# Understanding `.get()`

This:

```python
count[s[i]] = count.get(s[i], 0) + 1
```

means:

> Get the current frequency of this character.  
> If the character does not exist yet, start from `0`.  
> Then add `1`.

Example:

```python
count = {}
```

First time we see `"a"`:

```python
count.get("a", 0)
# 0
```

Then:

```text
0 + 1 = 1
```

Now:

```python
count = {"a": 1}
```

Second time we see `"a"`:

```python
count.get("a", 0)
# 1
```

Then:

```text
1 + 1 = 2
```

Now:

```python
count = {"a": 2}
```

### Why not this?

```python
count["a"] += 1
```

Because `"a"` may not exist yet.

That would cause:

```text
KeyError
```

Memory rule:

```text
dictionary[key] += 1
```

requires the key to already exist.

---

# Understanding the Final Loop

```python
for value in count.values():
    if value != 0:
        return False

return True
```

Suppose:

```python
count = {
    "a": 0,
    "n": 0,
    "g": 0
}
```

Then:

```python
count.values()
```

gives the frequencies:

```text
0, 0, 0
```

We check every frequency.

```text
0 -> okay
0 -> okay
0 -> okay
```

If every value is zero:

```python
return True
```

But suppose:

```python
count = {
    "a": 0,
    "b": 1,
    "c": -1
}
```

Then:

```text
0 -> okay
1 -> not zero
```

Immediately:

```python
return False
```

### Important mistake to avoid

Do not write:

```python
for value in count.values():
    return value == 0
```

That checks only the **first value** because `return` immediately ends the function.

Correct pattern:

```python
for value in count.values():
    if value != 0:
        return False

return True
```

Think:

> Find one thing that proves the answer is false.  
> If nothing proves it false, return true.

---

# Alternative: Build and Consume Frequencies

Another valid pattern is:

1. Count all characters from `s`.
2. Let `t` consume those frequencies.

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        frequency = {}

        for character in s:
            frequency[character] = frequency.get(character, 0) + 1

        for character in t:
            current_frequency = frequency.get(character, 0)

            if current_frequency <= 0:
                return False

            frequency[character] = current_frequency - 1

        return True
```

### Visualization

Think of `s` as stocking a warehouse:

```text
s = "aab"

a -> 2 boxes
b -> 1 box
```

Then `t` takes boxes.

For:

```text
t = "aba"
```

```text
take a: 2 -> 1
take b: 1 -> 0
take a: 1 -> 0
```

Everything was available.

But:

```text
s = "aab"
t = "aaa"
```

Then:

```text
take a: 2 -> 1
take a: 1 -> 0
take a: 0 -> unavailable
```

So:

```python
if current_frequency <= 0:
    return False
```

Memory pattern:

```text
Build frequency
-> Consume frequency
-> Fail if unavailable
```

---

# Counter Shortcut

Python also provides `Counter`.

```python
from collections import Counter

class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        return Counter(s) == Counter(t)
```

Example:

```python
Counter("aab")
```

acts roughly like:

```python
{
    "a": 2,
    "b": 1
}
```

This is very clean, but it is still important to understand the manual hashmap solution for interviews.

---

# R — Review

Let's test the main solution.

### Test 1

```text
s = "anagram"
t = "nagaram"
```

Same length.

Final counts:

```text
a -> 0
n -> 0
g -> 0
r -> 0
m -> 0
```

Result:

```text
True
```

### Test 2

```text
s = "rat"
t = "car"
```

Final counts include non-zero values.

Result:

```text
False
```

### Test 3

```text
s = "aab"
t = "abb"
```

Final balance:

```text
a -> +1
b -> -1
```

Result:

```text
False
```

### Test 4

```text
s = "abc"
t = "ab"
```

Lengths are different.

Immediately:

```text
False
```

---

# E — Evaluate

## Time Complexity

We visit every character once and then inspect the dictionary.

```text
O(n)
```

## Space Complexity

The dictionary stores character frequencies.

```text
O(k)
```

where `k` is the number of unique characters.

If the alphabet is fixed, such as only lowercase English letters, this can effectively be considered constant extra space.

---

# Interview Memory Hooks

## Hook 1

```text
Order does not matter.
Frequency does matter.
-> HashMap / Counter
```

## Hook 2

```text
SET = Have I seen it?

HASHMAP = What do I know about it?

FREQUENCY MAP = How many times have I seen it?
```

## Hook 3

For the one-dictionary approach:

```text
s adds      +1
t subtracts -1

Everything returns to 0?
-> Anagram
```

## Hook 4

For the consume approach:

```text
s stocks the warehouse
t consumes the inventory

Nothing missing?
-> Anagram
```

---

# Final Interview Answer

If asked to explain your approach:

> Since order does not matter but character frequency does, I will use a hashmap to track the balance of each character. I first check that the strings have equal length. Then I increment the count for each character in `s` and decrement the count for each character in `t`. If the strings are anagrams, every character count should end at zero. This gives O(n) time complexity.

---

# Final Code to Remember

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        count = {}

        for i in range(len(s)):
            count[s[i]] = count.get(s[i], 0) + 1
            count[t[i]] = count.get(t[i], 0) - 1

        for value in count.values():
            if value != 0:
                return False

        return True
```
