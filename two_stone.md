Last Stone Weight — UMPIRE Method

Problem Summary

You are given an array of stone weights.

On each turn:





Choose the two heaviest stones.



If they are equal, both are destroyed.



If they are different, the lighter stone is destroyed and the heavier stone becomes the difference.



Continue until at most one stone remains.



Return the remaining stone's weight, or 0 if no stones remain.



U — Understand

The key phrase is:



Repeatedly take the two heaviest elements.

That strongly suggests using a heap / priority queue.

The process repeats until fewer than two stones remain.





M — Match

Python's heapq is a min-heap.

That means:

heapq.heappop(heap)

returns the smallest value.

But this problem needs the largest values.

So we negate every stone:

stones = [-x for x in stones]

Example:

Original: [2, 7, 4, 8]

Negated:  [-2, -7, -4, -8]

Now the most negative number represents the largest original stone.

Pattern to remember

Repeatedly need smallest
→ normal heapq

Repeatedly need largest
→ negate values + heapq





P — Plan





Convert every stone weight to negative.



Turn the list into a heap using heapq.heapify().



While there are at least two stones:





Pop the heaviest stone.



Pop the second-heaviest stone.



Convert both back to positive.



If they are different:





Compute their difference.



Negate the difference.



Push it back into the heap.



When the loop ends:





If the heap is empty, return 0.



Otherwise return the remaining stone as a positive number.





I — Implement

class Solution:
    def lastStoneWeight(self, stones: List[int]) -> int:
        stones = [-x for x in stones]
        heapq.heapify(stones)

        while len(stones) > 1:
            first = -heapq.heappop(stones)
            second = -heapq.heappop(stones)

            if first != second:
                new = first - second
                heapq.heappush(stones, -new)

        if len(stones) == 0:
            return 0

        return -stones[0]





R — Review

Suppose:

stones = [2, 7, 4, 1, 8, 1]

The two heaviest stones are:

8 and 7

After popping:

8 - 7 = 1

Push 1 back.

Next:

4 and 2
→ 4 - 2 = 2

Push 2 back.

Continue until fewer than two stones remain.





Important heappop() Concept

This:

first = -heapq.heappop(stones)

does two things:

REMOVE + RETURN

So when we write:

first = -heapq.heappop(stones)
second = -heapq.heappop(stones)

both stones are already removed.

If:

first == second

we do nothing because both stones are already destroyed.

If:

first != second

we push the difference back:

heapq.heappush(stones, -(first - second))





Important heapify() Concept

This is correct:

heapq.heapify(stones)

heapify() modifies the list in place.

This is wrong:

heapstone = heapq.heapify(stones)

because heapify() returns:

None

Mental model:

heapq.heapify(stones)

means:

"Turn this existing list into a heap."





Important Heap Index Rule

Do not assume:

stones[1]

is the second-smallest or second-largest element.

A heap only guarantees:

heap[0] = minimum element

For the next priority element, use another:

heapq.heappop()

So for the two largest stones:

first = -heapq.heappop(stones)
second = -heapq.heappop(stones)





Important While Loop Concept

The loop is:

while len(stones) > 1:

Why?

Because we can only smash stones when at least two stones remain.

5 stones → continue
4 stones → continue
3 stones → continue
2 stones → continue
1 stone  → stop
0 stones → stop





E — Evaluate



Heap Construction

heapq.heapify(stones)

takes:

O(n)





While Loop

The loop can run up to roughly:

O(n)

times.

But the work inside each iteration is not O(1).

We perform heap operations:

heapq.heappop(stones)   # O(log n)
heapq.heappop(stones)   # O(log n)
heapq.heappush(...)     # O(log n), sometimes

So one iteration is:

O(log n)

The loop runs up to:

O(n)

times.

Therefore:

O(n) iterations × O(log n) work
= O(n log n)

Including heapify:

O(n) + O(n log n)
= O(n log n)



Final Complexity

Time:  O(n log n)
Space: O(n)





Interview Takeaway

When you see:



Repeatedly choose the largest or smallest element

think:

Heap / Priority Queue

When you see:



Repeatedly choose the two largest elements

think:

pop
pop
process
maybe push result back

For Python:

Need smallest → normal heapq

Need largest → negate values





Quick Memory Pattern

MAX HEAP WITH PYTHON

1. Negate
2. Heapify
3. Pop
4. Pop
5. Process
6. Push result if needed
7. Repeat

For this problem:

two heaviest
→ pop two
→ equal: do nothing
→ unequal: push difference
→ stop when heap size <= 1

