---
title: Alloc Too Well - JKLMNOP

date: 2023-02-16
due: 2023-02-21 23:59:59

points: "+5 EC"
ec: true

number: 3
published: true
---

# Overview

Looking for to nerd out with `malloc` a bit more?  This JKLMNOP is for you!


## Initial Files

In your CS 340 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp3-jklmnop --allow-unrelated-histories  -m "Merging initial files"
```


## Requirements

On a Linux system, the version of the `alloc` functions provided are **VERY GOOD** in the general case.  This version of code provided on Linux systems of must handle programs that make small allocations, large allocations, and everything in between!  Since it comes with the C programming library, this default malloc is commonly referred to as `libc` malloc.

One reason people make special-purpose memory allocators is to improve memory allocations for a subset of problems.  Can you beat `libc` malloc for a specific subset of code?

Copy your existing `alloc.c` into the `mp3-jklmnop` directory and optimize it to be as space efficient and past as possible on the five provided testers.  Specifically, you must:

- Use less space than `libc` malloc on all five testers, **AND**
- Among all five testers, run in less than 100% of `libc`'s time.

The provided test suite tests both of these conditions.  If you get to the timed tests, you will find that a summary is printed for you to show how your alloc preformed relative to `libc`.


## Submission and Grading

### Submission

Once you have locally passed all the tests, you will need to submit and grade your code.  First commit using the standard git commands:

```
git add -A
git commit -m "MP submission"
git push
```

### Grading

The initial grading is done via a manual GitHub Action.  You **MUST** complete this step before the deadline to earn any points for the MP:

- Navigate to your repository on [https://github.com/cs340-illinois](https://github.com/cs340-illinois).
- Click on the "Action" Tab
- Choose "mp3-jklmnop autograding"
- Click the green "Run Workflow" button (located on the blue bar)
- Press "Run Workflow"
- You will trigger a GitHub Action that will complete the grading run!


### Points

The JKLMNOP is worth +5 points, awarded only if all tests (`autograder`) are successful.