# Best Time to Buy and Sell Stock — Revision Notes

## Problem

Given an array `prices`, where `prices[i]` is the stock price on day `i`:

- Buy the stock on one day.
- Sell it on a different day in the future.
- Complete at most one transaction.
- Return the maximum possible profit.
- If no profitable transaction exists, return `0`.

Example:

```text
prices = [7, 1, 5, 3, 6, 4]

Buy at 1
Sell at 6
Profit = 6 - 1 = 5
```

## U — Understand

Important constraints from the problem:

1. The buying day must come before the selling day.
2. We can buy and sell only once.
3. We want the largest positive difference.
4. We return `0` when all prices decrease.

## M — Match the Pattern

This is not a hash-map problem.

The best classification is:

> Array + one pass + greedy + running minimum

It can be imagined as having a buying position and a selling position, but the cleanest solution does not need two actual pointers.

## Brute-Force Idea

For every buying day, test every future selling day:

```text
Buy on day 0 → test days 1, 2, 3, ...
Buy on day 1 → test days 2, 3, 4, ...
Buy on day 2 → test days 3, 4, 5, ...
```

This works, but it requires two nested loops.

- Time: `O(n²)`
- Space: `O(1)`

The repeated work is unnecessary. When treating today as the selling day, we only need the cheapest earlier buying price—not every earlier price.

## Optimized Intuition

Walk through the prices from left to right while remembering two values:

```text
minimum_price  = cheapest buying price seen so far
maximum_profit = best profit found so far
```

For every price, pretend today is the selling day and ask:

1. What profit would I make if I sold today?
2. Is that profit better than the best profit so far?
3. Is today's price the new cheapest buying price?

The current profit is:

```python
current_profit = price - minimum_price
```

## Correct Initialization

```python
minimum_price = prices[0]
maximum_profit = 0
```

### Why not initialize `minimum_price` to `0`?

`minimum_price` must represent a real stock price that we have encountered.

If it starts at `0`, the algorithm behaves as if we could buy the stock for free. Because prices cannot be negative, no real price would replace that `0`.

### Why initialize `maximum_profit` to `0`?

Before finding a profitable transaction, our best possible choice is to make no transaction, which produces a profit of `0`.

## Final Solution

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        minimum_price = prices[0]
        maximum_profit = 0

        for price in prices:
            current_profit = price - minimum_price

            if current_profit > maximum_profit:
                maximum_profit = current_profit

            if price < minimum_price:
                minimum_price = price

        return maximum_profit
```

## Trace

For `prices = [7, 1, 5, 3, 6, 4]`:

| Current price | Minimum price after the day | Profit considered | Maximum profit |
|---:|---:|---:|---:|
| 7 | 7 | `7 - 7 = 0` | 0 |
| 1 | 1 | `1 - 7 = -6` | 0 |
| 5 | 1 | `5 - 1 = 4` | 4 |
| 3 | 1 | `3 - 1 = 2` | 4 |
| 6 | 1 | `6 - 1 = 5` | 5 |
| 4 | 1 | `4 - 1 = 3` | 5 |

Final answer: `5`.

## Decreasing Prices

For `prices = [7, 6, 4, 3, 1]`, every possible transaction loses money.

`maximum_profit` begins at `0`, and no negative profit can replace it. Therefore, the result remains `0`.

## Why the Day Order Is Valid

We scan from left to right. Therefore:

- `minimum_price` comes from today or an earlier day.
- The current `price` represents the possible selling day.
- A future price can never become the buying price for a sale happening today.

This preserves the rule that buying must happen before selling.

## Is This a Two-Pointer Problem?

Not in its clearest implementation.

We can imagine:

- a conceptual buying position at the cheapest price seen so far;
- a conceptual selling position at the current loop position.

However, we store the buying **price**, not an actual pointer or index. The more precise name is:

> Greedy algorithm with a running minimum

## Comparison with Other Patterns

| Pattern | What it maintains | Example |
|---|---|---|
| Running minimum / greedy | Best value seen before the current position | Best Time to Buy and Sell Stock |
| Two pointers | Two indexes that move based on conditions | Container With Most Water |
| Sliding window | A meaningful contiguous range | Longest substring problems |

## Container With Most Water

This is a true two-pointer problem because we explicitly maintain two boundary indexes:

```python
left = 0
right = len(height) - 1
```

The amount of water is:

```text
water = min(left height, right height) × width
```

The shorter wall limits the water. Therefore, we move the pointer at the shorter wall inward, hoping to find a taller boundary. Moving the taller wall keeps the same limiting short wall while reducing the width, so it cannot improve the current situation.

## Recognition Rules

### Running minimum / maximum

Consider this pattern when the question asks:

> What is the best result using the current value and the best earlier value?

### Two pointers

Consider two pointers when:

> I must compare two positions, and a condition lets me eliminate or move one side.

### Sliding window

Consider a sliding window when:

> I must maintain a contiguous subarray or substring while expanding and shrinking its boundaries.

## Complexity

- Time: `O(n)` because every price is visited once.
- Space: `O(1)` because only a few variables are stored.

## Final Memory Rule

> At every day, pretend I sell today. Calculate the profit using the cheapest price I have seen before, preserve the best profit, and update the cheapest price when necessary.

