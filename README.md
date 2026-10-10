# Binary Search Interview Questions: Spot the Variant, Fix the Boundary Bugs, and Drill the Right 20 Problems

Almost nobody gets asked to write plain binary search anymore. Ask a working engineer to code `while (left <= right)` and they'll do it in ninety seconds. Then the interviewer says "the array is rotated at an unknown pivot," or "find the minimum speed at which Koko finishes her bananas," and the room gets quiet.

That gap — between knowing the loop and knowing which problem needs it — is what binary search interview questions are actually designed to expose. This article covers the three families these questions fall into, the boundary mistakes that sink otherwise-strong candidates, the specific problems worth drilling, and how to practice them without grinding for six weeks.

## Why interviewers keep asking binary search

The algorithm is simple enough that an interviewer can evaluate you on everything else around it. Published prep guides describe the same set of signals: whether you check that the data is actually monotonic before reaching for the pattern, whether you can define the invariant your loop maintains, whether you manage boundaries and off-by-one cases without flailing, and whether you can explain why the complexity is what it is.

That last part matters more than candidates expect. A LeetCode community write-up on binary search puts it bluntly: binary search isn't about arrays, it's about monotonicity. If you can answer "as `x` increases, does some condition flip from false to true exactly once?" then binary search is legal. If you can't answer that, the honest move is to say so and pick a different approach.

## The three families of binary search interview questions

Nearly every version you'll see lands in one of these buckets.

| Family | What the interviewer is checking | How it shows up |
| --- | --- | --- |
| Exact match on sorted data | That you can write the loop correctly | Search a sorted array; search insert position |
| Boundaries and structural variants | Precision under pressure | First/last occurrence, lower/upper bound, rotated array, peak element, 2D matrix |
| Search on the answer space | Whether you can see a monotonic predicate that isn't an array | Koko eating bananas, ship packages in D days, split array largest sum |
| Exponential / unbounded search | Awareness of what binary search needs | Target in a stream or an array of unknown length |

The first family is the warm-up, and everyone knows it. The second is where interviews get decided. The third is where they get won.

### Family 2: boundaries and variants

These problems look like standard binary search and then remove one of your assumptions.

**Rotated sorted arrays.** `[4, 5, 6, 7, 0, 1, 2]` is sorted, rotated at an unknown pivot. The observation that makes it solvable: at any midpoint, one of the two halves is still properly sorted. Find that half, decide whether the target can live inside it, and discard the rest. The trap is the comparison operators — is it `a[left] <= a[mid]` or `<`? Get that wrong and you have an off-by-one bug that only shows up on arrays rotated by exactly one element, which is precisely the test case you didn't write.

With duplicates allowed, the problem gets worse in an honest way. When `a[left] == a[mid] == a[right]`, you genuinely cannot tell which half is sorted, so you shrink both ends by one and move on. Worst case becomes O(n). Anyone who claims O(log n) for that variant is wrong, and an interviewer who knows the problem will notice.

**First and last occurrence.** The rule that separates people who can do this from people who can't: never return immediately on a match. On a hit, record the index and keep searching — left for the first occurrence, right for the last. Candidates who try to hack it with a boolean flag usually produce something that works on the examples and breaks on `[2, 2]`.

**Lower bound vs. upper bound.** Lower bound is the first index where `arr[i] >= target`. Upper bound is the first index where `arr[i] > target`. In Python, `bisect_left` and `bisect_right` give you exactly those, and the count of a value is `bisect_right - bisect_left`. Getting the subtraction backwards is a classic silent failure — the code returns a plausible number, just the wrong one.

**Peak element.** The array isn't sorted at all, yet binary search works, because the property "there's a peak somewhere to my right" is decidable from a single comparison: if `nums[mid] < nums[mid + 1]`, the peak is to the right. This problem is often the first one where candidates realize binary search doesn't require sortedness — only a monotonic decision rule.

### Family 3: search on the answer

This is the family that shows up most in real loops at large companies, and it's the one people under-practice.

The setup: you aren't searching an array. You're searching the range of all possible answers. Koko has piles `[3, 6, 7, 11]` and 8 hours; what's the minimum eating speed? The speed is monotonic — too slow doesn't work, fast enough works, and every speed above that also works. So the predicate looks like `false, false, false, true, true, true`, and binary search finds the boundary.

