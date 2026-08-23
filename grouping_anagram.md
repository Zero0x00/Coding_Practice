# Group Anagrams — Revision Notes

## Problem

Given an array of strings, group together the words that are anagrams.

```text
Input:  ["eat", "tea", "tan", "ate", "nat", "bat"]
Output: [["eat", "tea", "ate"], ["tan", "nat"], ["bat"]]
```

The order of the groups and the words inside them does not matter.

---

## UMPPIRE Method

### U — Understand

Two words are anagrams when they contain:

- The same characters
- With the same frequencies
- Possibly in a different order

For example:

```text
eat → a:1, e:1, t:1
tea → a:1, e:1, t:1
ate → a:1, e:1, t:1
```

Therefore, these words belong in the same group.

### M — Match

This problem combines two patterns:

1. **Frequency hash map:** describe each word using character frequencies.
2. **Dictionary grouping:** use the description as a key and collect matching words in a list.

This is different from checking whether only two words are anagrams. We do not compare every word with every other word. Instead, we calculate a shared identity for each word.

### P — Plan

1. Create an empty dictionary called `dic`.
2. Loop through every word.
3. Count the characters using `Counter`.
4. Turn the frequency information into a consistent, hashable signature.
5. If the signature is new, create an empty list for it.
6. Append the original word to that signature's list.
7. Return all dictionary values.

---

## What Is a Signature?

A **signature** is an anagram fingerprint. It is not a special Python keyword; it is simply a descriptive variable name.

```python
signature = tuple(sorted(Counter(word).items()))
```

All anagrams produce the same signature:

```text
eat → (("a", 1), ("e", 1), ("t", 1))
tea → (("a", 1), ("e", 1), ("t", 1))
ate → (("a", 1), ("e", 1), ("t", 1))
```

A non-anagram produces a different signature:

```text
tan → (("a", 1), ("n", 1), ("t", 1))
```

### Understanding the signature expression

```python
tuple(sorted(Counter(word).items()))
```

| Part | Purpose |
|---|---|
| `Counter(word)` | Counts each character |
| `.items()` | Produces `(character, frequency)` pairs |
| `sorted(...)` | Places the pairs in a consistent order |
| `tuple(...)` | Makes the result immutable and usable as a dictionary key |

### Why use `.items()`?

Iterating directly over a `Counter` returns only its keys and loses the frequencies:

```python
sorted(Counter("aab"))  # ["a", "b"]
sorted(Counter("abb"))  # ["a", "b"]
```

This would incorrectly give both words the same signature.

Using `.items()` preserves the frequencies:

```text
aab → (("a", 2), ("b", 1))
abb → (("a", 1), ("b", 2))
```

### Why sort?

Tuple order matters:

```python
(("a", 1), ("b", 1)) == (("b", 1), ("a", 1))
# False
```

Sorting guarantees that equivalent frequency maps produce the same tuple.

### Why convert to a tuple?

Dictionary keys must be hashable. A list and a `Counter` are mutable, so neither can be used directly as a dictionary key. A tuple is immutable and hashable when all its contents are hashable.

---

## How the Dictionary Groups Words

Dictionary keys are unique. If two words produce the same signature, they access the same dictionary entry and therefore the same list.

After processing `"eat"`:

```python
{
    (("a", 1), ("e", 1), ("t", 1)): ["eat"]
}
```

After processing `"tea"`:

```python
{
    (("a", 1), ("e", 1), ("t", 1)): ["eat", "tea"]
}
```

Python is not detecting anagrams by itself. Our signature makes anagrams produce the same dictionary key.

The reusable dictionary-grouping pattern is:

```python
if key not in dictionary:
    dictionary[key] = []

dictionary[key].append(item)
```

There is no `else` because the current item must be appended whether the group is new or already exists.

---

## Final Solution

```python
from collections import Counter
from typing import List


class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        dic = {}

        for word in strs:
            signature = tuple(sorted(Counter(word).items()))

            if signature not in dic:
                dic[signature] = []

            dic[signature].append(word)

        return list(dic.values())
```

---

## Step-by-Step Trace

Input:

```python
["eat", "tea", "tan"]
```

### Word 1: `"eat"`

```text
signature = (("a", 1), ("e", 1), ("t", 1))
```

The signature does not exist, so create a list and append `"eat"`.

### Word 2: `"tea"`

```text
signature = (("a", 1), ("e", 1), ("t", 1))
```

The signature already exists, so append `"tea"` to the existing list.

### Word 3: `"tan"`

```text
signature = (("a", 1), ("n", 1), ("t", 1))
```

This is a new signature, so create a new list and append `"tan"`.

Final dictionary values:

```python
[["eat", "tea"], ["tan"]]
```

---

## Common Mistakes

### Using a counting pattern for a grouping problem

This pattern is for numeric frequencies:

```python
frequency[key] = frequency.get(key, 0) + 1
```

In Group Anagrams, each value must be a **list of words**, not a number.

### Replacing the group instead of appending

Incorrect:

```python
dic[signature] = word
```

Correct:

```python
dic[signature].append(word)
```

### Returning the dictionary

The problem asks for only the groups, not their signatures:

```python
return list(dic.values())
```

---

## Complexity

Let:

- `n` be the number of words.
- `k` be the maximum word length.

With lowercase English letters, each `Counter` has at most 26 entries. Building each counter takes `O(k)`, while sorting at most 26 pairs is bounded by a small constant.

- **Time:** `O(n × k)` under the lowercase-English constraint
- **Space:** `O(n × k)` for the output groups and stored signatures

---

## Key Takeaway

When a problem asks you to **group items**, ask:

> What common signature can I calculate so that matching items produce the same dictionary key?

For this problem:

```text
word → frequency signature → dictionary group
```
