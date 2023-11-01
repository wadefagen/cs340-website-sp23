---
title: Dancing with Our C Code - JKLMNOP

date: 2023-03-24
due: 2023-03-28 23:59:59

points: "+5 EC"
ec: true

number: 7
published: true
---

# Overview

Looking for to nerd out with PNGs a bit more?  This JKLMNOP is for you!


## Initial Files

In your CS 340 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp7-jklmnop -m "Merging initial files"
```


## Requirements

For +5 extra credit points, create a Dockerfile that builds the entire MP7 service into a docker container.

Your Dockerfile must build a docker container when the `docker build -t mp7-docker -f Dockerfile ../` command in invoked in your `mp7` directory.  This means that:

- Your Dockerfile (located in `mp7`) will be run relative to **one directory higher than mp7** (your `netid` directory).  
- The is a little strange -- but this is necessary so that you can include both `mp2` and `mp7` in your container.

You can view `test_docker.py` to view this (and a few other) docker commands that we run during testing.



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
- Choose "mp7-jklmnop autograding"
- Click the green "Run Workflow" button (located on the blue bar)
- Press "Run Workflow"
- You will trigger a GitHub Action that will complete the grading run!


### Points

The JKLMNOP is worth +5 points, awarded only if all tests pass.