The trigger phrases are worth memorizing as a set:

> "Minimize the maximum…" · "Maximize the minimum…" · "Find the smallest rate/speed/capacity such that…" · "Least number of days to…"

And the template, which is short enough to write from memory:

1. Define the search range `[lo, hi]` for the answer, not the input.
2. Write a feasibility function `is_feasible(mid)` that returns a boolean.
3. Confirm monotonicity: if `is_feasible(x)` is true, is `is_feasible(x + 1)` also true?
4. Binary search for the transition: on `true`, save `ans = mid` and move `hi = mid - 1`; on `false`, move `lo = mid + 1`.

A mistake that comes up constantly here: sorting the input array "to make binary search work." In capacity-to-ship-packages, sorting destroys the ordering the problem depends on. You never needed a sorted array — you needed a monotonic predicate.

### The boundary details that decide outcomes

Two details that cost real offers:

`mid = (left + right) / 2` overflows in Java and C++ when both indices are large enough. `mid = left + (right - left) / 2` doesn't. In Python it's a non-issue because integers are arbitrary precision, which is worth saying out loud if you're coding in Python — it shows you know why the idiom exists rather than just copying it.

Second: `while (left <= right)` versus `while (left < right)` isn't a style choice. The `<=` form is used when both pointers can land on the same element and you still need to inspect it. The `<` form is the standard shape for boundary searches where you converge on a single index. Mixing the loop condition from one template with the pointer updates from the other produces infinite loops, and infinite loops are the single most common way a binary search answer fails.

One invariant to state before you write anything: every iteration, the search space must strictly shrink.

## The 20 problems worth drilling

Published question roundups overlap heavily — a widely shared LeetCode discuss list of 20 binary search problems and Educative's breakdown of the 10 most common ones cover most of the same ground. The combined set, grouped by family so you practice the pattern and not just the problem:

**Baseline (make these automatic)**

1. Binary Search — 704
2. Search Insert Position — 35
3. Sqrt(x) — 69
4. Valid Perfect Square — 367

**Boundaries and variants**

5. Find First and Last Position of Element in Sorted Array — 34
6. Search in Rotated Sorted Array — 33
7. Find Minimum in Rotated Sorted Array — 153
8. Search a 2D Matrix II — 240
9. Find Peak Element — 162
10. Single Element in a Sorted Array — 540
11. Find K Closest Elements — 658
12. Kth Smallest Element in a Sorted Matrix — 378

**Search on answer**

13. Koko Eating Bananas — 875
14. Capacity To Ship Packages Within D Days — 1011
15. Split Array Largest Sum — 410
16. Minimum Number of Days to Make m Bouquets — 1482
17. Minimum Limit of Balls in a Bag — 1760
18. Magnetic Force Between Two Balls — 1552
19. Find the Duplicate Number — 287

**Stretch**

20. Median of Two Sorted Arrays — 4

If you only have a week, do 5 through 18. If you have two days, do 5, 6, 13, and 14 — they cover boundary searches, structural variants, and answer-space search, which is most of what gets asked.

## Practicing the pattern instead of the problems

The reason binary search questions feel unfair is that the tutorial version and the interview version are different skills. Watching a loop get written doesn't teach you to recognize `nums[mid] < nums[mid + 1]` as a peak-detection opportunity in the middle of a 45-minute conversation.

This is the specific gap AlgoMonster was built around. It's a pattern-based course rather than a problem bank: the curriculum is organized around explicit, named patterns, and binary search is taught as a family — vanilla search, the monotonic/boundary template (`first_true_index`), and binary search on the answer space — rather than as one algorithm with footnotes.

A few things it does that matter for this particular topic:

- **A written template per pattern, in six languages.** Python, Java, JavaScript, C++, Go, and Haskell. The templates are the deliverable; the problems are practice material.
- **Decision flowcharts.** "Is the input sorted? Are you minimizing a maximum? Is there a monotonic predicate?" — a sequence of questions that routes you to the pattern. This is the recognition skill that binary search questions test.
- **Speedrun mode.** Timed sets of mixed problems, which is closer to what an actual screen feels like than solving one medium at your own pace.
- **Company-specific ladders** for Google, Amazon, and Meta, plus the curated Blind 75 and Monster 50 lists.

