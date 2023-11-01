---
title: I Know Places (We Won't Be Found) - JKLMNOP

date: 2023-01-31
due: 2023-02-07 23:59:59

points: "+2 EC"
ec: true

number: 2
published: true
---

# Overview

Looking for to nerd out with PNGs a bit more?  This JKLMNOP is for you!


## Initial Files

In your CS 340 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp2-jklmnop --allow-unrelated-histories  -m "Merging initial files"
```


## Requirements

Complete the `png_dimensions` function in `png-dimensions.c` in the `mp2-jklmnop` folder.

The function must:

- Open and read the file `png_filename`, 
- Set the value of the `width` and `height` of the file into the provided pointers, and
- Return `0` on success and non-zero on any failure.


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
- Choose "mp2-jklmnop autograding"
- Click the green "Run Workflow" button (located on the blue bar)
- Press "Run Workflow"
- You will trigger a GitHub Action that will complete the grading run!


### Points

The JKLMNOP is worth +2 points, awarded only if all tests (`autograder` and `valgrind`) pass.