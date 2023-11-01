---
title: Midterm 1 Rubric
layout: content
---

# Rubric for Midterm 1 Grading of the Programming Question

You were awarded partial credit for having significant elements of a correct solution implemented in your solution.  As with all submission in CS 340, we always grade the **last submission you made**.

You can view your submission on PL ([https://prairielearn.com/](https://prairielearn.com/)).  If you believe there was any error in the manual grading, e-mail me (Wade) with **exactly** what parts of the rubric you believe your code should have earned points.


## +1pt - Any Code Modification that Compiles (Autograded)

Does the submission make any modification to the provided code, and does that modified code compile?  This point was provided by the autograder.


## +2pts - Correct Use of Locks and Conditional Variables

Does the submission make correct use of locks and conditional variables (or other synchronization techniques)?

Correct use requires that:

- `pthread_mutex_lock` calls are paired with `pthread_mutex_unlock` calls,
- The use of `pthread_cond_wait` is used inside of a locked region of code, **AND**
- Every conditional variable that is `wait`'d on had a corresponding `pthread_cond_signal` or `pthread_cond_broadcast`


## +3pts - Use of Meaningful Tracking Variables

Does the solution make correct use of meaningful tracking variables?

Correct use requires that a tracking variable that is capable of tracking the progress through the `"ILLINI"` string.

- This might be a `state` or `index` variable, if it's incremented after every `shout` **AND** correctly used in the various threads (ex: `state == 0 || state == 3 || state == 5` in the `I` thread).

- This might be combination of multiple variables (ex: `ct_i`, `ct_l`, and `ct_n`) where `ct_i` is incremented after `shout('I')` **AND** is it checked inside of the `L`/`N` threads.

- This might be a pointer that points to a static `"ILLINI"` string and advances after each `shout`.

Note: The existence of a tracking variable is not enough.  It must be used in a correct and meaningful way.


## +4pts - Complete Implementation (Autograded)

Does the solution have a correct and working implementation that completes the ILLINI cheer?  These 5 points are provided by the autograder and manually verified when the problem was graded.


## +2pts - Avoid Use of Busy and Sleep Waiting in a Complete Implementation

Does the solution avoid the use of busy and sleep waiting?  **ANY** use of busy-waiting or sleep-waiting will cause the solution not to earn these points, even if busy-waiting is only used on one function.

Note: These points can only be earned with a complete implementation.