The format is text and diagrams — roughly 700 of them — with no video at all. That's a deliberate design choice, not an oversight, and it's the first thing to check against your own learning style. If you retain information from reading and diagram-tracing, it's efficient. If you need someone talking over a whiteboard, it will feel dry.

👉 [See how AlgoMonster teaches the binary search pattern](https://algo.monster?fpr=li69)

## What it does not cover

Worth knowing before you buy, because these are the things people discover a week before an onsite.

- **Question volume is low.** The platform's content sits in the low hundreds of problems, against LeetCode's 3,000+. Pattern recognition from 200 well-chosen problems is the bet; if your bottleneck is raw speed, you'll still want LeetCode alongside it.
- **No video walkthroughs.** None.
- **System design and behavioral coverage are introductory.** Fine for a phone screen, not for a senior loop. Budget separately for those rounds.
- **No mock interviews or mentorship.** Self-paced and self-directed. No live human to poke at your reasoning.

There's also a reasonable philosophical objection: template-based learning can slide into template memorization. If you internalize `is_feasible(mid)` without understanding why the predicate has to be monotonic, an interviewer's follow-up — "what if the input arrived as a stream instead of in memory?" — will end the conversation regardless of whether your code passed.

## Plans and pricing

AlgoMonster has no monthly option. There's a free tier, an annual plan, and a one-time lifetime purchase.

| Plan | What you get | Price | Billing | Get it |
| --- | --- | --- | --- | --- |
| Free | Limited access to a subset of lessons and problems | $0 | Free account | [Start with the free tier](https://algo.monster?fpr=li69) |
| Pro – Yearly | Full access to the pattern curriculum, interviews from top tech companies, 12 months of access | $99/year (list $198) | Billed annually | [Get Pro Yearly](https://algo.monster?fpr=li69) |
| Pro – Lifetime | Everything in Yearly plus lifetime updates | $189 (list $378) | One-time | [Get Pro Lifetime](https://algo.monster?fpr=li69) |

Two notes on the numbers. First, the sale pricing on the subscribe page moves — the same plans have been listed at roughly $99/$189 during current promotions, and the MSRP shown is $198/$378. Check the page rather than trusting any number in an article, including this one. Second, there's an "Add promotion code" field at checkout. A partner code that circulates in interview-prep communities (SCHELLEY) has been reported to stack an extra 10% on top of the active sale, bringing yearly to about $89 and lifetime to about $170. It costs nothing to type it in and try.

**Which one to pick:** if you have a loop in the next two or three months and that's your only prep window, yearly is the rational choice. If you interview every couple of years — or you'll want to re-read the boundary templates before your next job change — lifetime pays for itself after roughly two years of annual renewals.

## How to talk through a binary search question out loud

A weak answer starts with "I'll use binary search." A strong one sounds like this:

> "The input is sorted, and each comparison lets me discard half the remaining search space, so binary search gives me logarithmic time."

And for an answer-space problem:

> "The solution space is monotonic — if this capacity works, any larger capacity also works. So I can binary search on the answer instead of the array."

That second sentence is the one that signals you understand the pattern rather than recognizing the problem. Interviewers follow up on it, too: what's the search range, what does `true` mean, which side do I discard, what happens to complexity if the input is streamed. Have answers ready.

## A short checklist before your next screen

- Confirm the monotonic property exists. If it doesn't, binary search is the wrong tool and saying so is a point in your favor.
- Write the mid formula in the overflow-safe form.
- State which template you're using — exact-match or boundary-search — because the loop condition follows from it.
- Never return on the first match when the question says "first" or "last."
- Verify the search space strictly shrinks each iteration.
- Name the complexity, including the `O(n log(range))` shape of answer-space problems and the O(n) worst case for rotated arrays with duplicates.
- Run one edge case mentally: empty input, single element, and the value at either end.

Binary search questions aren't testing whether you memorized an algorithm. They're testing whether you can hold a precise invariant in your head while someone interrupts you. That's a skill you can drill — but only by solving the variants, not the original.

👉 [Drill the binary search pattern on AlgoMonster](https://algo.monster?fpr=li69)
