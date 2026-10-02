# Container With Most Water — UMPIRE Revision

[LeetCode 11: Container With Most Water](https://leetcode.com/problems/container-with-most-water/)

## U — Understand

We are given `height`, where each value is a vertical line at index `i`. Choose two lines that, together with the x-axis, hold the most water. Return the maximum area.

The container's area depends on **both** its width and its water height:

```text
area = width × water height
```

- `width` is the distance between the line indices: `right - left`.
- Water height is limited by the shorter boundary: `min(height[left], height[right])`.

So the area is **not** the difference between the two line heights. For example, heights 5 and 3 do not give a water height of `5 - 3`; the water spills over the shorter wall, so the water height is 3.

## M — Match

This is a **two-pointer** problem. Start with one pointer at each end of the array. Those endpoints give the widest possible container. Move inward while keeping track of the largest area found.

The heights do **not** need to be sorted. We do not assume that moving in either direction makes heights go up or down.

## P — Plan

1. Set `left` to the first index and `right` to the last index.
2. Start `max_area` at `0`.
3. While `left < right`:
   - Calculate the current water height using the shorter line.
   - Calculate the width using `right - left`.
   - Calculate the area and update `max_area` if this area is larger.
   - Move the pointer at the shorter line inward.
4. Return `max_area`.

### Why move the shorter line?

The shorter line limits the water height. If we move the taller line inward, the width gets smaller while the shorter boundary still limits the water. That move cannot improve the area for the current shorter boundary.

If we move the shorter line, we may find a taller boundary. It could compensate for the smaller width, so that is the move worth checking. This is why we do not move both pointers every time, and why we do not just compare adjacent lines.

When both lines have equal height, moving either pointer is valid; the code below moves `right` in that case.

## I — Implement

```python
from typing import List

class Solution:
    def maxArea(self, height: List[int]) -> int:
        left = 0
        right = len(height) - 1
        max_area = 0

        while left < right:
            current_height = min(height[left], height[right])
            width = right - left
            area = current_height * width

            if area > max_area:
                max_area = area

            if height[left] < height[right]:
                left += 1
            else:
                right -= 1

        return max_area
```

### Important order

For each pair of pointers, calculate its height, width, and area **before** moving a pointer. Otherwise, the area might combine a height from one pair with a width from another.

## R — Review with a trace

Use `height = [1, 8, 6, 2, 5, 4, 8, 3, 7]`.

| Step | `left` | `right` | Boundary heights | Water height | Width | Area | Move |
|---:|---:|---:|---|---:|---:|---:|---|
| 1 | 0 | 8 | 1, 7 | 1 | 8 | 8 | Move `left` |
| 2 | 1 | 8 | 8, 7 | 7 | 7 | 49 | Move `right` |
| 3 | 1 | 7 | 8, 3 | 3 | 6 | 18 | Move `right` |

The best area in these first three checks is 49. Keep repeating the same process until the pointers meet. The algorithm eventually finds the maximum area, 49.

### Check these common mistakes

- **Subtracting the heights:** `height[right] - height[left]` is not the water height. Use the shorter height.
- **Forgetting width:** area is not just a height; multiply by `right - left`.
- **Moving before measuring:** measure the current pair first, then move a pointer.
- **Overwriting the best area:** update `max_area` only when the new area is larger.
- **Assuming heights are sorted:** no such assumption is needed; the shorter-boundary rule drives pointer movement.

## E — Evaluate

- **Time:** `O(n)` — each pointer moves inward at most `n - 1` times.
- **Extra space:** `O(1)` — only a few variables are used.

## Remember

> Measure the current container. Save the best area. Move the shorter wall.
