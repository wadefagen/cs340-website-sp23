---
title: Project Part I - Design and Middleware Implementation

date: 2022-04-08
due: 2022-04-18 23:59:59

points: 30

layout: project

number: Week 1
published: false
---

<style>
.maze {
  font-weight: bold;
  font-size: 120%;
  line-height: 80%;
  height: 80%;
  background: -webkit-linear-gradient(#13294B, #FF552E, #13294B, #FF552E);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  font-family: Montserrat;
}
</style>

# Overview

The final project in CS 240 is all about creating something as a course, together, that all of our work contributes to the final deliverable and runs simultaneously at our final exam meeting time.  Unlike the MPs, there is no "solution" to this project -- at this point, the deliverable does not exist and we are building it together.

Over several lectures, we discussed many ideas for this project.  This semester, we're going to build an <b>Infinite Maze</b>.

- The infinite maze is <b>one giant maze</b> that grows forever.
- When you reach an "exit", a new segment is generated to grow the maze.
- You can view a [proof of concept right here](../maze-poc.html).


<div class="alert alert-info" role="alert">
  <h3>Optional Team-Based Project!</h3>
  <p>
    This part of the project can be done <b>with a team of up to 3 total people</b> (you and up to two others).
    The requirements of this project will scale based on the size of your team.  In this document,
    <b><i>p</i></b> describes the number of members in your team (if you're solo, p=1; a group of two
    has p=2, etc).
  </p>
  <p>
    If you are working in a team, <b>your submission must be made in a team repo instead of your own personal
    repo</b>.  You must agree on a team name and then <a href="https://forms.gle/Cp8a1UrNaaX9NEDE9" target="_blank">everyone on your team must submit this form to have a repo created
    for your team.</a>
  </p>
</div>

## Software Architecture

There are three major components to our infinite maze:

- A <b>frontend</b> (basic proof of concept right now, but we can develop it out later),
- A <b>middleware</b> the interfaces between the frontend and backend (designed programmed in Part I),
- Many <b>backend microservices</b> that generate maze pieces (called "MazeGens" or MGs) and any other services

During the first two weeks of the project, you will develop your own private infinite maze by designing your own middleware and backend services.  As part of preparing for Week 3, we will choose a single course-wide middleware that will integrate with the MGs from the entire course.

Feel free to discuss high-level details of your maze server design between groups and as a class to come up with the best possible design.  The infinite maze will eventually be comprised of code developed by every single person in this class.


## Part I: Design and Middleware Implementation

To develop our infinite maze, you will design the system specification and middleware implementation within a group of **1-3 students**.


### Technical Requirement: Maze Geometry

The only technical requirement is that the request will be communicated to your middleware via a `GET` route to `/generateSegment`.  This route returns a JSON that must contain `geom` that contains the geometry of the maze segment.  The geometry is a array of strings, where each string is one row of the maze and each character is a single cell in the maze.  For example:

- `{"geom": ["92c", "4a1", "386"]}` is the geometry of a 3x3 maze.

Each character is a hex digit that encodes the walls of that cell.

- The most significant bit is `1` if there's a wall on the north side of the cell,
- The 2nd most significant bit is `1` if there's a wall on the east side of the cell,
- The 3rd most significant bit is `1` if there's a wall on the south side of the cell, and
- The least significant bit is `1` if there's a wall on the west side of the cell

This geometry is up for design as well, but the frontend will fail to function if this is not followed.


### Functional Requirement

The provided code always returns the same maze segment.  However, it's important we have **A LOT** of different maze layouts created by a lot of different people.

- Your middleware must communicate to backend MGs that generate the maze,
- Your middleware must be able to connect to many MGs that may come and go over time, and
- Your middleware must support additional features that could be added to enhance the maze


### Initial Files

In your CS 240 directory (if you are solo) or in your team directory (if you are in a team), merge the initial starting files with the following commands:

```
git fetch release
git merge release/maze-week1 -m "Merging initial files"
```


## Deliverables

For Part I, you must deliver:

1. A technical specification describing your design in `API.md`.  This must include:

    * An high-level overview of your implementation of the infinite maze is designed (including middleware and MGs),
    * Technical details on the request and response APIs between your middleware and the MGs,
    * Technical details on any changes to the API between the frontend and the middleware,
    * Technical details on how MGs are added and removed from the middleware,
    * Technical details on any dependencies needed to run your infinite maze (ex: python commands to run, other services, docker, etc), and
    * Any additional details of the ability for your design to incorporate the Week #2 challenges (see Week #2 preview below).
    * This file must be in Markdown (`md`) format and display on github.com's web browser interface in a readable way. 


2. An implementation of your middleware in the `app.py` file provided that works with the provided frontend,


3. An implementation of the provided 7x7 maze square as a static MG that works with your middleware, and

    * This must be implemented as a MG (using a `requests` library call) and not just the `return` value in the provided code,


4. At least ***p*** additional MGs that includes at least one MG by each team member:

    * Each MG must be implemented within a septate directory within the project folder.

    * The MGs this week can be static, but you're encouraged to be creative with them!


5. A file called `MG.md` that briefly describe the MGs in the various folders.  (This should be in the root project directory along with `API.md`.)

    * This file must be in Markdown (`md`) format and display on github.com's web browser interface in a readable way. 

    * *Edit: Previously this had a different name, and either name is fine.*


### Grading

The 30 project points will be assigned for completing all deliverables.  (This whole "week" is partial credit for the project as a whole, but there is no partial credit within this week.)


### Course-Wide Design Extra Credit

During Week #2, the course staff will review all of the designs you created for Week #1 (mostly the `API.md`).  We'll discuss several of the best designs in lecture and, as a class, choose one as the shared middleware for everyone to use.  The designs used for the course-wide middleware will earn EC. :)


### Part 2 Preview

For Part II (next week) update, you will have to add additional features to the infintie maze.  Some possible features may include: 

- How do you support multiple simultaneous users in the single infinite maze?  (Right now, each user has their own maze but we one big, shared maze.)
- How do we make exploring the maze more enjoyable?  (Should there be color?  Images?  Possibly only appearing once you complete a specific area of a block?)
- How do we add history to the maze?  (Should users leave little trails behind -- like footprints -- of where they have gone?)
- How do we support block sizes that are not square?  (Right now, there's an assumption that everything is 7x7.  We can do better.)

You may will to ensure your design this week will be able to scale into Week 2's challenges.


## Submission

When you have completed your program, double-check that your server runs as expected by the specifications above.  **If you are working on a team, make sure this is in your team repo.**  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "submission"
git push
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com).

