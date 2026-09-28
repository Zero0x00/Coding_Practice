# Valid Palindrome — UMPIRE Revision Notes

## U — Understand

### Problem

A phrase is a palindrome when it reads the same forward and backward **after**:

1. changing uppercase letters to lowercase, and
2. removing every non-alphanumeric character.

Alphanumeric means letters and numbers. Spaces and punctuation do not matter.

```text
"A man, a plan, a canal: Panama"
→ "amanaplanacanalpanama"
→ True
```

### Rules to Notice

- Compare characters from both ends.
- Ignore spaces, commas, colons, and other punctuation.
- `"A"` and `"a"` count as equal.
- An empty cleaned string is a palindrome.

## M — Match

### Pattern: Two Pointers

Use two pointers because we need to compare matching positions from the left and right sides.

```text
left →  "A man, a plan, a canal: Panama"  ← right
```

This is **not** a hash map problem. It is also not a sliding-window problem.

Two pointers do **not** always require a sorted array. Sorting is needed for problems such as Two Sum II, where pointer movement depends on whether a sum is too small or too large. In this problem, we simply compare characters at opposite ends.

## P — Plan

1. Put `left` at the beginning and `right` at the end.
2. While `left < right`:
   - Move `left` forward while it points to punctuation or a space.
   - Move `right` backward while it points to punctuation or a space.
   - Compare both valid characters after converting them to lowercase.
   - If they differ, return `False`.
   - If they match, move both pointers inward.
3. If every pair matches, return `True`.

### Key Condition

```python
not s[left].isalnum()
```

`s[left].isalnum()` is `True` for a letter or number. `not` reverses it, so this condition means: **the character is not a letter or number; skip it.**

| Character | `character.isalnum()` | `not character.isalnum()` |
|---|---:|---:|
| `"A"` | `True` | `False` |
| `"7"` | `True` | `False` |
| `" "` | `False` | `True` |
| `","` | `False` | `True` |

## I — Implement

### Optimal Two-Pointer Solution

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        left = 0
        right = len(s) - 1

        while left < right:

            while left < right and not s[left].isalnum():
                left += 1

            while left < right and not s[right].isalnum():
                right -= 1

            if s[left].lower() != s[right].lower():
                return False

            left += 1
            right -= 1

        return True
```

### Easier Clean-and-Reverse Solution

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        cleaned = "".join(
            character.lower()
            for character in s
            if character.isalnum()
        )

        return cleaned == cleaned[::-1]
```

Important Python details:

```python
"".join(["a", "b", "c"])   # "abc"
" ".join(["a", "b", "c"])  # "a b c"
```

Use `"".join(...)`, not `" ".join(...)`, because the problem requires spaces to be removed.

```python
cleaned[::-1]
```

reverses a string. This slicing also works with lists and tuples:

```python
"hello"[::-1]  # "olleh"
```

## R — Review

### Dry Run: `"A man, a plan, a canal: Panama"`

| Left valid character | Right valid character | Result |
|---|---|---|
| `A` | `a` | Match after `.lower()` |
| `m` | `m` | Match |
| `a` | `a` | Match |
| `n` | `n` | Match |
| ... | ... | All pairs match → `True` |

### Common Mistakes

- Removing only spaces instead of all non-alphanumeric characters.
- Forgetting `.lower()` before comparing letters.
- Using `if` to skip one punctuation mark instead of `while` to skip several in a row.
- Using `" ".join(...)`, which adds spaces between every character.
- Thinking `[::-1]` works only on lists; it works on strings too.

### Memory Trick

> Two guards walk inward from opposite ends. They ignore noise (spaces and punctuation) and compare only meaningful characters.

## E — Evaluate

| Approach | Time | Extra Space | Why |
|---|---:|---:|---|
| Clean and reverse | `O(n)` | `O(n)` | Creates `cleaned` and a reversed copy. `O(n) + O(n) = O(2n) = O(n)`. |
| Two pointers | `O(n)` | `O(1)` | Each pointer crosses the string once; no cleaned copy is created. |

### Interview Takeaway

The clean-and-reverse approach is great for first understanding the problem. In an interview, mention it first, then improve it to the two-pointer approach to reduce extra space from `O(n)` to `O(1)`.
