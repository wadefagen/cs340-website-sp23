---
title: "1989 Mosaics - Shared Course-wide Middleware"

date: 2023-04-27
due: 2023-05-03 23:59:59

points: 10

layout: project

number: Week 3
published: true
---

<div class="alert alert-info" role="alert">
  <h3>Using v4 Middleware</h3>

  <p>
    If you are still working on Week #3, and do not want to update your code to the Week #4 requirements, you will need
    to use the <code>v4</code> (or older) middleware.  If you have a middleware higher than v4, you can get v4 by running <code>git checkout tags/v4</code> to set your repo to the v4 middleware.
  </p>
</div>


# Overview

This week, you will adapt your MMGs to the course-wide middleware ([https://github.com/cs340-illinois/1989-shared-middleware](https://github.com/cs340-illinois/1989-shared-middleware)) and the Midterm #2 exam.

Optionally, for extra credit, you will work with the class community to expand the shared middleware.  You must make code changes to the shared middleware via a [Pull Request](https://github.com/cs340-illinois/1989-shared-middleware/pulls) to the shared middleware repo.

- Not sure what's needed?  Chat in #shared-middleware on Discord or check on the [Issues](https://github.com/cs340-illinois/1989-shared-middleware/issues).


## Course-Wide Shared Middleware

From this point forward, you **will not need to use your own middleware any more**.  Instead, we will all use a single middleware so that all of our MMGs will work together for the final project to create one giant shared pixel board.

- Shared Middleware Code: [https://github.com/cs340-illinois/1989-shared-middleware](https://github.com/cs340-illinois/1989-shared-middleware)


## Requirement #1: Refactor Your MMGs to the Shared Middleware

You must refactor **ALL** of your MMGs to work with the shared middleware.  See the [README.md](https://github.com/cs340-illinois/1989-shared-middleware) for the documentation on how to add your MMGs to the middleware.

- You should run the shared middleware locally, and make sure your MMGs connect to everything on `localhost`.
- In Week #4, you will need to reconfigure your middleware to connect to a shared virtual machine.


## Requirement #2: Creating Additional Mosaic 

Create **four** additional MMGs for a total of nine MMGs.  Each should be a different theme and should be given an individual name.



## Submission

This week, you will need to:

1. In your personal repo, have all your MMG updated to work with the shared middleware.

2. A screenshot **submitted on Canvas** of the shared middleware running with the content from your nine MMGs.  This link should take you directly to the Canvas assignment to upload: [https://canvas.illinois.edu/courses/33908/assignments/702644](https://canvas.illinois.edu/courses/33908/assignments/702644)
