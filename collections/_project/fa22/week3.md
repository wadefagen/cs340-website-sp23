---
title: "Project Week #3 - Shared Middleware and Collaboration"

date: 2022-11-29
due: 2022-12-06 23:59:59

points: 10

layout: project

number: Week 3
published: false
---

# Overview

This week, you will adapt your PGs to the course-wide middleware ([https://github.com/cs340-illinois/pixel-board-shared-middleware](https://github.com/cs340-illinois/pixel-board-shared-middleware)) and the Midterm #2 exam.

Optionally, for extra credit, you will work with the class community to expand the shared middleware.  You must make code changes to the shared middleware via a [Pull Requqest](https://github.com/cs340-illinois/pixel-board-shared-middleware/pulls) to the shared middleware repo.

- Not sure what's needed?  Chat in #project on Discord or check on the [Issues](https://github.com/cs340-illinois/pixel-board-shared-middleware/issues).


## Course-Wide Shared Middleware

From this point forward, you **will not need to use your own middleware any more**.  Instead, we will all use a single middleware so that all of our PGs will work together for the final project to create one giant shared pixel board.

- Shared Middleware Code: [https://github.com/cs340-illinois/pixel-board-shared-middleware](https://github.com/cs340-illinois/pixel-board-shared-middleware)


## Requirement #1: Refactor Your PGs to the Shared Middleware

You must refactor **ALL** of your PGs to work with the shared middleware.  See the [README.md](https://github.com/cs340-illinois/pixel-board-shared-middleware) for the documentation on how to add your PGs to the middleware.

- You should run the shared middleware locally, and make sure your PGs connect to everything on `localhost`.
- In Week #4, you will need to reconfigure your middleware to connect to a virtual machine.


## Requirement #2: Group Formation

In the project showcase, there will be a three different pixel boards your PGs to contribute pixels to.  However, in each pixel board, you'll only be allowed to run single PG.  This means you may only be able to contribute a few pixels a minute by yourself (depending on the final `PIXEL_RATE`).

You will run three PGs, connected to the following three pixel boards:

1. One PG will connect to your **private pixel board**, that only contains your PG for us to see what your PG creates!
2. A second PG that will connect to **course-wide SOLO pixel board**.  This is one pixel board with 100+ PGs each working towards their own goals.  This PG is your own PG.
3. A third PG that will connect to a **course-wide TEAM pixel board**.  This is one pixel board with ~20-40 groups of PGs.  Here, each PG should work in collaboration with your team to best execute your strategy.


For your team PG, you can work as a group of anywhere from **two to up to five people**.

- You must agree on a team name and then <a href="https://forms.gle/7UJv7wj6Pv7Watrm8" target="_blank">everyone on your team must submit this form to have a repo created for your team</a>.
- We'll e-mail you with the repo and you must submit your team PG into that repo.  Everyone will have access to the repo.


## Submission

This week, you will need to:

1. In your personal repo, have all your PGs updated to work with the shared middleware.

2. Be a member of a team (have you full team submit the Google Form).  You are not required to have your team PG completed by Week #3 deadline, but you will be required to have a PG in your team repo by final exam/project showcase.